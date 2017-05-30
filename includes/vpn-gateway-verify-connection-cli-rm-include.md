可使用 [az network vpn-connection show](/cli/azure/network/vpn-connection#show) 命令来验证连接是否成功。 在此示例中，“ --Name”是指要测试的连接的名称。 当连接处于建立过程中时，连接状态会显示“正在连接”。 建立连接后，状态更改为“已连接”。

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

