你必须创建 VNet 和网关子网第一次，然后处理以下任务。 请参阅文章[配置虚拟网络使用经典门户](../articles/expressroute/expressroute-howto-vnet-portal-classic.md)有关详细信息。   

## <a name="add-a-gateway"></a>添加网关
使用以下命令来创建网关。 请务必替换为你自己的任何值。

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>验证已创建网关
使用以下命令来验证已创建网关。 此命令还会检索网关 ID，你需要其他操作。

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>调整大小网关
有大量的[网关 Sku](../articles/expressroute/expressroute-about-virtual-network-gateways.md)。 可以使用以下命令以在任何时候更改网关 SKU。

> [!IMPORTANT]
> 此命令不起作用 UltraPerformance 网关。 若要更改 UltraPerformance 网关到网关，请首先删除现有的 ExpressRoute 网关，然后创建新的 UltraPerformance 网关。 若要降级从 UltraPerformance 网关将网关，首先删除 UltraPerformance 网关，然后再创建新的网关。 
> 
> 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>删除网关
使用以下命令以删除网关

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>