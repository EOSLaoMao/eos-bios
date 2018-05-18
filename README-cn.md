基于 EOS.IO 的区块链网络启动工具
-------------------------------

`eos-bios` 是一个用于启动 EOS 区块链网络的命令行工具


该工具提供了下面几个功能：

* 用于主网上线的多阶段（Multi-staged）发布功能
* 启动本地开发环境
* 启动测试网络
* 启动私有网络


在使用该工具之前，你需要了解 **discovery 协议**。关于协议的介绍，你可以观看下面这个视频：

[![The Disco Dance](https://i.ytimg.com/vi/8aNZ_ZnKS-A/hqdefault.jpg?sqp=-oaymwEZCNACELwBSFXyq4qpAwsIARUAAIhCGAFwAQ==&rs=AOn4CLCZeGSGv9Qix8mHX77R4-d0rzDkgA)](https://youtu.be/8aNZ_ZnKS-A)

https://youtu.be/8aNZ_ZnKS-A

如果你已经熟悉该工具的用法，可以直接跳到 [sample 目录](./sample_config) 。


本地开发环境
-----------------------------

git clone 该工具的代码，进入 `sample_config` 目录拷贝，[下载最新的 `eos-bios` 可执行文件](https://github.com/eoscanada/eos-bios/releases) 并解压到该目录。

将目录内的 `my_discovery_file.yaml` 文件的下列配置改为:

```
target_http_address: http://localhost:8888
```

然后运行：

    ./eos-bios boot --single


至此，本地节点启动完成。这个节点提供了一个全功能的开发环境，其功能和即将于 6 月份上线的主网基本一致。

上述配置只会启动一个节点，不会和其他的 BP 节点组成网络（通过 `peers` 参数可以进行配置）。


Orchestrate a community launch
------------------------------

社区多节点协作，共同启动网络
----------------------

当社区内的成员达成一致，想要共同启动一个 EOS 网络的时候，成员只需运行如下的命令：

    ./eos-bios orchestrate

根据社区成员提供的 discovery 文件数据和特定算法，各个节点将会被自动赋予相应的角色：

1.  _BIOS Boot 节点_，该节点将执行 `eos-bios boot` 命令。
2. _指定的 BP 节点（Appointed Block Producer）_，该类节点将执行 `eos-bios join --verify` 命令。
3.  _其他节点_，该类节点将执行 `eos-bios join` 命令，加入上述两类节点发起的新网络

当使用 `orchestrate` 命令共同启动网络的时候，新网络的启动要等到旧网络（也叫 seed network，种子网络）的区块高度达到 discovery 文件配置中的 `seed_network_launch_block` 才会自动开始。


### 如何加入主网启动网络

你可以让已经在最新的种子网络（seed network）中的任意节点邀请你加入网络。邀请新节点的命令如下：


    ./eos-bios invite [你的账户名] [你的公钥]

上述命令会帮你在种子网络中创建账户。接下来，更新你的 `my_discovery_file.yaml` 文件:


* `seed_network_account_name`, 该配置改为你的账户名。
* `seed_network_http_address`, 改配置改为你要加入的种子网络。

同时，将你上述公钥对应的私钥，更新到 `privkey.keys` 文件中。 紧接着，你可以执行 `publish` 命令：

    ./eos-bios publish

下面是 `my_discovery_file.yaml` 文件中需要更新的一些其他配置:

* `target_http_address` 是新网络中你的节点的 http api 地址
* `target_p2p_address` 是新网络中你的节点的 p2p 地址
* `target_appointed_block_producer_signing_key` 和 `target_initial_authority`: 这些配置将被用于在新的网络中创建通过 `target_account_name` 定义的账户.
* `seed_network_peers` 该配置[在这里有详细介绍](#可信网络节点).

其他值得一提的配置：

* `seed_network_launch_block` 当种子网络的区块高度达到该值的时候，`orchestrate` 命令将被允许执行（当该值小于当前区块高度时，意味着 `orchestrate` 命令可以立即执行）。
* `target_contents` 指的是大家一致同意的用于启动新网络的内容，比如系统合约，ERC-20 快照等等





### 练习使用 boot 命令

再次确认 `my_discovery_file.yaml` 文件配置（详细介绍见上文），然后执行:

    ./eos-bios boot

该命令将测试所以名为 `hook_boot_*.sh` 的 hooks 脚本.


发现网络中的其他节点
-----------------------

配置好 discovery 文件之后，可以通过下面的命令发现种子网络中的所有其他节点:

    ./eos-bios discover

改命令将输出节点的信息，比如名字，网址，权重等等。


可信网络节点
-------------

discovery 配置文件中的 `seed_network_peers` 部分的示例配置如下：

```
seed_network_peers:
- account: eosexample
  comment: "They are good"
  weight: 10  # Weights are between 0 and 100 (INT value)
- account: eosmore
  comment: "They are better"
  weight: 20
```

上述配置意味着，在启动新网络的过程中你信任其他两个节点，也就是
`eosexample` （10% 的权重）以及`eosmore`（20% 的权重）节点。`eos-bios`
将根据种子网络中所以节点的 `seed_network_peers` 配置计算各个节点的权重，并根据该权重，在启动网络时赋予不同节点不同的角色.

`seed_network_peers` 配置中的 `account` 表示的是种子网络中的账户名。


种子网络
-------------

| 种子网络地址 | Stage | 启动时间 |
| --------------------- | -----:| ------:|
| http://stage0.eoscanada.com | 0 | May 9th 2018 |
| http://stage1.eoscanada.com | 1 | May 9th 2018 |




多节点协作启动网络的示例流程
-----------------------------------------------

1. 所有节点运行 `eos-bios orchestrate` 命令。
2. `eos-bios` 读取所有节点的 discovery 配置，并根据该配置计算网络拓扑结构。
3. 根据各节点的权重值将节点进行排序。
4. 权重排名最高的 20% 的节点就 `seed_network_launch_block` 达成一致后，新网络开始启动。



下载和安装
------------------

你可以在这里下载该工具的最新版本:

[https://github.com/eoscanada/eos-bios/releases](https://github.com/eoscanada/eos-bios/releases)

这是一个编译好的二进制可执行文件，支持主流的操作系统。该工具没有其他依赖，只需被赋予可执行权限，即可运行。

当然，你也可以自行编译（使用 Go 语言）：

    go get -v github.com/eoscanada/eos-bios/eos-bios

编译成功之后，可执行文件将默认安装到 `~/go/bin` 目录下（安装 Go 请查阅 https://golang.org/dl ）。



加入 Telegram 讨论
-------------------

加入 Telegram BIOS BOOT 讨论群：
[https://t.me/joinchat/GSUv1UaI5QIuifHZs8k_eA](EOSIO BIOS BOOT)



旧版方案和文档
--------------------

旧版方案和文档请参见代码库中的 README.v0.md 文件




配置检查清单
-------------------

* 是否已经将配置中的 `target_p2p_address` 改为新网络的节点 p2p 地址？
* 是否已经将配置中的 `target_http_address` 指向了我的节点地址, 并且确保节点可以背 `eos-bios` 访问到？



Troubleshooting
---------------

* `hook_join_network.sh` 文件中的 `PRIVKEY` 和 `PUBKEY` 是否和你的 discovery 文件的
  `target_initial_authority` 和 `target_initial_block_signing_key` 一致？

* 网络分叉了？是否有节点配置了旧网络的 p2p 地址？