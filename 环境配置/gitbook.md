# gitbook

## 简介

如何将gitbook配置在gitlab上以及在github上

## 项目结构

需要一个`SUMMARY.md`来生成文档的目录结构
建议参考下面的目录结构

gitbook
\|-.github
\|---workflows
\|------build.yml
\|-doc
\|---1.md
\|---2.md
\|-SUMMARY.nd
\|-.gitlab-ci.yml

## github

### 1. Personal access token

#### A. 申请 Personal access token

在右上角 `Github 头像 -> Settings -> Developer settings -> Personal access tokens -> Generate new token`

勾选 `repo:status public_repo`

> 注意: 要保存好刚产生出来 `token`

#### B. 将 `Personal access token` 写入至 `repositories` 的 `Secrets`

在你的 `repositories Settings -> Secrets -> New repository secret`

将上一步骤的 token 写入, 并命名 `GH_ACCESS_TOKEN`

### 2. 建立 `GitHub Actions YAML`

新增 `GitHub Actions `的脚本在此路径:`.github/workflows/build.yml`

此脚本功能是当你 `push code` 至 `master branch` 时, 会执行 `jobs` 的指令, 藉由 `Gitbook CLI` 将档案 `build` 到 `gh-pages` 此 `branch` 的` _book` 资料夹‌

需修改你想要的 `branches`, `MYEMAIL`

```yaml
name: Build my gitbook and deploy to gh-pages
on:
  workflow_dispatch:
  push:
    branches:
      - master

jobs:
  master-to-gh-pages:
    runs-on: ubuntu-latest

    steps:
      - name: checkout master
        uses: actions/checkout@v2
        with:
          ref: master

      - name: install nodejs
        uses: actions/setup-node@v1

      - name: configue gitbook
        run: |
          npm install -g gitbook-cli          
          gitbook install
          npm install -g gitbook-summary
      - name: generate _book folder
        run: |
          gitbook build
      - name: push _book to branch gh-pages
        env:
          TOKEN: ${{ secrets.GH_ACCESS_TOKEN }}
          REF: github.com/${{github.repository}}
          MYEMAIL: kscbxxLiuXP@126.com
          MYNAME: ${{github.repository_owner}}
        run: |
          cd _book
          git config --global user.email "${MYEMAIL}"
          git config --global user.name "${MYNAME}"
          git init
          git remote add origin https://${REF}
          git add . 
          git commit -m "Updated By Github Actions With Build ${{github.run_number}} of ${{github.workflow}} For Github Pages"
          git branch -M master
          git push --force --quiet "https://${TOKEN}@${REF}" master:gh-pages
```

### 3. GitHub Pages 设定

在你的 ｀repositories Settings->Pages｀

Source 的 Branch 选 ｀gh-pages｀

### 4. 测试脚本
在你的 ｀repositories Actions -> Workfows -> Run workflow｀

他就开始跑 yml 脚本, 可以点进去看他执行的 log