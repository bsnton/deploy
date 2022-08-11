### 原部署文档链接：https://github.com/tonlabs/ton-labs-deploy-net/tree/rust
### 下面将对部署过程细节部分说明

# README
* 节点操作系统采用ubuntu 20.04
* 控制主机安装ansible, 下面命令
```
sudo apt update
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```



## Prerequisites for the management node (host you start deployments from)
* Add the hostnames of all validators to the next configuration file (all hostnames should be accessible from the management host)
  * `ansible/hosts/fldnet` (pay attention to change hostname twice in the each line)
    like
    ```
    [fldnet_validators]
    host1 ansible_host=host1
    host2 ansible_host=host2
    host3 ansible_host=host3
    ```
  
* Customize `SDK_URL` variable in `ansible/group_vars/all.yaml` to the appropriate DApp Server URL,
* Dapp sever URL 最好https开头
* Configure passwordless ssh to the validators hosts (may be done by the "ssh certificate authentication")

* Install Ansible ver >= 2.9.6 on the management node

* Customize `ansible/group_vars/all.yaml` (e.g. check your ssh user and port, verify other parameters as well).

`ansible/group_vars/all.yaml`:
 ```
 ansible_ssh_user: ruser
 ansible_port: 2223
 ```
## 节点主机配置
* 创建ruser用户
* 安装docker，并授权ruser用户可用

## Build RUST node docker image
Edit `NODE_IMAGE` parameter in `ansible/group_vars/all.yaml` - specify the name for node docker image. 

You can use https://github.com/tonlabs/net.ton.dev build scripts as an example.

Make sure just built node docker image is available on all the hosts where the network will be deployed.

可以直接使用我已经push好的node镜像：docker pull huawuque18/ton-node_node:latest  
即修改：https://github.com/tonlabs/ton-labs-deploy-net/blob/rust/ansible/group_vars/all.yaml#L18

## Run Network Deployment
```
ssh-agent
ssh-add
```
```
cd ansible && ansible-playbook -i hosts ./deploy-network.playbook.yaml 
```
## Deploy DApp Server
* See https://docs.ton.dev/86757ecb2/p/472e61-run-dapp-server and https://github.com/tonlabs/TON-OS-DApp-Server
## Deploy Wallets
* Download multisig deployment files (`SafeMultisigWallet.tvc`, `SafeMultisigWallet.abi.json`, `SetcodeMultisigWallet.abi.json`) from https://github.com/tonlabs/ton-labs-contracts/tree/master/solidity/safemultisig and https://github.com/tonlabs/ton-labs-contracts/tree/master/solidity/setcodemultisig
* Deploy validator wallets:
Generate wallet address (`WALLET_ADDRESS`) for `msig.keys.json` key pair:
```
tonos-cli genaddr SafeMultisigWallet.tvc SafeMultisigWallet.abi.json --setkey msig.keys.json --wc -1
```
```
tonos-cli call "${WALLET_ADDRESS}" constructor "{\"owners\":[\"0x<custodian_pub_key>\"],\"reqConfirms\":1}" --abi SafeMultisigWallet.abi.json --sign msig.keys.json
```
* Deploy main wallet:
```
tonos-cli call -1:7777777777777777777777777777777777777777777777777777777777777777 constructor '{"owners":["0x<custodian_pub_key>"],"reqConfirms":1}' --abi SetcodeMultisigWallet.abi.json --sign msig.keys.json
```
You can use public key from `msig.keys.json` as `<custodian_pub_key>`.
* Configure wallets on each validator node according to https://docs.ton.dev/86757ecb2/p/60448f-run-automated-validator-single-custodian
