你可以连接到 VM 部署到你的 VNet 的创建与你的 VM 的远程桌面连接。 最初验证你可以连接到你的 VM 的最佳方法是使用其专用 IP 地址，而不是计算机名称进行连接。 这样一来，你要测试以查看您是否可以连接，是否名称解析配置不正确。

1. 找到的专用 IP 地址。 可以采用多种方式来找到 VM 的专用 IP 地址。 下面，我们展示的 Azure 门户和 powershell 的步骤。

  - Azure 门户-在 Azure 门户中找到你的虚拟机。 查看 VM 的属性。 列出专用的 IP 地址。

  - PowerShell-使用此示例以查看 Vm 的专用 IP 列表地址从资源组。 你不必在使用它之前修改此示例。

    ```powershell
    $VMs = Get-AzureRmVM
    $Nics = Get-AzureRmNetworkInterface | Where VirtualMachine -ne $null

    foreach($Nic in $Nics)
    {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
    }
    ```

2. 验证你已连接到你的 VNet 使用 VPN 连接。
3. 打开**远程桌面连接**通过在任务栏上的搜索框中键入"RDP"远程桌面连接"，然后选择远程桌面连接。 你也可以打开在 PowerShell 中使用 mstsc 命令的远程桌面连接。 
4. 在远程桌面连接中，输入 VM 的专用 IP 地址。 你可以单击"显示选项"以调整其他设置，然后连接。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>若要解决连接到 VM 的 RDP 连接

如果你不能通过你的 VPN 连接连接到虚拟机，检查下列项目：

- 验证你的 VPN 连接成功。
- 验证你为 VM 连接到专用的 IP 地址。
- 如果你可以连接到虚拟机使用专用的 IP 地址，但不是计算机名称，请验证你已正确配置 DNS。 有关名称解析用于虚拟机的工作原理的详细信息，请参阅[Vm 的名称解析](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)。
- 有关 RDP 连接的详细信息，请参阅[解决远程桌面连接到 VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)。