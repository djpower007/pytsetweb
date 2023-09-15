
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
