你可以验证你的连接成功通过[az 网络 vpn 连接显示](/cli/azure/network/vpn-connection#show)命令。 在示例中，-名称指的是你想要测试连接的名称。 过程中正在建立连接时，其连接状态将显示连接。 一旦建立连接时，状态将更改为已连接。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

