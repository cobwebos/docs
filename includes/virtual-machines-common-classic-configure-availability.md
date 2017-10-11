


可用性集可帮助如保留虚拟机期间停机时间，可用在维护期间。 将两个或多个采用类似配置的虚拟机放置在可用性集中创建维护的应用程序或你的虚拟机运行的服务的可用性所需的冗余。 有关此工作原理的详细信息，请参阅[管理虚拟机的可用性][Manage the availability of virtual machines]。

它是使用可用性集和负载平衡终结点以帮助确保你的应用程序始终可用并且正在运行有效地的最佳实践。 有关负载平衡终结点的详细信息，请参阅[的负载平衡 Azure 基础结构服务][Load balancing for Azure infrastructure services]。

也可以将经典虚拟机添加到可用性集使用两个选项之一：

* [选项 1： 创建虚拟机和可用性集在同一时间][Option 1: Create a virtual machine and an availability set at the same time]。 然后，将新的虚拟机添加到集，当你创建这些虚拟机。
* [选项 2： 将现有的虚拟机添加到可用性集][Option 2: Add an existing virtual machine to an availability set]。

> [!NOTE]
> 在经典模型中，你要放入同一可用性集中的虚拟机必须属于同一云服务。
> 
> 

## <a id="createset"></a>选项 1： 创建虚拟机和可用性集在同一时间
可以使用 Azure 门户或 Azure PowerShell 命令来执行此操作。

若要使用 Azure 门户：

1. 如果你尚未这样做，登录到[Azure 门户](https://portal.azure.com)。
2. 在中心菜单上，单击**+ 新建**，然后单击**虚拟机**。
   
    ![Alt 映像文本](./media/virtual-machines-common-classic-configure-availability/ChooseVMImage.png)
3. 选择你想要使用的应用商店虚拟机映像。 你可以选择创建 Linux 或 Windows 虚拟机。
4. 对于所选的虚拟机，请验证部署模型设置为**经典**，然后单击**创建**
   
    ![Alt 映像文本](./media/virtual-machines-common-classic-configure-availability/ChooseClassicModel.png)
5. 输入虚拟机名称、 用户名和密码 （对于 Windows 计算机） 或 SSH 公钥 （适用于 Linux 计算机）。 
6. 选择 VM 大小，然后单击**选择**以继续。
7. 选择**可选配置 > 可用性集**，以及选择的可用性集你想要添加到虚拟机。
   
    ![Alt 映像文本](./media/virtual-machines-common-classic-configure-availability/ChooseAvailabilitySet.png) 
8. 查看你的配置设置。 完成后，单击**创建**。
9. 当 Azure 创建虚拟机时，你可以跟踪在进度**虚拟机**在中心菜单上。

若要使用 Azure PowerShell 命令创建 Azure 虚拟机并将其添加到新的或现有的可用性集，请参阅[使用 Azure PowerShell 创建和预配置基于 Windows 的虚拟机](../articles/virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a id="addmachine"></a>选项 2： 将现有虚拟机添加到可用性集
在 Azure 门户中，你可以添加到现有可用性的现有经典虚拟机设置或创建新的。 （请记住，同一可用性组中的虚拟机必须属于同一云服务。）步骤是几乎相同。 使用 Azure PowerShell，可以将虚拟机添加到现有可用性集。

1. 如果尚未这样做，登录到[Azure 门户](https://portal.azure.com)。
2. 在中心菜单上，单击**虚拟机 （经典）**。
   
    ![Alt 映像文本](./media/virtual-machines-common-classic-configure-availability/ChooseClassicVM.png)
3. 从虚拟机列表中，选择你想要添加到集中的虚拟机的名称。
4. 选择**可用性集**从虚拟机**设置**。
   
    ![Alt 映像文本](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetSettings.png)
5. 选择要添加到虚拟机的可用性集。 虚拟机必须属于相同的云服务可用性集。
   
    ![Alt 映像文本](./media/virtual-machines-common-classic-configure-availability/AvailabilitySetPicker.png)
6. 单击 **“保存”**。

若要使用 Azure PowerShell 命令，请打开管理员级别 Azure PowerShell 会话，运行以下命令。 占位符 (如&lt;VmCloudServiceName&gt;)，替换引号，包括内的所有内容 < 和 > 字符，替换为正确的名称。

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

> [!NOTE]
> 虚拟机可能需要重新启动才能完成将其添加到可用性集。
> 
> 

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: ../articles/virtual-machines/virtual-machines-linux-load-balance.md
[Manage the availability of virtual machines]:../articles/virtual-machines/linux/manage-availability.md

[Create a virtual machine running Windows]: ../articles/virtual-machines/virtual-machines-windows-hero-tutorial.md
[Virtual Network overview]: ../articles/virtual-network/virtual-networks-overview.md

