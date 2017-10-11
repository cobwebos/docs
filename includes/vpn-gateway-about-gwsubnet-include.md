虚拟网络网关使用名为 GatewaySubnet 的特定子网。 网关子网包含 VPN 网关服务所使用的 IP 地址。 在创建网关子网时，它必须命名为 GatewaySubnet。  在命名子网 GatewaySubnet 告知 Azure 要创建的网关服务的位置。 如果你其他名称子网，你的 VPN 网关配置将会失败。

GatewaySubnet 中的 IP 地址被分配到网关服务。 当创建 GatewaySubnet 时，你指定的子网包含的 IP 地址数。 你指定 GatewaySubnet 的大小取决于你想要创建的 VPN 网关配置。 可以创建 GatewaySubnet 越小越/29 时，我们建议你创建更大的子网，通过选择/27 或/28 包括多个地址。 使用更大的网关子网允许足够的 IP 地址，以容纳将来可能的配置。