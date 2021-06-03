deploy 中文文档
===========
部署环境：ubuntu20.04
##### 安装部署
* 在管理节点主机上安装Ansible
* 为所有节点创建ruser用户，为Ansible管理主机配置ruser免密码ssh登录到其他节点
* 所有节点主机安装docker,	docker-compose <br>
git clone https://github.com/tonlabs/ton-labs-deploy-net.git	<br>
---

##### 修改环境参数
`ton-labs-deploy-net/scripts/env_profiles/env_fld_deploy_net.sh`<br>
     *export SDK_URL="" SDK_URL变量自定义 为DApp server的URL*<br>
     
		 
`ansible/hosts/fldnet`<br>
`scripts/env.sh`<br>
`ansible/group_vars/all.yaml`<br>

