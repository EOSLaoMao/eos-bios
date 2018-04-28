# 前期准备阶段
1. 准备三台机器（模拟三节点boot， 老猫团队用的是三台Ubuntu 16.04，配置不需要太高）
2. 安装好docker（可以选择local的方式，本教程以docker为主）
3. 将当前操作的linux用户添加进docker的用户组
4. 在每台机器上，下载eos-bios项目，具体还有一些其他步骤可以参照README-cn.md中的“启动本地单一节点” 一节

# 准备配置文件
1. 进入eos-bios的sample_config目录，ls看一下， 这里面有三个文件，会要修改，分别是
    * config.yaml
    * my_discovery_file.yaml
    * privkey-GDW5CV.key
2. config.yaml

 
