---
title: 创建 Azure 虚拟机技术资产
description: 了解如何为 Azure 应用商店创建和配置虚拟机 （VM） 产品/服务的技术资产。
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: eb885d0e965579b1ab2d66395f9f96eab0845bae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266495"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>创建 Azure 虚拟机技术资产

> [!IMPORTANT]
> 我们将 Azure 虚拟机产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照[云合作伙伴门户的虚拟机产品/服务创建技术资产](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets)以管理您的产品/服务的说明进行操作。

本文介绍如何为 Azure 应用商店创建和配置虚拟机 （VM） 产品/服务的技术资产。 VM 包含两个组件：操作系统虚拟硬盘 （VHD） 和可选关联数据磁盘 VHD：

* **操作系统 VHD** – 包含随产品/服务部署的操作系统和解决方案。 准备 VHD 的过程因它是基于 Linux 的、基于 Windows 的还是基于自定义的 VM而异。
* **数据磁盘 VHD** - VM 专用的持久存储。 不要使用操作系统 VHD（例如，C： 驱动器）来存储持久性信息。

VM 映像包含一个操作系统磁盘和最多 16 个数据磁盘。 即使磁盘为空，也使用每个数据磁盘的一个 VHD。

> [!NOTE]
> 无论您使用哪种操作系统，都只添加解决方案所需的最小数据磁盘数。 客户无法在部署时删除属于映像的磁盘，但始终可以在部署期间或部署后添加磁盘。

> [!IMPORTANT]
> 计划中的每个 VM 映像必须具有相同的数据磁盘数。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、构建和测试这些资产需要时间，需要对 Azure 平台和用于构建产品/服务的技术进行技术知识。 除了解决方案领域之外，您的工程团队还应了解以下 Microsoft 技术：

