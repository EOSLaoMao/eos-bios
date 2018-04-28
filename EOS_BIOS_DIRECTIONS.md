# 前期准备阶段
1. 准备三台机器（模拟三节点boot， 老猫团队用的是三台Ubuntu 16.04), 三台节点的名字分别为test-node1,test-node2,test-node3。
2. 安装好docker（可以选择local的方式，本教程以docker为主）
3. 将当前操作的linux用户添加进docker的用户组
4. 在每台机器上，下载eos-bios项目，具体还有一些其他步骤可以参照README-cn.md中的“启动本地单一节点” 一节
5. 分别为三个节点，生成三对公私钥匙, 下面是例子, 不能用于测试，请自行生成： 
    * test-node1
        * private_key: 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD4
        * public_key: EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV 

    * test-node2
        * private_key: 5HSskKHwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zs4tsv
        * public_key: EOS7595AjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW79D 

    * test-node3
        * private_key: 55ISKHwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zs4jma
        * public_key: EOS7859Skq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW70A 

# 准备配置文件
1. 进入某个节点下eos-bios的sample_config目录，ls看一下， 这里面有三个文件，会要被修改，分别是
    * config.yaml: 本地的配置文件，执行eos-bios的会读取该文件，以确认本地的环境
    * my_discovery_file.yaml: 这份文件里面的内容比较多，为了简化描述，我们用 https://abourget.keybase.pub/testnet-docker.yaml 代替，将该yaml下载下来，命名位testnet-docker-[nodename].yam，例如：testnet-docker-node1.yaml。每个节点都需要一个discovery文件与之对应，所以一共要准备三份。
    * privkey-GDW5CV.key
2. 分别修改testnet-docker-[hostname].yaml
    * 编辑该文件，具体参考下图(以test-node1为例)
    ![test-node1](https://github.com/EOSLaoMao/eos-bios/blob/feature/bios-boot-directions/img/discovery.png?raw=true)
    * 上传三个文件到keybase(具体步骤不描述了，请自己查看，自己测试也可以用其他的静态文件服务)
    * 其中weight字段，是为了明确哪个节点为启动节点，假设认为test-node2为启动节点，那么三个文件中关于test-node2的weight的和应该为最大
3. 修改三个节点上的config.yaml，具体如下图（以test-node1为例）
    ![config](https://github.com/EOSLaoMao/eos-bios/blob/feature/bios-boot-directions/img/config.png?raw=true)
4. 修改三个节点上的privkey-GDW5CV.key，将里面的private_key，替换为相应节点的private_key

# 启动阶段
1. 进入某个节点的eos-bios目录, 执行<code>./eos-bios discover</code>
2. 如果上面配置正确应该会看到下图结果:
    ![eos-discover](https://github.com/EOSLaoMao/eos-bios/blob/feature/bios-boot-directions/img/eos-discover.png?raw=true)
3. weight最高的就是就会作为启动节点，weight配置在上面的discovery文件中
4. 确认好三个节点能互相发现以后，就可以准备启动了
5. 在三个节点分别运行<code>./eos-bios orchestrate</code>,无需在意顺序

 