可以使用 `Get-AzureVNetConnection` 验证经典虚拟网络网关的连接。 

1. 使用以下 cmdlet 示例，配置符合自己需要的值。 如果虚拟网络的名称包含空格，必须将该名称括在引号中。
   
        Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
2. cmdlet 运行完毕后，查看该值。 在以下示例中，“连接状态”显示为“已连接”，且可以看到入口和出口字节数。

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal


<!--HONumber=Jan17_HO3-->


