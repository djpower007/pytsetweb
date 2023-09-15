
![Alt text](overview-actions-simple.webp)

name: learn-github-actions                                #可选 ，仓库文件名
run-name: ${{ github.actor }} is learning GitHub Actions  #Action中显示的名称
on: [push]                                                #出发action的动作
jobs:                                                     #所有的任务在这里运行
  check-bats-version:                                     #定义job的name，
    runs-on: ubuntu-latest                                #配置一个新的ubuntu
    steps:                                                #将job中运行的所有步骤组合在一起。嵌套的每个项都是一个单独的操作或shell脚本。
      - uses: actions/checkout@v3                         #使用V3的action版本去执行
      - uses: actions/setup-node@v3                       #安装nodejs14版本
        with:
          node-version: '14'
      - run: npm install -g bats                          #在ubuntu的runner中运行脚本
      - run: bats -v                                      #在ubuntu的runner中运行脚本

#从市场中搜索自己需要的action的步骤
https://github.com/marketplace?type=actions

#在同一个仓库中引用action
   - uses: ./.github/actions/hello-world-action
#在不同仓库中引用action
 格式{owner}/{repo}@{ref}  
       - name: My first step
        uses: actions/setup-node@v3    actions里setup-node的V3版本
#引用docker的公开版本
 格式docker://{image}:{tag}
       - name: My first step
        uses: docker://alpine:3.8
自定义的action引用
#使用tags
 - uses: actions/javascript-action@v1.0.1
#多个版本，使用sha来引用
steps:
  - uses: actions/javascript-action@a824008085750b8e136effc585c3cd6082bd575f
#使用分支
steps:
  - uses: actions/javascript-action@main

