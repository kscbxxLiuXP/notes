# Maven配置(macOS)

## 下载Maven

打开**maven官网**：[https://maven.apache.org](https://maven.apache.org/)，选择下载Binary版本的包

![image-20231128200455910](https://www.liuxinpeng.cn/img/202311282004961.png)

可以在用户目录下新建一个文件夹`Environment`，将`apache-maven-3.9.5`解压到这个地方

![image-20231128200917008](https://www.liuxinpeng.cn/img/202311282009059.png)

## 配置maven

### 配置环境变量

修改`.zshrc`，在配置文件最后一行添加下面的内容

```
#maven
export MAVEN_HOME=/Users/liuxinpeng/Environment/apache-maven-3.9.5
export PATH=$MAVEN_HOME/bin:$PATH
```

输入`source .zshrc`后再次执行下面的命令：

```
mvn -v
```

出现下面的内容说明配置成功

![image-20231128201328052](https://www.liuxinpeng.cn/img/202311282013092.png)

### 配置镜像

`Maven`自身下载包非常慢，我们需要配置**阿里云**镜像，加快导包的速度

在`Maven`安装文件夹下，找到`settings.xml`并打开

![image-20231128201627672](https://www.liuxinpeng.cn/img/202311282016699.png)

根据阿里云的[配置指南](https://developer.aliyun.com/mvn/guide)，在`<mirrors>`下添加如下内容，并把默认的镜像源注释掉

```
<mirror>
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

![image-20231128202028159](https://www.liuxinpeng.cn/img/202311282020187.png)

## IDE配置

在`Environment`目录下新建一个`maven-repository`作为本地仓库

![image-20231128202245276](/Users/liuxinpeng/Library/Application Support/typora-user-images/image-20231128202245276.png)

在IDEA中打开设置，设置如下

![image-20231128202501552](https://www.liuxinpeng.cn/img/202311282025584.png)

| Option | Description |
| ------ | :---------- |
| `data`   | path to data files to supply the data that will be passed into templates. |
| engine | `engine to be used for processing templates. Handlebars is the default.` |
| ext    | extension to be used for dest files.<br /><br >`asd` |