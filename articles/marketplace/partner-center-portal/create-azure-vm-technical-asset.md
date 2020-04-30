---
title: 创建 Azure 虚拟机技术资产
description: 了解如何为 Azure Marketplace 创建和配置虚拟机（VM）产品/服务的技术资产。
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 4d2d33f9d83132147b5b257ffcd6d659f272b8ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81730716"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>创建 Azure 虚拟机技术资产

> [!IMPORTANT]
> 我们正在将 Azure 虚拟机产品/服务的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移你的产品/服务之前，请按照为[虚拟机创建技术资产](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets)中的说明进行操作，以便云合作伙伴门户管理你的产品/服务。

本文介绍如何为 Azure Marketplace 创建和配置虚拟机（VM）产品/服务的技术资产。 VM 包含两个组件：操作系统虚拟硬盘（VHD）和可选的关联数据磁盘 Vhd：

* **操作系统 VHD** –包含与你的产品/服务一起部署的操作系统和解决方案。 准备 VHD 的过程会有所不同，具体取决于它是基于 Linux、基于 Windows 的虚拟机还是基于自定义的 VM。
* **数据磁盘 vhd** -VM 的专用持久存储。 不要使用操作系统 VHD （例如，C：驱动器）来存储持久性信息。

VM 映像包含一个操作系统磁盘和多达16个数据磁盘。 每个数据磁盘使用一个 VHD，即使磁盘为空白。

> [!NOTE]
> 不管使用哪种操作系统，只需添加解决方案所需的最小数据磁盘数。 客户无法在部署时删除作为映像一部分的磁盘，但它们始终可以在部署期间或之后添加磁盘。

> [!IMPORTANT]
> 计划中的每个 VM 映像必须具有相同的数据磁盘数。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、构建和测试这些资产需要一定的时间，并需要 Azure 平台和用于构建产品/服务的技术的技术知识。 除解决方案域外，工程团队还应了解以下 Microsoft 技术：

