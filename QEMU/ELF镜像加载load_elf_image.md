# QEMU 镜像加载

## 简介

本文介绍`qemu`如何加载elf镜像，从main函数中loader_exec函数开始加载二进制镜像

然后调用`load_elf_binary`解析`elf`的各个段，加载ELF，elf字符串，elf解释器(动态链接器)，创建elf表

## ELF



## QEMU中加载ELF

![image-20231218110808516](https://www.liuxinpeng.cn/img/202312181108677.png)

### loader_exec

    ret = loader_exec(filename, target_argv, target_environ, regs,info,&bprm);
     
    filename：要加载的ELF文件的名称
    target_argv：qemu运行的参数，在这里即hello（hello是生成的可执行文件名， $qemu hello）
    target_environ：执行qemu的shell的环境变量
    regs,info,bprm是ELF文件加载过程中涉及的三个重要数据结构，下面会详细分析。

loader_exec函数的功能及含义参见代码注释。

    int loader_exec(const char* filename, char** argv, char** envp,
                 struct target_pt_regs * regs, struct image_info*infop,
                 struct linux_binprm *bprm)
    {
        int retval;
        int i;
        bprm->p= TARGET_PAGE_SIZE*MAX_ARG_PAGES-sizeof(unsignedint);    /*MAX_ARG_PAGES= 33*/
        memset(bprm->page, 0, sizeof(bprm->page));
        retval = open(filename, O_RDONLY);                                /*返回打开文件的fd*/
        if (retval< 0)
            return retval;
        bprm->fd= retval;
        bprm->filename= (char *)filename;
        bprm->argc= count(argv);
        bprm->argv= argv;
        bprm->envc= count(envp);
        bprm->envp= envp;
        /*1. 要加载文件的属性判断：是否常规文件，是否可执行文件，是否ELF文件； 2. 读取ELF文件的前1024个字节*/
        retval = prepare_binprm(bprm);
        if(retval>=0){            /*prepare_binrpm函数已经读出了目标文件的前1024个字节，先判断下这个文件是否是ELF文件，即前4个字节*/
            if (bprm->buf[0]== 0x7f
                    && bprm->buf[1]== 'E'
                    && bprm->buf[2]== 'L'
                    && bprm->buf[3]== 'F'){
                retval = load_elf_binary(bprm, regs, infop);
    #if defined(TARGET_HAS_BFLT)
            } elseif (bprm->buf[0]== 'b'
                    && bprm->buf[1]== 'F'
                    && bprm->buf[2]== 'L'
                    && bprm->buf[3]== 'T'){
                retval = load_flt_binary(bprm,regs,infop);
    #endif
            } else{
                fprintf(stderr,"Unknown binary format\n");
                return -1;
            }
        }
        if(retval>=0){
            /* success. Initialize important registers*/
            do_init_thread(regs, infop);
            return retval;
        }
        /* Something went wrong, return the inodeand free the argument pages*/
        for (i=0; i<MAX_ARG_PAGES; i++){
            g_free(bprm->page[i]);
        }
        return(retval);
    }
     

 ### load_elf_binary

```c
 int load_elf_binary(struct linux_binprm* bprm, struct target_pt_regs* regs,
                        struct image_info * info)
    {
        struct image_info interp_info;
        struct elfhdr elf_ex;
        char *elf_interpreter = NULL;
        info->start_mmap= (abi_ulong)ELF_START_MMAP;        /*ELF_START_MMAP= 0x80000000*/
        info->mmap= 0;
        info->rss= 0;
        /*主要工作就是初始化info，申请进程虚拟地址空间，将ELF文件映射到这段虚拟地址空间上*/
        load_elf_image(bprm->filename, bprm->fd, info,
                       &elf_interpreter, bprm->buf);
 
     ... ... ... ...
 
      
        return 0;
    }
```

### load_elf_image

```c
static void load_elf_image(const char*image_name,int image_fd,
                               struct image_info *info, char**pinterp_name,
                               char bprm_buf[BPRM_BUF_SIZE])
    {
        struct elfhdr *ehdr = (struct elfhdr *)bprm_buf;
        struct elf_phdr *phdr;
        abi_ulong load_addr, load_bias, loaddr, hiaddr,error;
        int i, retval;
        const char *errmsg;
        /* First of all, some simple consistency checks*/
        errmsg = "Invalid ELF image for this architecture";
        if (!elf_check_ident(ehdr)){/*ELF头检查*/
            goto exit_errmsg;
        }
        bswap_ehdr(ehdr);    /*当前为空，是不是主机和目标机大小尾端不一致时才会swap*/
        if (!elf_check_ehdr(ehdr)){
            goto exit_errmsg;
        }
        /*下面的代码即读出ELF文件的程序头表，首先判断下是否已经被完全读出*/
        i = ehdr->e_phnum* sizeof(struct elf_phdr);    /*program header 表的大小*/
        if (ehdr->e_phoff+ i <= BPRM_BUF_SIZE){
            phdr = (struct elf_phdr *)(bprm_buf+ ehdr->e_phoff);
        } else{
            phdr = (struct elf_phdr *) alloca(i);    /*申请i个程序头部*/
            retval = pread(image_fd, phdr, i, ehdr->e_phoff);    /*从文件image_id的偏移为ehdr->e_phoff处读取i个字节到phdr中，即phdr存放program header*/
            if (retval!= i){
                goto exit_read;
            }
        }
        bswap_phdr(phdr, ehdr->e_phnum);
    #ifdef CONFIG_USE_FDPIC
        info->nsegs= 0;
        info->pt_dynamic_addr= 0;
    #endif
        /* Find the maximum size of the imageand allocate an appropriate
           amount of memory to handle that.*/
        loaddr = -1, hiaddr= 0;
        for (i= 0; i < ehdr->e_phnum;++i){/*遍历每一个program header*/
            if (phdr[i].p_type== PT_LOAD){
                abi_ulong a = phdr[i].p_vaddr;
                if (a < loaddr){        /*loaddr= -1而且是unsigned 类型的，所以loaddr是个很大的数*/
                    loaddr = a;            /*loaddr记录segment的起始地址*/
                }
                a += phdr[i].p_memsz;    /*这个segment在内存中的偏移地址*/
                if (a > hiaddr){        /*hiaddr记录segment的结束地址*/
                    hiaddr = a;
                }
    #ifdef CONFIG_USE_FDPIC
                ++info->nsegs;
    #endif
            }
        }
        load_addr = loaddr;        /*计算出来的需要加载的起始地址*/
        if (ehdr->e_type== ET_DYN){    /*共享目标文件(.so)*/
            /* The image indicates that it can be loaded anywhere. Find a
               location that can hold the memoryspace required.If the
               image is pre-linked, LOADDR will be non-zero. Since wedo
               not supply MAP_FIXED here we'll use that addressif and
               only if it remains available.*/
            load_addr = target_mmap(loaddr, hiaddr- loaddr, PROT_NONE,
                                    MAP_PRIVATE | MAP_ANON| MAP_NORESERVE,
                                    -1, 0);
            if (load_addr== -1) {
                goto exit_perror;
            }
        } elseif (pinterp_name!= NULL) {
            /* Thisis the main executable. Make sure that the low
               address does not conflict with MMAP_MIN_ADDRor the
               QEMU application itself. */
            probe_guest_base(image_name, loaddr, hiaddr);
        }
        load_bias = load_addr - loaddr;
    #ifdef CONFIG_USE_FDPIC
        {
            struct elf32_fdpic_loadseg *loadsegs= info->loadsegs=
                g_malloc(sizeof(*loadsegs)* info->nsegs);
            for (i= 0; i < ehdr->e_phnum;++i){
                switch (phdr[i].p_type){
                case PT_DYNAMIC:
                    info->pt_dynamic_addr= phdr[i].p_vaddr+ load_bias;
                    break;
                case PT_LOAD:
                    loadsegs->addr= phdr[i].p_vaddr+ load_bias;
                    loadsegs->p_vaddr= phdr[i].p_vaddr;
                    loadsegs->p_memsz= phdr[i].p_memsz;
                    ++loadsegs;
                    break;
                }
            }
        }
    #endif
        info->load_bias= load_bias;    /*真实的加载地址和计算出来(读ELF头信息)的加载地址之差*/
        info->load_addr= load_addr;    /*真实的加载地址*/
        info->entry= ehdr->e_entry+ load_bias;    /*重新调整下程序的入口*/
        info->start_code= -1;
        info->end_code= 0;
        info->start_data= -1;
        info->end_data= 0;
        info->brk= 0;
        for (i= 0; i < ehdr->e_phnum; i++){
            struct elf_phdr *eppnt = phdr + i;
            if (eppnt->p_type== PT_LOAD){
                abi_ulong vaddr, vaddr_po, vaddr_ps, vaddr_ef, vaddr_em;
                int elf_prot = 0;
                /*记录PT_LOAD类型segment的权限：读/写/可执行*/
                if (eppnt->p_flags& PF_R) elf_prot= PROT_READ;
                if (eppnt->p_flags& PF_W) elf_prot|= PROT_WRITE;
                if (eppnt->p_flags& PF_X) elf_prot|= PROT_EXEC;
                vaddr = load_bias + eppnt->p_vaddr;
                vaddr_po = TARGET_ELF_PAGEOFFSET(vaddr);/*((vaddr)& ((1<< 12)-1))，目的是取页内偏移*/
                vaddr_ps = TARGET_ELF_PAGESTART(vaddr);    /*((vaddr)& ~(unsigned long)((1<< 12)-1))，向下页对齐，目的取页对齐的地址*/
                /*将ELF文件映射到进程地址空间中*/
                error= target_mmap(vaddr_ps, eppnt->p_filesz+ vaddr_po,    /*映射的时候从页内偏移vaddr_po开始映射，即保持原来的偏移量*/
                                    elf_prot, MAP_PRIVATE| MAP_FIXED,
                                    image_fd, eppnt->p_offset- vaddr_po);
                if (error ==-1) {
                    goto exit_perror;
                }
                vaddr_ef = vaddr + eppnt->p_filesz;
                vaddr_em = vaddr + eppnt->p_memsz;
                /*If the load segment requests extra zeros (e.g. bss), map it.*/
                if (vaddr_ef < vaddr_em){
                    zero_bss(vaddr_ef, vaddr_em, elf_prot);
                }
                /* Find the full program boundaries.*/
                if (elf_prot & PROT_EXEC){
                    if (vaddr < info->start_code){
                        info->start_code= vaddr;    /*代码段的起始虚拟地址（页对齐的地址）*/
                    }
                    if (vaddr_ef > info->end_code){
                        info->end_code= vaddr_ef;    /*代码段的结束虚拟地址（页对齐的地址）*/
                    }
                }
                if (elf_prot & PROT_WRITE){
                    if (vaddr < info->start_data){
                        info->start_data= vaddr;    /*数据段的起始虚拟地址*/
                    }
                    if (vaddr_ef > info->end_data){
                        info->end_data= vaddr_ef;    /*数据段的起始虚拟地址（包括bss段的大小）*/
                    }
                    if (vaddr_em > info->brk){
                        info->brk= vaddr_em;    /*程序内存映像的顶端（代码段+数据段+bss段）*/
                    }
                }
            } elseif (eppnt->p_type== PT_INTERP&& pinterp_name){/*内部解释程序名称：/lib/ld-linux.so.2*/
                char *interp_name;
                if (*pinterp_name){
                    errmsg = "Multiple PT_INTERP entries";
                    goto exit_errmsg;
                }
                interp_name = malloc(eppnt->p_filesz);
                if (!interp_name){
                    goto exit_perror;
                }
                if (eppnt->p_offset+ eppnt->p_filesz<= BPRM_BUF_SIZE){
                    memcpy(interp_name, bprm_buf+ eppnt->p_offset,
                           eppnt->p_filesz);
                } else {
                    retval = pread(image_fd, interp_name, eppnt->p_filesz,
                                   eppnt->p_offset);
                    if (retval != eppnt->p_filesz){
                        goto exit_perror;
                    }
                }
                if (interp_name[eppnt->p_filesz- 1] != 0){
                    errmsg = "Invalid PT_INTERP entry";
                    goto exit_errmsg;
                }
                *pinterp_name = interp_name;
            }
        }
        if (info->end_data== 0){
            info->start_data= info->end_code;
            info->end_data= info->end_code;
            info->brk= info->end_code;
        }
        if (qemu_log_enabled()){
            load_symbols(ehdr, image_fd, load_bias);
        }
        close(image_fd);
        return;
     exit_read:
        if (retval>= 0){
            errmsg = "Incomplete read of file header";
            goto exit_errmsg;
        }
     exit_perror:
        errmsg = strerror(errno);
     exit_errmsg:
        fprintf(stderr,"%s: %s\n", image_name, errmsg);
        exit(-1);
    }
```

