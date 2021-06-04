Free Ton deploy中文文档
====
部署环境：ubuntu20.04
#### 安装部署
* 在管理节点主机上安装Ansible
* 为所有节点创建ruser用户，为Ansible管理主机配置ruser免密码ssh登录到其他节点
* 所有节点主机安装docker,	docker-compose <br>
* 节点开放 3030/tcp,30310/udp
* git clone https://github.com/tonlabs/ton-labs-deploy-net.git	<br>
---

#### 修改环境参数
`ton-labs-deploy-net/scripts/env_profiles/env_fld_deploy_net.sh`<br>	
    export SDK_URL="" SDK_URL变量自定义 为DApp server的URL<br>	

`ansible/hosts/fldnet`<br>
    fldnet [fldnet_validators] 配置节点信息
    
`scripts/env.sh`<br>
```
export HOST_SSH_PORT=${HOST_SSH_PORT:-2223}<br>
export REMOTE_USER=${REMOTE_USER:-ruser}<br>
```
    
`ansible/group_vars/all.yaml`<br>
```
ansible_ssh_user: ruser<br>
ansible_port: 2223<br>
```
* 创建 https://github.com/tonlabs/ton-1/tree/fld 的分支并自定义 crypto/smartcont/gen_wallets.sh（ruser在那里更改为您的 ssh 用户)

### 配置初始多重签名钱包
* 创建https://github.com/tonlabs/ton-1/tree/fld的分支<br>
* 在分叉中编辑https://github.com/tonlabs/ton-1/blob/fld/crypto/smartcont/InitWalletsFld.fif<br>
在https://github.com/tonlabs/ton-1/blob/fld/crypto/smartcont/InitWalletsFld.fif 中您将看到一个钱包列表（这些钱包将被初始验证器节点使用），其形式为：<br>
```
//[wallets]<br>
entry "ZerostateMsig0" GR$6000001 "7a56f74315f7e1c7db2cf662603acad7beeba788fe6b20c9bb6dfd8abe262bcc" create-msig<br>
// [end]<br>  
```
`ZerostateMsig0`是排序索引,`GR$6000001`是初始钱包余额,`7a56f74315f7e1c7db2cf662603acad7beeba788fe6b20c9bb6dfd8abe262bcc`钱包部署公钥。<br>
要生成钱包，您应该按照以下步骤操作：
```
tonos-cli genphrase
tonos-cli genpubkey "<seed_phrase>"
```
或生成密钥对并从那里获取公钥：
```
tonos-cli getkeypair msig.keys.json "<seed_phrase>"
```
对主钱包执行相同操作：
```
giver "Ref" ref-addr GR$5000000000 "553f9351ca08417c8c338413c2fd4b30eb7f6d35694e3e1b8ceb3d5729a34520" create-spec-giver
```
提交并将更改推送到您的 ton-1 叉中。
* 为节点部署脚本创建一个fork https://github.com/tonlabs/net.ton.dev/tree/fld
* 在fork https://github.com/tonlabs/net.ton.dev/blob/fld/scripts/env.sh 中编辑对节点源文件的引用：
``````
export TON_GITHUB_REPO="https://github.com/<your_fork>/ton-1.git"
export TON_STABLE_GITHUB_COMMIT_ID="<your_branch>"  #默认fld，不需要修改。
``````
* 在网络部署脚本中编辑对节点部署脚本的引用： https://github.com/tonlabs/ton-labs-deploy-net/blob/master/docker-compose/node/build/Dockerfile ：
```
RUN git clone https://github.com/tonlabs/net.ton.dev.git && cd net.ton.dev && git checkout fld
```
****
### 运行节点部署
```
cd scripts && ./deploy.sh
```
### 部署TONOS DApp Server
* 请参阅https://docs.ton.dev/86757ecb2/p/472e61-run-dapp-server和https://github.com/tonlabs/TON-OS-DApp-Server


### 部署钱包
##### 安装tonos-cl 工具操作，方法查看链接：https://docs.ton.dev/86757ecb2/p/8080e6-tonos-cli
* 从 https://github.com/tonlabs/ton-labs-contracts/tree/master/solidity/safemultisig和https://github.com/tonlabs/ton-labs-contracts/tree 
下载 multisig部署文件(SafeMultisigWallet.tvc, SafeMultisigWallet.abi.json, SetcodeMultisigWallet.abi.json)/master/solidity/setcodemultisig  
* 部署验证器钱包：WALLET_ADDRESS为msig.keys.json密钥对生成钱包地址 ( ) ：
```
tonos-cli genaddr SafeMultisigWallet.tvc SafeMultisigWallet.abi.json --setkey msig.keys.json --wc -1
```
```
tonos-cli call "${WALLET_ADDRESS}" constructor "{\"owners\":[\"0x<custodian_pub_key>\"],\"reqConfirms\":1}" --abi SafeMultisigWallet.abi.json --sign msig.keys.json
```

* 部署主钱包：
```
tonos-cli call -1:7777777777777777777777777777777777777777777777777777777777777777 constructor '{"owners":["0x<custodian_pub_key>"],"reqConfirms":1}' --abi SetcodeMultisigWallet.abi.json --sign msig.keys.json
```
您可以使用来自msig.keys.jsonas 的公钥<custodian_pub_key>。

* 根据 https://docs.ton.dev/86757ecb2/p/60448f-run-automated-validator-single-custodian 在每个验证器节点上配置钱包
