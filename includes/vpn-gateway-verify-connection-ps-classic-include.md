你可以验证你的连接成功通过 Get-azurevnetconnection cmdlet。

1. 使用以下 cmdlet 示例中，配置以满足自己的值。 如果它包含空格，虚拟网络的名称必须用引号引起来。

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Cmdlet 运行完毕后，查看的值。 在下面的示例中，连接状态显示为已连接，然后你可以看到入口和出口字节数。

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal