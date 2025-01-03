# TGForwarder

tgsearch、tgsou需要配置一堆频道群组，完全可以跑个定时任务监控这些频道群组，把网盘、磁力资源全都转发到自己的频道，这样只需要配置一个就可以  
效果参考：https://t.me/s/tgsearchers  

### 信息获取
在线获取TG session(选择V1)： https://tg.uu8.pro/  

api_id和api_hash获取：https://my.telegram.org/  

github上公开的
```
api_id = 2934000
api_hash = "7407f1e353d48363c48df4c8b3904acb"

api_id = '27335138'
pi_hash = '2459555ba95421148c682e2dc3031bb6'

api_id = 6627460
api_hash = '27a53a0965e486a2bc1b1fcde473b1c4'
```

### 功能：
- 支持带关键词的纯文本、图文、视频消息转发到自己频道，可以自定义搜索关键词和禁止关键词
- 对于禁止转发的消息可以下载图片以主动发送的方式发布到自己频道
- 支持根据链接和视频大小去重，已经存在的资源不再转发（默认只针对最近500条消息去重）
- 支持尝试加入频道群组(无法过验证)
- 支持转发消息评论中的视频、资源链接
- 参数only_send默认只主动发送，不转发，可以降低被限制风险
- 支持阿里云、夸克、115网盘链接有效性检测
- 支持对不同的频道/群组，可以自定义监听最近消息数量，默认取limit值，配置方式 channels_groups_monitor = ['Quark_Movies|20', 'Aliyun_4K_Movies|5']
- 支持自定义替换消息中的关键字，如标签、频道/群组等
- 支持根据关键字匹配(可根据网盘类型、资源类型tag)，自动分发到不同频道/群组，支持设置独立的包含/排除关键词。如果不需要分发，设置channel_match=[]
- 默认过滤掉今年之前的资源，pass_years=True则允许转发老年份资源
- 每次转发后自动统计今日转发数量并置顶，发送前会删除之前发出的统计消息
- 支持消息中文字超链接为网盘链接的场景，自动还原为url，暂不支持多个文字超链接替换场景
- 支持消息中文字超链接跳转机器人发送`/start`获取资源链接，自动还原为url，暂不支持多个文字超链接场景



### 代理参数说明:
- SOCKS5  
proxy = (socks.SOCKS5,proxy_address,proxy_port,proxy_username,proxy_password)
- HTTP  
proxy = (socks.HTTP,proxy_address,proxy_port,proxy_username,proxy_password))
- HTTP_PROXY  
proxy=(socks.HTTP,http_proxy_list[1][2:],int(http_proxy_list[2]),proxy_username,proxy_password)


## github action
```
name: TGForwarder # 工作流程的名称
 
on: # 什么时候触发
  schedule:
    - cron: '1 7-23 * * *'  # 定时触发
  push:
    paths-ignore:
      - '**' # 忽略文件变更, **忽略所有
 
jobs: # 执行的工作
  run_demo_actions:
    runs-on: ubuntu-latest # 在最新版本的 Ubuntu 操作系统环境下运行
    steps: # 要执行的步骤
      - name: Checkout code
        uses: actions/checkout@v4  # 用于将github代码仓库的代码拷贝到工作目录中
        with:
          # 转到 Settings > Secrets and variables > Actions
          # 点击 New repository secret，添加 Secret，名称为 BOT，输入你的token
          token: ${{ secrets.BOT }}
 
      - name: Set up Python
        uses: actions/setup-python@v2 # 用于设置 Python 环境，它允许你指定要在工作环境中使用的 Python 版本
        with:
          python-version: '3.10.10'  # 选择要用的Python版本

      - name: Install requirements.txt
        run: | # 安装依赖包
          pip install -r ./requirements.txt 
 
      - name: Run TGForwarder.py
        run: python TGForwarder.py # 执行py文件

      - name: Commit and push history.json file
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add history.json
          git commit -m "Update history.json file" || echo "No changes to commit"
          git push https://${{ secrets.BOT }}:x-oauth-basic@github.com/${GITHUB_REPOSITORY}.git
```
