你可以连接到 VM 部署到你的 VNet 的创建与你的 VM 的远程桌面连接。 最初验证你可以连接到你的 VM 的最佳方法是使用其专用 IP 地址，而不是计算机名称进行连接。 这样一来，你要测试以查看您是否可以连接，是否名称解析配置不正确。 

1. 找到你的 VM 的专用 IP 地址。 用于在 Azure 门户中，VM 看属性或使用 PowerShell，你可以找到 VM 的专用 IP 地址。
2. 验证你已连接到你的 VNet 使用点到站点 VPN 连接。 
3. 在任务栏中，搜索框中键入"RDP"远程桌面连接"打开远程桌面连接，然后选择远程桌面连接。 你也可以打开在 PowerShell 中使用 mstsc 命令的远程桌面连接。 
3. 在远程桌面连接中，输入 VM 的专用 IP 地址。 你可以单击"显示选项"以调整其他设置，然后连接。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>若要解决连接到 VM 的 RDP 连接

如果你不能通过你的 VPN 连接连接到虚拟机，有的一些你可以检查。 有关详细的疑难解答信息，请参阅[解决远程桌面连接到 VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。

- 验证你的 VPN 连接成功。
- 验证你为 VM 连接到专用的 IP 地址。
- 使用 ipconfig 来检查分配给从其所连接的计算机上的以太网适配器的 IPv4 地址。 如果你登录到要连接的 VNet 的地址范围内或您 VPNClientAddressPool 的地址范围内的 IP 地址，这称为重叠的地址空间。 当你的地址空间重叠以这种方式时，网络流量不会达到 Azure 时，它会保留在本地网络上。
- 如果你可以连接到虚拟机使用专用的 IP 地址，但不是计算机名称，请验证你已正确配置 DNS。 有关名称解析用于虚拟机的工作原理的详细信息，请参阅[Vm 的名称解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
- 验证为 VNet 指定 DNS 服务器 IP 地址后，生成 VPN 客户端配置程序包。 如果更新的 DNS 服务器 IP 地址，请生成并安装新的 VPN 客户端配置程序包。