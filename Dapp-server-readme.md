## 部署Dapp Server
1. `git clone https://github.com/tonlabs/TON-OS-DApp-Server.git`
2. 修改`TON-OS-DApp-Server/scripts/env.sh`
```
export NETWORK_TYPE="${NETWORK_TYPE:-net.ton.dev}"
export EMAIL_FOR_NOTIFICATIONS="email@yourdomain.com"
```
3. 添加`TON-OS-DApp-Server/docker-compose/statsd/.env `  
	IntIP='your internet ip'

5. copy初始节点容器中的`ton-global.config.json`,在下面目录中
```
/mount/persistent-volume/t-node-blockchain-configs/etc/ton-global.config.json
```
将其上传在github中，复制raw地址，例如`https://raw.githubusercontent.com/bsnton/deploy/main/ton-global.config.json`

6. 在`/TON-OS-DApp-Server/docker-compose/ton-node/build/entrypoint.sh
`中

用刚才上传的文件链接
`https://raw.githubusercontent.com/bsnton/deploy/main/ton-global.config.json`
替换`https://raw.githubusercontent.com/tonlabs/${NETWORK_TYPE}/master/configs/${NETWORK_TYPE}/ton-global.config.json`


6. 运行安装
```
$ cd TON-OS-DApp-Server/scripts/
$ . ./env.sh 
$ ./install_deps.sh
$ ./deploy.sh

```
7. 安装完成，代理q-server  
***注意**：当前安装教程的proxy代理容器配置好像有问题，停止web.root和proxy容器，手动代理*
```
docker stop web.root
docker stop proxy
```
	安装nginx代理到q-server的端口4000，最好加https
	访问 https://yourdomain/graphql  进行验证
