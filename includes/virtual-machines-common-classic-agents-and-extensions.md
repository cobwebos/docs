

VM 扩展可帮助你：

* 修改安全和标识功能，例如重置帐户值和使用反恶意软件
* 启动、 停止或配置监视和诊断
* 重置或安装连接功能，如 RDP 和 SSH
* 诊断、 监视和管理 Vm

有许多其他功能。 将定期发布新的 VM 扩展功能。 本文介绍适用于 Windows 和 Linux，以及它们如何支持 VM 扩展功能的 Azure VM 代理。 有关按功能类别的 VM 扩展的列表，请参阅[Azure VM 扩展和功能](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

## <a name="azure-vm-agents-for-windows-and-linux"></a>Windows 和 Linux 的 azure VM 代理
Azure 虚拟机代理 （VM 代理） 是安装、 配置和删除 VM 扩展实例的 Azure 虚拟机上的受保护、 轻量进程。 VM 代理充当 Azure VM 的安全本地控制服务。 该代理加载的扩展提供特定功能提高工作效率使用的实例。

将存在两种 Azure VM 代理，一个用于 Windows Vm，对于 Linux Vm 的一个。

如果你想要使用一个或多个 VM 扩展的虚拟机实例，该实例必须具有已安装的 VM 代理。 通过使用 Azure 门户和从映像创建虚拟机映像**Marketplace**会自动在创建过程中安装 VM 代理。 如果虚拟机实例缺少 VM 代理，你可以在创建虚拟机实例之后安装 VM 代理。 或者，你可以然后将上载的自定义 VM 映像中安装代理。

> [!IMPORTANT]
> 这些 VM 代理是非常轻量级的启用的安全的管理的虚拟机实例的服务。 可能有不希望 VM 代理的情况。 如果是这样，请务必创建没有使用 Azure CLI 或 PowerShell 安装 VM 代理的 Vm。 尽管可以物理删除 VM 代理，但实例上的 VM 扩展的行为是不确定的。 因此，不支持删除已安装的 VM 代理。
>

在以下情况下启用 VM 代理：

* 通过使用 Azure 门户并选择从映像创建虚拟机实例时**Marketplace**，
* 通过使用创建 VM 的实例时[New-azurevm](https://msdn.microsoft.com/library/azure/dn495254.aspx)或[New-azurequickvm](https://msdn.microsoft.com/library/azure/dn495183.aspx) cmdlet。 你可以通过添加创建没有 VM 代理 VM **– DisableGuestAgent**参数[Add-azureprovisioningconfig](https://msdn.microsoft.com/library/azure/dn495299.aspx) cmdlet，

* 当你手动下载和安装 VM 代理上现有的 VM 实例，并设置**ProvisionGuestAgent**值赋给**true**。 你可以使用此方法适用于 Windows 和 Linux 代理，通过使用 PowerShell 命令或 REST 调用。 (如果未设置**ProvisionGuestAgent**值后手动安装 VM 代理，VM 代理添加不正确检测到。)下面的代码示例演示如何使用 PowerShell 执行此操作其中`$svc`和`$name`已确定自变量：

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* 当你创建的 VM 映像包含已安装的 VM 代理。 存在使用 VM 代理的映像，你可以将该映像上载到 Azure。 对于 Windows VM，下载[Windows VM 代理.msi 文件](http://go.microsoft.com/fwlink/?LinkID=394789)并安装 VM 代理。 对于 Linux VM，请从 GitHub 存储库位于安装 VM 代理<https://github.com/Azure/WALinuxAgent>。 有关如何在 Linux 上安装 VM 代理的详细信息，请参阅[Azure Linux VM 代理用户指南](../articles/virtual-machines/linux/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

> [!NOTE]
> 在 PaaS 中，VM 代理称为**WindowsAzureGuestAgent**，并且始终可在 Web 和辅助角色 Vm 上。 (有关详细信息，请参阅[Azure 角色体系结构](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx)。)角色 Vm 的 VM 代理现在可以添加扩展到云服务 Vm 方式与其永久性虚拟机完全相同。 添加 VM 扩展时，在角色 Vm 上的 VM 扩展和永久 Vm 的最大区别。 使用角色 Vm，到云服务中，然后到云服务中部署第一次添加扩展。
>
> 使用[Get-azureserviceavailableextension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet 可列出所有可用的角色 VM 扩展。
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>查找、 添加、 更新和删除 VM 扩展
有关这些任务的详细信息，请参阅[添加、 查找、 更新和删除 Azure VM 扩展](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。
