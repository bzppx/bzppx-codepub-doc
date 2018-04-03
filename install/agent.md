# Agent 部署

> agent 即为发布节点，一个  agent 为一个发布节点，每台机器上可以部署多个 agent ,一个 agent 也可以执行多个发布任务
> 注意: linux 下所有的操作最好都以 root 用户执行

## 准备好安装目录
```
# 创建目录, 名字可以随意起
$ mkdir codepub-agent
$ cd codepub-agent
```

## 下载最新发布的版本

> 请参照你自己的平台下载对应的安装包
地址: https://github.com/bzppx/bzppx-agent-codepub/releases

以 linux amd64 为例，下载 v0.8 版本（注意，请下载最新版本）
```
$ wget https://github.com/bzppx/bzppx-agent-codepub/releases/download/v0.8/bzppx-agent-codepub-linux-amd64.tar.gz
# 解压到当前目录
$ tar -zxvf bzppx-agent-codepub-linux-amd64.tar.gz
```

## 在 codepub 后台添加一个节点

进入 codepub 后台，点击 节点管理 -> 添加节点

![add_node](./images/add_node.png)

- 节点IP填写你现在要部署的机器的外网 ip，如果和 codepub 是在一个局域网，可以写内网 ip
- 端口填写该 agent 要监听的 tcp 端口
- 节点组根据你的需要选择，没有先添加一个节点组
- 备注

![node](./images/node_list.png)

## 配置 agent

最好复制一份默认的配置文件 config.toml

```
$ cp config.toml agent.toml
```

打开配置文件
```
[access]
token = "53d86cc809090ee047cf343fd787888e" # 请查看 codepub 后台节点列表, 上面添加节点后生成的 token

[rpc]
listen = ":9091" # 请查看 codepub 后台节点列表, 上面添加节点 port

[cert]
key_file = "cert/server.key" # 不需要动
crt_file = "cert/server.pem" # 不需要动

[log]
level = ["info","error","debug"]
dir = "log"
FileMaxSize = 102400000
MaxCount = 3
```


## 启动 agent 

```
$ ./bzppx-agent-codepub --conf agent.toml
```

## 后台运行

```
$ ./bzppx-agent-codepub --conf agent.toml > /tmp/debug.log 2>&1 &
```