* 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
* 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
* Azure[虚拟机](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 存储](https://azure.microsoft.com/services/?filter=storage)和[azure 网络](https://azure.microsoft.com/services/?filter=networking)的工作知识
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
* [JSON](https://www.json.org/)的工作知识

## <a name="suggested-tools--optional"></a>建议的工具-可选

请考虑使用以下脚本环境之一来帮助管理 Vm 和 Vhd：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

此外，请考虑将以下工具添加到你的开发环境：

* [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

查看[Azure 开发人员工具](https://azure.microsoft.com/product-categories/developer-tools/)页中可用的工具，如果使用的是 Visual Studio， [Visual Studio Marketplace](https://marketplace.visualstudio.com/)。

## <a name="create-a-vm-image-using-an-approved-base"></a>使用已批准的基准创建 VM 映像

> [!NOTE]
> 若要使用在自己的本地构建的映像创建虚拟机技术资产，请参阅[使用自己的映像创建 VM](#create-a-vm-using-your-own-image)。

本部分介绍使用已批准的基础的各个方面，例如，使用远程桌面协议（RDP），选择 VM 的大小，安装最新的 Windows 更新，并使 VHD 映像通用化。

以下部分主要介绍基于 windows 的 Vhd。 有关创建基于 Linux 的 Vhd 的详细信息，请参阅[Azure 认可的分发](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)中的 Linux。

> [!WARNING]
> 按照本主题中的指导使用 Azure 创建包含预配置的已认可操作系统的 VM。 如果这与你的解决方案不兼容，则可以使用已批准的操作系统创建和配置本地 VM。 然后可以根据[准备好要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，对此 VM 进行配置并准备好上传。

### <a name="select-an-approved-base"></a>选择批准的基础映像

选择 Windows 操作系统或 Linux 作为基础。

#### <a name="windows"></a>Windows

基于 Windows 的 VM 映像的操作系统 VHD 必须基于 Azure 批准的基本映像，该映像包含 Windows Server 或 SQL Server。 若要开始，请从 Azure 门户中的以下映像之一创建 VM：

* Windows Server（[2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)）
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （Enterprise、Standard、Web）
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) （企业版、标准版、Web 版）

> [!NOTE]
> 如果使用的是当前 Azure 门户或 Azure PowerShell，则会批准2014和更高版本上发布的 Windows Server 映像。

#### <a name="linux"></a>Linux

Azure 提供一系列经过批准的 Linux 分发。 有关最新列表，请参阅 [Azure 认可的 Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-in-the-azure-portal"></a>在 Azure 门户中创建 VM

按照以下步骤在[Azure 门户](https://ms.portal.azure.com/)中创建基本 VM 映像：

1. 使用与要用于发布 VM 产品/服务的 Azure 订阅关联的 Microsoft 帐户登录到[Azure 门户](https://ms.portal.azure.com/)。
2. 创建新资源组，并提供**资源组名称**、**订阅**和**资源组位置**。 有关详细信息，请参阅[管理资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 选择左侧的 "**虚拟机**" 以显示 "虚拟机详细信息" 页。
4. 选择 " **+ 添加**" 以打开 "**创建虚拟机体验**"。
5. 从下拉列表中选择映像，或者单击 "**浏览所有公用和专用映像**"，搜索或浏览所有可用的虚拟机映像。
6. 根据以下建议选择要部署的 VM 的大小：
    * 如果计划在本地开发 VHD，则大小并不重要。 请考虑使用一个较小的 VM。
    * 如果计划在 Azure 中开发映像，请考虑对所选映像使用一个建议的 VM 大小。

7. 在 "**磁盘**" 部分中，展开 "**高级**" 部分，将 "**使用托管磁盘**" 选项设置为 "**否**"。
8. 提供创建 VM 所需的其他详细信息。
9. 选择 "查看" 和 "**创建**" 以查看你的选择。 看到“验证通过”消息时，选择“创建”   。

Azure 会开始预配指定的虚拟机。 可以通过选择左侧的 "**虚拟机**" 选项卡来跟踪其进度。 创建后，状态将更改为 "**正在运行**"。

如果在创建新的基于 Azure 的 VHD 时遇到困难，请参阅[VHD 创建期间的常见问题（faq）](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation)。

### <a name="connect-to-your-azure-vm"></a>连接到 Azure VM

本部分介绍如何连接到 Azure 上创建的 VM 并登录到该 VM。 成功连接后，可以使用 VM，就像在本地登录到其主机服务器一样。

#### <a name="connect-to-a-windows-based-vm"></a>连接到基于 Windows 的 VM

使用远程桌面客户端连接到 Azure 上托管的基于 Windows 的 VM。 大多数 Windows 版本原生包含对远程桌面协议 (RDP) 的支持。 对于其他操作系统，你可以在[远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)查找有关客户端的详细信息。

本文详细介绍如何使用内置的 Windows RDP 支持来连接到你的 VM：[如何连接并登录到运行 Windows 的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 在此过程中可能会出现安全警告。 例如，".rdp 文件来自未知发布者" 或 "无法验证你的用户凭据" 等警告。 可以放心忽略这些警告。

#### <a name="connect-to-a-linux-based-vm"></a>连接到基于 Linux 的 VM

若要连接到基于 Linux 的 VM，需要使用安全外壳协议（SSH）客户端。 以下步骤使用免费的[PuTTY](https://www.ssh.com/ssh/putty/) SHH 终端。

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 搜索并选择“虚拟机”。 
3. 选择要连接到的 VM。
4. 如果 VM 尚未运行，则启动它。
5. 选择 VM 的名称以打开其 "**概述**" 页。
6. 请注意 VM 的公共 IP 地址和 DNS 名称（如果未设置这些值，则必须[创建网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)）。
7. 打开 PuTTY 应用程序。
8. 在 PuTTY 的“配置”对话框中，输入 VM 的 IP 地址或 DNS 名称。

    :::image type="content" source="media/avm-putty.png" alt-text="说明 PuTTY 终端设置。将突出显示 "主机名" 或 "IP 地址" 和 "端口" 框。":::

9. 选择 "**打开**" 以打开 PuTTY 终端。
10. 出现提示时，请输入 Linux VM 帐户的帐户名和密码。

如果遇到连接问题，请参阅 SSH 客户端的文档。 例如，第[10 章：常见错误消息](https://www.ssh.com/ssh/putty/putty-manuals)。

有关详细信息，包括如何将桌面添加到预配的 Linux VM，请参阅[安装和配置远程桌面以连接到 Azure 中的 LINUX vm](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)。

## <a name="create-a-vm-using-your-own-image"></a>使用自己的映像创建 VM

本部分介绍如何创建和部署用户提供的虚拟机（VM）映像。 为此，可以从 Azure 部署的虚拟硬盘（VHD）提供操作系统和数据磁盘 VHD 映像。

> [!NOTE]
> 若要选择性地使用已批准的基本映像，请按照[使用已批准的基准创建 VM 映像](#create-a-vm-image-using-an-approved-base)中的说明进行操作。

1. 将映像上传到 Azure 存储帐户。
2. 部署 VM 映像。
3. 捕获 VM 映像。

### <a name="upload-your-images-to-an-azure-storage-account"></a>将映像上传到 Azure 存储帐户

1. 登录到[Azure 门户](https://portal.azure.com/)。
2. 将通用操作系统 VHD 和数据磁盘 Vhd 上传到 Azure 存储帐户。

### <a name="deploy-your-image"></a>部署映像

使用 Azure 门户或 Azure PowerShell 创建映像。

#### <a name="deploy-using-the-azure-portal"></a>使用 Azure 门户部署

1. 在主页上，选择 "**创建资源**"，搜索 "模板部署"，然后选择 "**创建**"。
2. 选择 **"在编辑器中生成自己的模板"**。

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="说明 "自定义部署" 页。":::

3. 将此[JSON 模板](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)粘贴到编辑器中，然后选择 "**保存**"。
4. 提供显示的“自定义部署”属性页的参数值。****

    | 参数 | 说明 |
    | ------------ | ------------- |
    | 用户存储帐户名称 | 单元格2中的内容 |
    | 用户存储容器名称 | 通用化 VHD 所在的存储帐户名称 |
    | 公共 IP 的 DNS 名称 | 公共 IP DNS 名称。 在部署产品/服务后，为 Azure 门户中的公共 IP 地址定义 DNS 名称。 |
    | 管理员用户名 | 新 VM 的管理员帐户的用户名 |
    | 管理员密码 | 新 VM 的管理员帐户的密码 |
    | 操作系统类型 | VM 操作系统： Windows 或 Linux |
    | 订阅 ID | 所选订阅的标识符 |
    | 位置 | 部署的地理位置 |
    | VM 大小 | [AZURE VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，例如 Standard_A2 |
    | 公共 IP 地址名称 | 公共 IP 地址的名称 |
    | VM 名称 | 新 VM 的名称 |
    | 虚拟网络名称 | VM 使用的虚拟网络的名称 |
    | NIC 名称 | 运行虚拟网络的网络接口卡的名称 |
    | VHD URL | 完整的 OS 磁盘 VHD URL |
    |  |  |

5. 提供这些值后，选择 "**购买**"。

Azure 将开始部署。 它在指定的存储帐户路径中创建一个具有指定的非托管 VHD 的新 VM。 可以通过在门户左侧选择 "**虚拟机**"，在 Azure 门户中跟踪进度。 创建 VM 后，状态将从 "正在运行" 变为 "正在运行"。

#### <a name="deploy-using-azure-powershell"></a>使用 Azure PowerShell 进行部署

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>捕获 VM 映像

使用与你的方法相对应的以下说明：

* Azure PowerShell：[如何从 AZURE VM 创建非托管 vm 映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI：[如何创建虚拟机或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API：[虚拟机 - 捕获](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>配置虚拟机

本部分介绍如何对 Azure VM 进行大小调整、更新和一般化。 若要将 VM 部署到 Azure Marketplace，必须执行这些步骤。

### <a name="sizing-the-vhds"></a>调整 VHD 大小

如果选择了一个预配置了操作系统的 Vm （还可以选择其他服务），则已选择了标准 Azure VM 大小。 使用预先配置的 OS 启动解决方案是建议的方法。 但是，如果手动安装操作系统，则必须在 VM 映像中调整主要 VHD 的大小：

* 对于 Windows，应将操作系统 VHD 创建为127到 128 GB 的固定格式 VHD。
* 对于 Linux，此 VHD 应创建为30– 50 GB 的固定格式 VHD。

如果物理大小小于127到 128 GB，则 VHD 应可扩展（稀疏/动态）。 提供的基本 Windows 和 SQL Server 映像已经满足这些要求，因此请勿更改 VHD 的格式或大小。

数据磁盘可以大至 1TB。 在确定大小时，请记住，在部署时，客户无法调整映像内的 Vhd 的大小。 数据磁盘 VHD 应创建为固定格式 VHD。 它们还应可扩展（稀疏/动态）。 数据磁盘最初可以是空的或包含数据。

### <a name="install-the-most-current-updates"></a>安装最新的更新

操作系统 Vm 的基本映像必须包含最新的更新，直到发布日期。 在发布所创建的操作系统 VHD 之前，请确保用所有最新的安全和维护修补程序更新操作系统和所有已安装的服务。

对于 Windows Server，请运行 "**检查更新**" 命令。

对于 Linux 分发版，通常可以通过命令行工具或图形实用工具来下载和安装更新。 例如，Ubuntu Linux 提供 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理器](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具用于更新 OS。

### <a name="perform-additional-security-checks"></a>执行附加的安全检查

在 Azure Marketplace 中维护解决方案映像的高级安全。 以下文章提供了安全配置和帮助你： [Azure Marketplace 映像的安全建议](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)列表。 其中的某些建议特定于基于 Linux 的映像，但大部分建议适用于任何 VM 映像。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

如果需要其他配置，请使用在启动时运行的计划任务，以便在部署 VM 之后对它进行任何最终更改。 另请考虑以下建议：

* 如果该任务是一次运行一次的任务，则任务应在成功完成后将其删除。
* 配置不应依赖于 C 或 D 以外的驱动器，因为这两个驱动器始终都存在（驱动器 C 是操作系统磁盘，驱动器 D 是临时本地磁盘）。

有关 Linux 自定义项的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现此目的，必须对操作系统 VHD 进行通用化操作，该操作会从 VM 中删除所有特定于实例的标识符和软件驱动程序。

### <a name="windows"></a>Windows

Windows OS 磁盘已使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)通用化。 如果后来更新或重新配置了 OS，则必须重新运行 sysprep。

> [!WARNING]
> 由于更新可能会自动运行，因此在运行 sysprep 后，请关闭 VM，直到其部署完成。 此关闭将避免后续更新对操作系统或已安装的服务进行实例特定的更改。 有关运行 sysprep 的详细信息，请参阅[通用化 VHD 的步骤](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="linux"></a>Linux

以下过程通用化 Linux VM，并将其重新部署为单独的 VM。 有关详细信息，请参阅[如何创建虚拟机或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)。 当到达 "从捕获的映像创建 VM" 部分时，可以停止。

1. **删除 Azure Linux 代理**

    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入以下命令： `sudo waagent -deprovision+user`。
    3. 键入**Y**继续（可以将 **-force**参数添加到前一个命令，以避免确认步骤）。
    d. 命令完成后，键入**Exit**以关闭 SSH 客户端。

2. **停止虚拟机**

    1. 在 Azure 门户中，选择资源组（RG）并取消分配 VM。
    2. 你的 VHD 现已通用化，你可以使用此 VHD 创建新的 VM。

## <a name="next-steps"></a>后续步骤

如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅[在创建 VHD 期间出现的常见问题](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)。

否则：

* [验证 vm 映像](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri)介绍了如何测试和提交 azure Marketplace 认证的 vm 映像，包括在何处获取*azure 认证工具的认证测试工具*，以及如何使用它来验证 vm 映像。
