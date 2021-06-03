deploy 中文文档
===========
部署环境：ubuntu20.04
### 安装部署
* 在管理节点主机上安装Ansible
* 为所有节点创建ruser用户，为Ansible管理主机配置ruser免密码ssh登录到其他节点
* 所有节点主机安装docker,	docker-compose <br>
git clone https://github.com/tonlabs/ton-labs-deploy-net.git	<br>
---

### 修改环境参数
`ton-labs-deploy-net/scripts/env_profiles/env_fld_deploy_net.sh`<br>	
    export SDK_URL="" SDK_URL变量自定义 为DApp server的URL<br>	

`ansible/hosts/fldnet`<br>
    fldnet [fldnet_validators] 配置节点信息
    
`scripts/env.sh`<br>
    export HOST_SSH_PORT=${HOST_SSH_PORT:-2223}<br>
    export REMOTE_USER=${REMOTE_USER:-ruser}<br>
    
`ansible/group_vars/all.yaml`<br>
	ansible_ssh_user: ruser<br>
	ansible_port: 2223<br>
* 创建 https://github.com/tonlabs/ton-1/tree/fld 的分支并自定义 crypto/smartcont/gen_wallets.sh（ruser在那里更改为您的 ssh 用户)

### 配置初始多重签名钱包
* 创建https://github.com/tonlabs/ton-1/tree/fld的分支<br>
* 在分叉中编辑https://github.com/tonlabs/ton-1/blob/fld/crypto/smartcont/InitWalletsFld.fif<br>
在https://github.com/tonlabs/ton-1/blob/fld/crypto/smartcont/InitWalletsFld.fif 中您将看到一个钱包列表（这些钱包将被初始验证器节点使用），其形式为：<br>
    // [wallets]<br>
    entry "ZerostateMsig0" GR$6000001 "7a56f74315f7e1c7db2cf662603acad7beeba788fe6b20c9bb6dfd8abe262bcc" create-msig<br>
    // [end]<br>
