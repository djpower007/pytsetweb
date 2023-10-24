
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
#自定义的action引用
#使用tags
 - uses: actions/javascript-action@v1.0.1
#多个版本，使用sha来引用
steps:
  - uses: actions/javascript-action@a824008085750b8e136effc585c3cd6082bd575f
#使用分支
steps:
  - uses: actions/javascript-action@main

#输入和输出
name: "Example"
description: "Receives file and generates output"
inputs:
  file-path: # id of input
    description: "Path to test script"
    required: true
    default: "test-file.js"
outputs:
  results-file: # id of output
    description: "Path to results file"

#自定义环境变量
定义   POSTGRES_HOST  POSTGRES_PORT两个变量，用在执行的脚本中
jobs:
  example-job:
      steps:
        - name: Connect to PostgreSQL
          run: node client.js
          env:
            POSTGRES_HOST: postgres
            POSTGRES_PORT: 5432

#给job设置default任务
给shell设置一个默认的文件夹
defaults:
  run:
    shell: bash
    working-directory: ./scripts

#给一个job1设置默认的环境
jobs:
  job1:
    runs-on: ubuntu-latest
    defaults:
      run:
        shell: bash
        working-directory: ./scripts

jobs:
  example-job:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./scripts
    steps:
      - name: Check out the repository to the runner
        uses: actions/checkout@v3  
      - name: Run a script
        run: ./my-script.sh
      - name: Run another script
        run: ./my-other-script.sh
#执行两个脚本
jobs:
  example-job:
    runs-on: ubuntu-latest
    defaults:
      run:
        working-directory: ./scripts
    steps:
      - name: Check out the repository to the runner
        uses: actions/checkout@v3  
      - name: Make the script files executable
        run: chmod +x my-script.sh my-other-script.sh
      - name: Run the scripts
        run: |
          ./my-script.sh
          ./my-other-script.sh


#注意参数为下划线 _ 比如：workflow_dispatch: 

-uses 使用版本
-run 为执行

myStringInBraces: ${{ 'It''s open source!' }}
 echo "$myStringInBraces"   
转义后It's open source!

可选触发模式 on: [push,pull_request]
在runs-on后面就可加判断  if: ${{ github.event_name == 'pull_request' }}

在最后细节处定义的env变量，会覆盖大局的。
- run: echo 'Hi ${{ env.mascot }}'  # Hi Mona
      - run: echo 'Hi ${{ env.mascot }}'  # Hi Octocat
        env:
          mascot: Octocat
引用上下文变量
jobs.一个job的名称.分支项.分支变量

一个job的步骤里的变量在其他步骤引用，调用。
第一个方法，在steps同步，给一个outputs,下写入一个output1的输出，在另一个job中引用，如：
输出：    outputs:
        randomjob: ${{ strategy.job-index }}
引入：    with:
          name: test-job-${{ needs.strategy_context.outputs.randomjob }}.log
第二个方法，运行的环境变量，对GitHub Actions 使用命令 set-env ，这将是环境变量被注入到以后的步骤
echo "{environment_variable_name}={value}" >> "$GITHUB_ENV"
steps:
  # 这将会在第一个 `run` 脚本中设置 `FOO` 环境变量。
  # 然后指示 GitHub Actions 在随后的运行步骤中使其可用。
  - run: |
      export FOO=bar
      echo "::set-env name=FOO::$FOO"
  - run: echo $FOO

NEED 变量调用
 outputs:
  build_id: ${{ steps.build_step.outputs.build_id }} 
    steps:
      - uses: actions/checkout@v4
      - name: Build
        id: build_step
        run: |
          ./build
          echo "build_id=$BUILD_ID" >> $GITHUB_OUTPUT  # build_id 到 outputs 输出
下一个job调用
   - run: ./deploy --build ${{ needs.build.outputs.build_id }}
   
runner内部的环境变量，需要根据runner的主机，写变量的引用，比如ubuntu是$var  powershell是env:$var：
on:
  workflow_dispatch:
env:
  day_of_week: Monday 
jobs:
    display-variables:
      env:
        Greeting: Hello
      steps:
        - name: Use variables
          env:
           First_name: Chris

变量取名
Names can only contain alphanumeric characters ([a-z], [A-Z], [0-9]) or underscores (_). Spaces are not allowed.
Names must not start with the GITHUB_ prefix.
Names must not start with a number.
Names are case insensitive.
Names must be unique at the level they are created at.

在yml里var大小写不敏感，但是运行环境里，大小写敏感

#push和merge时触发
on: [push, merge]
#with为环境设置参数
   with:
     node-version: '14'
 - name: install bats
   run: npm install -g bats
 - run: bats-V
#run 需要一个 “- ”分支，要么用name一个“- name”，要么自己“- run”

on: [push, fork] 同时发生才能triggering

on:
  push:
    paths:
      - 'sub-project/**'
      - '!sub-project/docs/**'
