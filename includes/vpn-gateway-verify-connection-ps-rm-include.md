你可以验证你的连接成功和不带，通过 Get AzureRmVirtualNetworkGatewayConnection cmdlet 中，-调试。 

1. 使用以下 cmdlet 示例中，配置以满足自己的值。 如果出现提示，请选择以便运行 'All' 'A'。 在示例中，-名称指的是你想要测试连接的名称。

  ```powershell
  Get-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnection -ResourceGroupName MyRG
  ```
2. Cmdlet 运行完毕后，查看的值。 在下面的示例中，连接状态将显示为已连接，你可以看到入口和出口字节数。
   
  ```
  "connectionStatus": "Connected",
  "ingressBytesTransferred": 33509044,
  "egressBytesTransferred": 4142431
  ```