如果你不能通过你的 VPN 连接连接到虚拟机，检查下列项目：

- 验证你的 VPN 连接成功。
- 验证你为 VM 连接到专用的 IP 地址。
- 如果你可以连接到虚拟机使用专用的 IP 地址，但不是计算机名称，请验证你已正确配置 DNS。 有关名称解析用于虚拟机的工作原理的详细信息，请参阅[Vm 的名称解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。

当你通过点到站点连接时，请检查以下其他项：

- 使用 ipconfig 来检查分配给从其所连接的计算机上的以太网适配器的 IPv4 地址。 如果你登录到要连接的 VNet 的地址范围内或您 VPNClientAddressPool 的地址范围内的 IP 地址，这称为重叠的地址空间。 当你的地址空间重叠以这种方式时，网络流量不会达到 Azure 时，它会保留在本地网络上。
- 验证为 VNet 指定 DNS 服务器 IP 地址后，生成 VPN 客户端配置程序包。 如果更新的 DNS 服务器 IP 地址，请生成并安装新的 VPN 客户端配置程序包。

有关疑难解答的 RDP 连接的详细信息，请参阅[解决远程桌面连接到 VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。