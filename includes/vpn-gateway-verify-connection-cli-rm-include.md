可使用以下 CLI 命令来验证连接是否成功。 将值配置为与自己的值相符。 在此示例中， -n 指所创建的，并要测试的连接的名称。

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

当连接仍在建立过程中时，连接状态会显示“正在连接”。 建立连接后，状态将更改为“已连接”，且可以看到入口和出口字节数。