虚拟网络网关使用名为“GatewaySubnet”的特定子网。 网关子网包含 VPN 网关服务使用的 IP 地址。 创建网关子网时，必须将其命名为“GatewaySubnet”。  将一个子网命名为“GatewaySubnet”可让 Azure 了解创建网关服务的位置。 如果用其他名称命名子网，则 VPN 网关配置将会失败。

GatewaySubnet 中的 IP 地址分配到网关服务。 创建 GatewaySubnet 时，请指定子网包含的 IP 地址数。 指定的 GatewaySubnet 的大小取决于要创建的 VPN 网关配置。 尽管创建的 GatewaySubnet 最小可为 /29，但建议选择 /27 或 /28，创建包含更多地址的更大子网。 使用更大的网关子网可以有足够的 IP 地址来应对未来可能会有的配置。