* 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
* 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
* [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)[、Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 Azure[网络](https://azure.microsoft.com/services/?filter=networking)的工作知识
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
* [JSON](https://www.json.org/)的工作知识

## <a name="suggested-tools--optional"></a>建议的工具 = 可选

请考虑使用以下脚本环境之一来帮助管理 VM 和 VHD：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

此外，请考虑将以下工具添加到开发环境：

* [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

查看[Azure 开发人员工具](https://azure.microsoft.com/product-categories/developer-tools/)页中的可用工具，如果使用 Visual Studio，则查看[可视化工作室应用商店](https://marketplace.visualstudio.com/)。

## <a name="create-a-vm-image-using-an-approved-base"></a>使用已批准的基创建 VM 映像

> [!NOTE]
> 要使用在自己内部构建的映像创建虚拟机技术资产，请转到[使用自己的映像创建 VM。](#create-a-vm-using-your-own-image)

本节介绍使用已批准的基座的各个方面，例如使用远程桌面协议 （RDP）、为 VM 选择大小、安装最新的 Windows 更新以及通用 VHD 映像。

以下各节主要侧重于基于窗口的 VHD。 有关创建基于 Linux 的 VHD 的详细信息，请参阅[Azure 认可的发行版上的 Linux。](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

> [!WARNING]
> 按照本主题中的指南，使用 Azure 创建包含预配置、认可的操作系统的 VM。 如果这与解决方案不兼容，则可以使用经批准的操作系统创建和配置本地 VM。 然后可以根据[准备好要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，对此 VM 进行配置并准备好上传。

### <a name="select-an-approved-base"></a>选择批准的基础映像

选择 Windows 操作系统或 Linux 作为基础。

#### <a name="windows"></a>Windows

基于 Windows 的 VM 映像的操作系统 VHD 必须基于包含 Windows 服务器或 SQL Server 的 Azure 批准的基本映像。 首先，从 Azure 门户中的以下映像之一创建 VM：

* Windows Server（[2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)）
* [SQL 服务器 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （企业， 标准， Web）
* [SQL 服务器 2012 SP2（](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)企业、标准、Web）

> [!NOTE]
> 如果您使用的是当前 Azure 门户或 Azure PowerShell，则 2014 年 9 月 8 日及以后发布的 Windows 服务器映像将获得批准。

#### <a name="linux"></a>Linux

Azure 提供了一系列已批准的 Linux 发行版。 有关最新列表，请参阅 [Azure 认可的 Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-in-the-azure-portal"></a>在 Azure 门户中创建 VM

按照以下步骤在[Azure 门户](https://ms.portal.azure.com/)中创建基本 VM 映像：

1. 使用与要用于发布 VM 产品/服务的 Azure 订阅关联的 Microsoft 帐户登录到[Azure 门户](https://ms.portal.azure.com/)。
2. 创建新资源组，并提供**资源组名称**、**订阅**和**资源组位置**。 有关详细信息，请参阅[管理资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 选择左侧的**虚拟机**以显示虚拟机详细信息页。
4. 选择 **+ 添加**以打开**虚拟机体验**。
5. 从下拉列表中选择图像或单击 **"浏览所有公共和私有映像**"以搜索或浏览所有可用的虚拟机映像。
6. 根据以下建议选择要部署的 VM 的大小：
    * 如果您计划在本地开发 VHD，则大小并不重要。 请考虑使用一个较小的 VM。
    * 如果计划在 Azure 中开发映像，请考虑对所选映像使用一个建议的 VM 大小。

7. 在 **"磁盘"** 部分中，展开 **"高级**"部分，并将 **"使用托管磁盘"** 选项设置为 **"否**"。
8. 提供创建 VM 所需的其他详细信息。
9. 选择 **"查看 + 创建"** 以查看您的选择。 看到“验证通过”消息时，选择“创建”   。

Azure 开始预配指定的虚拟机。 您可以通过选择左侧的 **"虚拟机"** 选项卡来跟踪其进度。 创建后，状态将更改为 **"正在运行**"。

如果在创建新基于 Azure 的 VHD 时遇到困难，请参阅[VHD 创建期间的常见问题 （FAQ）。](https://aka.ms/VHDcreationIssues)

### <a name="connect-to-your-azure-vm"></a>连接到 Azure VM

本节介绍如何连接到在 Azure 上创建的 VM 并登录到 VM。 成功连接后，可以使用 VM，就像您本地登录到其主机服务器一样。

#### <a name="connect-to-a-windows-based-vm"></a>连接到基于 Windows 的 VM

使用远程桌面客户端连接到 Azure 上托管的基于 Windows 的 VM。 大多数 Windows 版本原生包含对远程桌面协议 (RDP) 的支持。 对于其他操作系统，您可以在[远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)中找到有关客户端的详细信息。

本文详细介绍了如何使用内置 Windows RDP 支持连接到 VM：[如何连接到运行 Windows 的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 在此过程中，您可能会收到安全警告。 例如，诸如".rdp 文件来自未知发布者"或"无法验证您的用户凭据"等警告。 可以放心忽略这些警告。

#### <a name="connect-to-a-linux-based-vm"></a>连接到基于 Linux 的 VM

要连接到基于 Linux 的 VM，您需要一个安全的 shell 协议 （SSH） 客户端。 以下步骤使用免费[的 PuTTY](https://www.ssh.com/ssh/putty/) SHH 端子。

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 搜索并选择**虚拟机**。
3. 选择要连接到的 VM。
4. 如果 VM 尚未运行，则启动它。
5. 选择 VM 的名称以打开其 **"概述"** 页。
6. 请注意 VM 的公共 IP 地址和 DNS 名称（如果未设置这些值，则必须[创建网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)）。
7. 打开 PuTTY 应用程序。
8. 在 PuTTY 的“配置”对话框中，输入 VM 的 IP 地址或 DNS 名称。

    :::image type="content" source="media/avm-putty.png" alt-text="说明 PuTTY 终端设置。主机名或 IP 地址和端口框将突出显示。":::

9. 选择 **"打开**"以打开 PuTTY 终端。
10. 出现提示后，输入 Linux VM 帐户的帐户名称和密码。

如果存在连接问题，请参阅 SSH 客户端的文档。 例如，[第 10 章：常见错误消息](https://www.ssh.com/ssh/putty/putty-manuals)。

有关详细信息（包括如何将桌面添加到预配的 Linux VM），请参阅[安装和配置远程桌面以连接到 Azure 中的 Linux VM。](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)

## <a name="create-a-vm-using-your-own-image"></a>使用您自己的映像创建 VM

本节介绍如何创建和部署用户提供的虚拟机 （VM） 映像。 可以通过从 Azure 部署的虚拟硬盘 （VHD） 提供操作系统和数据磁盘 VHD 映像来执行此操作。

> [!NOTE]
> 要选择使用已批准的基本映像，请[按照使用已批准的基](#create-a-vm-image-using-an-approved-base)创建 VM 映像中的说明进行操作。

1. 将映像上载到 Azure 存储帐户。
2. 部署 VM 映像。
3. 捕获 VM 映像。

### <a name="upload-your-images-to-an-azure-storage-account"></a>将映像上载到 Azure 存储帐户

1. 登录到 Azure[门户](https://portal.azure.com/)。
2. 将通用操作系统 VHD 和数据磁盘 VHD 上载到 Azure 存储帐户。

### <a name="deploy-your-image"></a>部署映像

使用 Azure 门户或 Azure PowerShell 创建映像。

#### <a name="deploy-using-the-azure-portal"></a>使用 Azure 门户部署

1. 在主页上，选择 **"创建资源**"，搜索"模板部署"，然后选择"**创建**"。
2. 在**编辑器中选择"构建您自己的模板**"。

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="说明自定义部署页。":::

3. 将此[JSON 模板](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)粘贴到编辑器中，然后选择 **"保存**"。
4. 提供显示的“自定义部署”属性页的参数值。****

    | 参数 | 描述 |
    | ------------ | ------------- |
    | 用户存储帐户名称 | 单元格 2 中的内容 |
    | 用户存储容器名称 | 通用化 VHD 所在的存储帐户名称 |
    | 公共 IP 的 DNS 名称 | 公共 IP DNS 名称。 部署产品/服务后，在 Azure 门户中定义公共 IP 地址的 DNS 名称。 |
    | 管理员用户名 | 新 VM 的管理员帐户的用户名 |
    | 管理员密码 | 新 VM 的管理员帐户的密码 |
    | 操作系统类型 | VM 操作系统：Windows 或 Linux |
    | 订阅 ID | 所选订阅的标识符 |
    | 位置 | 部署的地理位置 |
    | VM 大小 | [Azure VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，例如Standard_A2 |
    | 公共 IP 地址名称 | 公共 IP 地址的名称 |
    | VM 名称 | 新 VM 的名称 |
    | 虚拟网络名称 | VM 使用的虚拟网络的名称 |
    | NIC 名称 | 运行虚拟网络的网络接口卡的名称 |
    | VHD URL | 完整的 OS 磁盘 VHD URL |
    |  |  |

5. 提供这些值后，选择 **"购买**"。

Azure 将开始部署。 它在指定的存储帐户路径中创建具有指定非托管 VHD 的新 VM。 您可以通过选择门户左侧的**虚拟机**来跟踪 Azure 门户中的进度。 创建 VM 时，状态将从"开始"更改为"正在运行"。

#### <a name="deploy-using-azure-powershell"></a>使用 Azure PowerShell 进行部署

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>捕获 VM 映像

使用与方法对应的以下说明：

* Azure PowerShell：[如何从 Azure VM 创建非托管 VM 映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI：[如何创建虚拟机或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API：[虚拟机 - 捕获](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>配置虚拟机

本节介绍如何调整、更新和概括 Azure VM。 这些步骤对于准备要部署在 Azure 应用商店中的 VM 是必需的。

### <a name="sizing-the-vhds"></a>调整 VHD 大小

如果选择了预配置操作系统的 VM 之一（以及可选的附加服务），则已选择标准 Azure VM 大小。 使用预先配置的 OS 启动解决方案是建议的方法。 但是，如果要手动安装操作系统，则必须在 VM 映像中调整主 VHD 的大小：

* 对于 Windows，操作系统 VHD 应创建为 127-128 GB 固定格式 VHD。
* 对于 Linux，此 VHD 应创建为 30-50 GB 固定格式 VHD。

如果物理大小小于 127-128 GB，则 VHD 应可扩展（稀疏/动态）。 提供的基本 Windows 和 SQL Server 映像已经满足这些要求，因此不要更改 VHD 的格式或大小。

数据磁盘可以大至 1TB。 在决定大小时，请记住，在部署时，客户无法调整映像中的 VHD 大小。 数据磁盘 VHD 应创建为固定格式 VHD。 它们也应该是可扩展的（稀疏/动态）。 数据磁盘最初可以是空的或包含数据。

### <a name="install-the-most-current-updates"></a>安装最新的更新

操作系统 VM 的基本映像必须包含截至其发布日期的最新更新。 在发布您创建的操作系统 VHD 之前，请确保使用所有最新的安全和维护修补程序更新操作系统和所有已安装的服务。

对于 Windows 服务器，运行 **"检查更新"** 命令。

对于 Linux 分发版，通常可以通过命令行工具或图形实用工具来下载和安装更新。 例如，Ubuntu Linux 提供 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理器](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具用于更新 OS。

### <a name="perform-additional-security-checks"></a>执行附加的安全检查

在 Azure 应用商店中维护解决方案映像的高度安全性。 以下文章提供了安全配置和过程的清单，以帮助您[：Azure 应用商店映像的安全建议](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。 其中的某些建议特定于基于 Linux 的映像，但大部分建议适用于任何 VM 映像。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

如果需要其他配置，请使用在启动时运行的计划任务，在部署 VM 后对其进行任何最终更改。 另请考虑以下建议：

* 如果它是一次运行任务，则任务应在成功完成后删除自身。
* 配置不应依赖于 C 或 D 以外的驱动器，因为始终保证只有这两个驱动器存在（驱动器 C 是操作系统磁盘，驱动器 D 是临时本地磁盘）。

有关 Linux 自定义项的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 为此，必须对操作系统 VHD 进行通用化，该操作从 VM 中删除所有特定于实例的标识符和软件驱动程序。

### <a name="windows"></a>Windows

Windows OS 磁盘已使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)通用化。 如果后来更新或重新配置了 OS，则必须重新运行 sysprep。

> [!WARNING]
> 由于更新可能会自动运行，因此在运行 sysprep 后，请关闭 VM，直到其部署。 此关机将避免后续更新对操作系统或已安装的服务进行特定于实例的更改。 有关运行 sysprep 的详细信息，请参阅[概括 VHD 的步骤](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="linux"></a>Linux

以下过程将 Linux VM 概括为一个 Vm，并将其重新部署为单独的 VM。 有关详细信息，请参阅[如何创建虚拟机或 VHD 的图像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)。 当您到达"从捕获的图像创建 VM"部分时，可以停止。

1. **删除 Azure Linux 代理**

    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入以下命令： `sudo waagent -deprovision+user`。
    3. **类型 Y**继续（您可以将 **-force**参数添加到上一个命令以避免确认步骤）。
    d. 命令完成后，键入**Exit**以关闭 SSH 客户端。

2. **停止虚拟机**

    1. 在 Azure 门户中，选择资源组 （RG） 并取消分配 VM。
    2. 您的 VHD 现已通用化，您可以使用此 VHD 创建新 VM。

## <a name="next-steps"></a>后续步骤

如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅[在创建 VHD 期间出现的常见问题](https://aka.ms/AzureVM_VHDCreationFAQ)。

否则：

* [验证 VM 映像](https://aks.ms/CertifyVMimage)说明如何测试和提交 VM 映像以进行 Azure 应用商店认证，包括获取*Azure 认证工具的认证测试工具*的位置以及如何使用它来验证 VM 映像。
