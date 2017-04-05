



有关 VM 代理及其如何支持 VM 扩展的详细信息，请参阅 [VM 代理和 VM 扩展概述](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。

## <a name="azure-vm-extensions"></a>Azure VM 扩展
VM 扩展实现了要用于 VM 的大多数关键功能，包括重置密码、配置 RDP 等基本功能以及其他许多功能。 由于始终可添加新扩展，VM 在 Azure 中支持的可能功能的数量将不断增加。 默认情况下，从映像库创建 VM 时，将安装几个基本 VM 扩展，包括 **IaaSDiagnostics**（当前仅限 Windows VM）、**VMAccess** 和 **BGInfo**（当前也仅限 Windows）。 但是，由于功能更新和新扩展的不断流动，在任一特定时间并非所有扩展都可同时在 Windows 和 Linux 上实现。

## <a name="connectivity-and-basic-management"></a>连接和基本管理
以下扩展在创建且运行后对于启用、重新启用或禁用与 VM 的基本连接来说至关重要。

| VM 扩展名称 | 功能说明 | 更多信息 |
| --- | --- | --- |
| VMAccessAgent (Windows) |创建、更新和重置用户信息以及 RDP 连接配置。 |[Windows](../articles/virtual-machines/windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |
| VMAccessForLinux (Linux) |创建、更新和重置用户信息以及 SSH 连接配置。 |[Linux](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |

## <a name="deployment-and-configuration-management"></a>部署和配置管理
以下扩展支持不同类型的部署以及配置管理方案和功能。 另请参阅下面有关 VM 操作和管理的部分。

| VM 扩展名称 | 功能说明 | 更多信息 |
| --- | --- | --- |
| **MSEnterpriseApplication** |实现了由 Windows System Center 提供支持的功能。 |[System Center 2012 R2 虚拟机角色](http://social.technet.microsoft.com/wiki/contents/articles/18274.system-center-2012-r2-virtual-machine-role-authoring-guide-resource-extension-package.aspx) |
| **Octopus Deploy**（基于 DSC 扩展） |支持自动将 ASP.NET Web 应用程序和 Windows 服务部署到开发、测试和生产环境。 |[Octopus Deploy 入门](http://docs.octopusdeploy.com/display/OD/Getting%20started) |
| **Visual Studio 发布管理器**（基于 DSC 扩展） |使用 Visual Studio 支持连续部署。 |[使用 Release Management 自动进行部署](https://msdn.microsoft.com/Library/vs/alm/Release/overview) |
| **CentosChefClient** | | |
| **ChefClient** |在 Windows 上创建 Chef 客户端。 （也可以使用下面的 DSC 扩展。） |[Chef 和 Microsoft Azure](https://www.getchef.com/solutions/azure/) |
| **LinuxChefClient** | | |
| **DockerExtension** |安装 Docker 后台程序以支持远程 Docker 命令。 |有关[如何使用 Docker 虚拟机扩展](../articles/virtual-machines/linux/dockerextension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)更广泛的信息，请参阅 [Docker VM 扩展用户指南](https://github.com/Azure/azure-docker-extension/blob/master/README.md) |
| **DSC** |PowerShell DSC (Desired State Configuration) 扩展。 |[Azure PowerShell DSC (Desired State Configuration) 扩展](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx) |
| **PuppetEnterpriseAgent** |实现 Puppet Enterprise 的功能。 |[Azure 上的 Puppet](http://puppetlabs.com/solutions/microsoft) |
| **CustomScriptExtension** (Windows)**CustomScriptForLinux** (Linux) |在任何时候调用 VM 上的自定义脚本：启动时或在生存期内。 |[自定义脚本扩展](../articles/virtual-machines/windows/classic/extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |
| **AzureCATExtensionHandler** |使用 **IaaSDiagnostics** 和少数其他数据源（如 [Azure 存储分析指标](https://msdn.microsoft.com/library/azure/hh343270.aspx)）收集的诊断数据，并将这些数据转换为适合 SAP 主机控制进程使用的聚合数据集 |[适用于 SAP 的 Azure 增强型监视](https://azure.microsoft.com/blog/2014/06/04/azure-enhanced-monitoring-for-sap/) |

## <a name="security-and-protection"></a>安全和保护
本部分中的扩展为 Azure VM 提供关键安全功能。

| VM 扩展名称 | 功能说明 | 更多信息 |
| --- | --- | --- |
| **CloudLinkSecureVMWindowsAgent** |为 Microsoft Azure 客户提供在多租户共享的基础结构上加密其虚拟机数据的功能，并完全控制 Azure 存储基础结构上其加密数据的加密密钥。 |[利用 BitLocker 和本机 OS 加密保护 Microsoft Azure 虚拟机](http://www.cloudlinktech.com/azure) |
| **McAfeeEndpointSecurity** |保护 VM 免受恶意软件的威胁。 |[McAfee](https://www.mcafeeasap.com/MarketingContent/default.aspx) |
| **TrendMicroDSA** |启用 TrendMicro 的 Deep Security 平台支持可提供入侵检测和防护、防火墙、防恶意软件、Web 信誉评估、日志检查和完整性监视。 |[如何在 Azure VM 上安装和配置 Trend Micro Deep Security 即服务](../articles/virtual-machines/windows/classic/install-trend.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |
| **PortalProtectExtension** |保护 Microsoft SharePoint 环境免受威胁。 |[保护 Azure 上的 SharePoint 部署](http://blog.trendmicro.com/securing-sharepoint-deployment-azure/) |
| **IaaSAntimalware** |用于 Azure 云服务和虚拟机的 Microsoft 反恶意软件是一种实时保护功能，当已知的恶意软件或不需要的软件试图在系统上安装自身或运行时，它使用可配置的警报帮助识别和删除病毒、间谍软件和其他恶意软件。 |[适用于 Azure 云服务和虚拟机的反恶意软件](../articles/security/azure-security-antimalware.md) |
| **SymantecEndpointProtection** |Symantec Endpoint Protection 12.1.4 实现了跨多个物理和虚拟系统的安全性和性能。 |[如何在 Azure VM 上安装和配置 Symantec Endpoint Protection](../articles/virtual-machines/windows/classic/install-symantec.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) |

## <a name="vm-operations-and-management"></a>VM 操作和管理
支持常见的操作管理功能和行为。 另请参阅上面有关部署和配置管理的部分。

| **VM 扩展名称** | 功能说明 | 更多信息 |
| --- | --- | --- |
| **AzureVmLogCollector** |可以使用 **AzureVMLogCollector** 扩展按需从一个或多个云服务 VM（从 Web 角色和辅助角色）执行一次性日志收集，并将收集到的文件传输到 Azure 存储帐户 - 所有这些操作都无需远程登录到任何 VM。 |[AzureLogCollector 扩展](../articles/virtual-machines/windows/log-collector-extension.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| **IaaSDiagnostics** |启用、禁用和配置 Azure 诊断，也可由 **AzureCATExtensionHandler** 用于支持 SAP 监视。 |[使用 Azure 诊断扩展监视 Microsoft Azure 虚拟机](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| **OSPatchingForLinux** |使 Azure VM 管理员能够使用自定义配置自动执行 VM OS 更新。 可以使用 OSPatching 扩展为虚拟机配置 OS 更新，包括：指定安装 OS 修补程序的频率和时间，指定要安装的修补程序，并配置更新后的重新启动行为 |[OS 修补扩展博客文章](https://azure.microsoft.com/blog/2014/10/23/automate-linux-vm-os-updates-using-ospatching-extension/)。 另请参阅 GitHub 上 [OS 修补扩展](https://github.com/Azure/azure-linux-extensions)中的自述文件和源。 |

## <a name="developing-and-debugging"></a>开发和调试
出于完整性考虑，在此处列出了这些 VM 扩展，因为它们为 Visual Studio 相关功能提供支持，但不应直接使用这些扩展。

| VM 扩展名称 | 功能说明 | 更多信息 |
| --- | --- | --- |
| **VS14CTPDebugger** |支持使用 Azure SDK 2.4 从 VS 进行远程调试 |[Visual Studio 中的远程调试](https://msdn.microsoft.com/library/y7f5zaaa.aspx) |
| **VS2013Debugger** |支持使用 Azure SDK 2.4 从 VS 进行远程调试 | |
| **VS2012Debugger** |支持使用 Azure SDK 2.4 从 VS 进行远程调试 | |
| **RemoteDebugVS14CTP** |支持使用 Azure SDK 2.3 从 VS 进行远程调试 | |
| **RemoteDebugVS2013** |支持使用 Azure SDK 2.3 从 VS 进行远程调试 | |
| **RemoteDebugVS2012** |支持使用 Azure SDK 2.3 从 VS 进行远程调试 | |
| **WebDeployForVSDevTest** |在 Windows Server 上安装和配置 IIS 和 Web 部署。 不支持删除或禁用它。 | |

## <a name="miscellaneous-features"></a>其他功能
以下扩展为其他可能会很有用的 VM 功能提供支持。

| VM 扩展名称 | 功能说明 | 更多信息 |
| --- | --- | --- |
| **BGInfo** |使用 RDP 时在桌面上显示有用服务器信息的合并图片。 |[BGInfo 扩展](https://msdn.microsoft.com/library/mt589195.aspx) |
| **HpcVmDrivers** |在运行 Windows Server 2012 R2 或 Windows Server 2012 的 A8 或 A9 VM 上，安装、配置和维护远程直接内存访问 (RDMA) 网络设备驱动程序。 运行并行 MPI 应用程序时，支持群集 A8 或 A9 VM 使用 RDMA 网络。 |[关于 A8、A9、A10 和 A11 计算密集型实例](../articles/virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |

