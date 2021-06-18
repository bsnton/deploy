## 部署 Dapp Server
1. `git clone https://github.com/tonlabs/TON-OS-DApp-Server.git`
2. 修改`TON-OS-DApp-Server/scripts/env.sh`
```
export NETWORK_TYPE="${NETWORK_TYPE:-net.ton.dev}"
export EMAIL_FOR_NOTIFICATIONS="email@yourdomain.com"
```
3. 添加`TON-OS-DApp-Server/docker-compose/statsd/.env `  

`IntIP='your internet ip'`

4. copy容器中的`ton-global.config.json`,在下面目录中
```
/mount/persistent-volume/t-node-blockchain/configs/ton-global.config.json
```
将其上传在github中，复制raw地址，例如`https://raw.githubusercontent.com/bsnton/deploy/main/ton-global.config.json`

5. 在`/TON-OS-DApp-Server/docker-compose/ton-node/build/entrypoint.sh
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

