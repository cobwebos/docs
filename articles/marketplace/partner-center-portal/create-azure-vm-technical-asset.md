---
title: 创建 Azure 虚拟机技术资产
description: 了解如何为 Azure 市场的虚拟机 (VM) 产品/服务创建并配置技术资产。
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: e126ee2bd4133281195d4a86c5cb6f1c47bbd6ac
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266910"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>创建 Azure 虚拟机技术资产

本文介绍如何为 Azure 市场的虚拟机 (VM) 产品/服务创建并配置技术资产。 VM 包含两个组件：操作系统虚拟硬盘 (VHD)，以及可选的关联数据磁盘 VHD：

* **操作系统 VHD** - 包含与你的产品/服务一起部署的操作系统和解决方案。 准备 VHD 的过程根据 VM 是基于 Linux、基于 Windows 还是自定义 VM 而有所不同。
* **数据磁盘 VHD** - 适用于 VM 的专用、持久存储。 请不要使用操作系统 VHD（例如 C: 驱动器）来存储持久性信息。

一个 VM 映像包含一个操作系统磁盘以及多达 16 个数据磁盘。 每个数据磁盘使用一个 VHD，即使磁盘为空。

> [!NOTE]
> 无论使用何种操作系统，都仅添加解决方案所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。

> [!IMPORTANT]
> 计划中的每个 VM 映像必须具有相同的数据磁盘数。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成产品/服务的技术方面有一定的专业知识。 除了解决方案领域以外，工程团队还应该了解以下 Microsoft 技术：

* 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
* 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
* [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
* [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
* [JSON](https://www.json.org/) 的实践知识

## <a name="suggested-tools--optional"></a>建议的工具（可选）

考虑使用以下其中一种脚本环境来帮助管理 VM 和 VHD：

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://code.visualstudio.com/)

此外，考虑将以下工具添加到开发环境：

* [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * 扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * 扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * 扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

在 [Azure 开发人员工具](https://azure.microsoft.com/product-categories/developer-tools/)页中查看可用的工具；如果使用的是 Visual Studio，请在 [Visual Studio Marketplace](https://marketplace.visualstudio.com/)中查看。

## <a name="create-a-vm-image-using-an-approved-base"></a>使用批准的基础映像创建 VM 映像

> [!NOTE]
> 若要使用在自己本地构建的映像创建虚拟机技术资产，请参阅[使用自己的映像创建 VM](#create-a-vm-using-your-own-image)。

此部分还介绍使用批准的基础映像的各个方面，例如，使用远程桌面协议 (RDP)、选择 VM 大小、安装最新的 Windows 更新，以及通用化 VHD 映像。

以下部分主要介绍基于 Windows 的 VHD。 有关创建基于 Linux 的 VHD 的详细信息，请参阅 [Azure 认可的 Linux 发行版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

> [!WARNING]
> 请遵照本主题中的指导，使用 Azure 创建包含预配置的认可操作系统的 VM。 如果此内容不符合你的解决方案，可以使用批准的操作系统来创建并配置本地 VM。 然后可以根据[准备好要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，对此 VM 进行配置并准备好上传。

### <a name="select-an-approved-base"></a>选择批准的基础映像

选择 Windows 操作系统或 Linux 作为基础映像。

#### <a name="windows"></a>Windows

用于基于 Windows 的 VM 映像的操作系统 VHD 必须基于 Azure 批准的基础映像（包含 Windows Server 或 SQL Server）。 若要开始，请从 Azure 门户中的以下映像创建 VM：

* Windows Server（[2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016)、[2012 R2 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)、[2012 Datacenter](https://www.microsoft.com/cloud-platform/windows-server-pricing)、[2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)）
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)（Enterprise、Standard、Web）
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)（Enterprise、Standard、Web）

> [!NOTE]
> 如果使用最新 Azure 门户或 Azure PowerShell，则在 2014 年 9 月 8 日及以后发布的 Windows Server 映像已得到批准。

#### <a name="linux"></a>Linux

Azure 提供一系列已批准的 Linux 发行版。 有关最新列表，请参阅 [Azure 认可的 Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-in-the-azure-portal"></a>在 Azure 门户中创建 VM

请按照下列步骤在 [Azure 门户](https://ms.portal.azure.com/)中创建基础 VM 映像。

1. 使用要用于发布 VM 产品/服务的 Azure 订阅关联的 Microsoft 帐户登录到 [Azure 门户](https://ms.portal.azure.com/)。
2. 创建新资源组，并提供**资源组名称**、**订阅**和**资源组位置**。 有关详细信息，请参阅[管理资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。
3. 选择左侧的“虚拟机”，显示“虚拟机详细信息”页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择映像，或单击“浏览所有公共和专用映像”来搜索或浏览所有可用的虚拟机映像。
6. 根据以下建议选择要部署的 VM 的大小：
    * 如果计划在本地开发 VHD，大小则无关紧要。 请考虑使用一个较小的 VM。
    * 如果计划在 Azure 中开发映像，请考虑对所选映像使用一个建议的 VM 大小。

7. 在“磁盘”部分中，展开“高级部分，并将“使用托管磁盘”选项设置为“否”   。
8. 提供创建 VM 所需的其他详细信息。
9. 选择“查看 + 创建”可查看选择。 看到“验证通过”消息时，选择“创建” 。

Azure 随即开始预配所指定的虚拟机。 可以选择左侧的“虚拟机”选项卡来跟踪预配进度。 创建虚拟机后，状态将更改为“正在运行”。

如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅[在创建 VHD 期间出现的常见问题（常见问题解答）](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-issues-during-vhd-creation)。

### <a name="connect-to-your-azure-vm"></a>连接到 Azure VM

本部分介绍如何连接并登录到在 Azure 上创建的 VM。 成功连接后，可以使用该 VM，就如同已本地登录到其主机服务器一样。

#### <a name="connect-to-a-windows-based-vm"></a>连接到基于 Windows 的 VM

使用远程桌面客户端连接到托管在 Azure 上的基于 Windows 的 VM。 大多数 Windows 版本原生包含对远程桌面协议 (RDP) 的支持。 对于其他操作系统，可在[远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)中找到有关客户端的详细信息。

本文详细介绍如何使用内置的 Windows RDP 支持来连接到 VM：[如何连接并登录到运行 Windows 的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 此过程中可能会出现安全警告。 例如，“.rdp 文件来自未知的发布者”或“无法验证你的用户凭据”等警告。 可以放心忽略这些警告。

#### <a name="connect-to-a-linux-based-vm"></a>连接到基于 Linux 的 VM

若要连接到基于 Linux 的 VM，需要一个安全外壳协议 (SSH) 客户端。 以下步骤使用免费的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 终端。

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 搜索并选择“虚拟机”。
3. 选择要连接到的 VM。
4. 如果该 VM 尚未运行，请将它启动。
5. 选择 VM 的名称打开其“概述”页。
6. 请注意 VM 的公共 IP 地址和 DNS 名称（如果未设置这些值，则必须[创建网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)）。
7. 打开 PuTTY 应用程序。
8. 在 PuTTY 的“配置”对话框中，输入 VM 的 IP 地址或 DNS 名称。

    :::image type="content" source="media/avm-putty.png" alt-text="演示 PuTTY 终端设置。突出显示“主机名”或“IP 地址”和“端口”框。":::

9. 选择“打开”以打开 PuTTY 终端。
10. 出现提示时，请输入 Linux VM 帐户的帐户名称和密码。

如果遇到连接问题，请参阅 SSH 客户端的文档。 例如，[第 10 章：常见错误消息](https://www.ssh.com/ssh/putty/putty-manuals)。

有关详细信息，包括如何将桌面添加到预配的 Linux VM，请参阅[安装并配置远程桌面以连接到 Azure 中的 Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)。

## <a name="create-a-vm-using-your-own-image"></a>使用自己的映像创建 VM

本部分介绍如何创建和部署用户提供的虚拟机 (VM) 映像。 为此，你可以提供 Azure 部署的虚拟硬盘 (VHD) 中的操作系统和数据磁盘 VHD 映像。

> [!NOTE]
> 若要选择使用已批准的基础映像，请按照[使用已批准的基础映像创建 VM 映像](#create-a-vm-image-using-an-approved-base)中的说明进行操作。

1. 将映像上传到 Azure 存储帐户。
2. 部署 VM 映像。
3. 捕获 VM 映像。

### <a name="upload-your-images-to-an-azure-storage-account"></a>将映像上传到 Azure 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 将通用操作系统 VHD 和数据磁盘 VHD 上传到 Azure 存储帐户。

### <a name="deploy-your-image"></a>部署映像

使用 Azure 门户或 Azure PowerShell 创建映像。

#### <a name="deploy-using-the-azure-portal"></a>使用 Azure 门户部署

1. 在“主页”上，选择“创建资源”，搜索“模板部署”，然后选择“创建” 。
2. 选择“在编辑器中生成自己的模板”。

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="“自定义部署”页。":::

3. 将此 [JSON 模板](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template)粘贴到编辑器中，然后选择“保存”。
4. 提供显示的“自定义部署”属性页的参数值。

    | 参数 | 说明 |
    | ------------ | ------------- |
    | 用户存储帐户名称 | 单元格 2 中的内容 |
    | 用户存储容器名称 | 通用化 VHD 所在的存储帐户名称 |
    | 公共 IP 的 DNS 名称 | 公共 IP DNS 名称。 在部署该产品/服务后，为 Azure 门户中的公共 IP 地址定义 DNS 名称。 |
    | 管理员用户名 | 新 VM 的管理员帐户的用户名 |
    | 管理员密码 | 新 VM 的管理员帐户的密码 |
    | 操作系统类型 | VM 操作系统：Windows 或 Linux |
    | 订阅 ID | 所选订阅的标识符 |
    | 位置 | 部署的地理位置 |
    | VM 大小 | [Azure VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)，例如 Standard_A2 |
    | 公共 IP 地址名称 | 公共 IP 地址的名称 |
    | VM 名称 | 新 VM 的名称 |
    | 虚拟网络名称 | VM 使用的虚拟网络的名称 |
    | NIC 名称 | 运行虚拟网络的网络接口卡的名称 |
    | VHD URL | 完整的 OS 磁盘 VHD URL |
    |  |  |

5. 提供这些值后，选择“购买”。

Azure 将开始部署。 它将使用指定的非托管 VHD 在指定的存储帐户路径中创建新 VM。 可以选择 Azure 门户左侧的“虚拟机”，在门户中跟踪进度。 创建 VM 后，状态将从“正在启动”更改为“正在运行”。

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

* Azure PowerShell：[如何从 Azure VM 创建非托管 VM 映像](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI：[如何创建虚拟机或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API：[虚拟机 - 捕获](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>配置虚拟机

本部分介绍如何对 Azure VM 进行大小调整、更新和通用化。 必须执行这些步骤才能准备好要在 Azure 市场部署的 VM。

### <a name="sizing-the-vhds"></a>调整 VHD 大小

如果选择了一个已预配置操作系统（以及可选的其他服务）的 VM，则已经选取了一种标准 Azure VM 大小。 使用预先配置的 OS 启动解决方案是建议的方法。 但是，若要手动安装 OS，则必须在 VM 映像中调整主要 VHD 的大小：

* 对于 Windows，操作系统 VHD 应创建为 127-128 GB 的固定格式 VHD。
* 对于 Linux，此 VHD 应创建为 30-50 GB 的固定格式 VHD。

如果物理大小小于 127-128 GB，则 VHD 应可扩展（稀疏/动态）。 提供的基础 Windows 和 SQL Server 映像已满足这些要求；请不要更改 VHD 的格式或大小。

数据磁盘可以大至 1TB。 决定其大小时，请记住，客户无法在部署时对映像中的 VHD 调整大小。 数据磁盘 VHD 应创建为固定格式 VHD。 它们还应可扩展（稀疏/动态）。 数据磁盘最初可以是空的或包含数据。

### <a name="install-the-most-current-updates"></a>安装最新的更新

操作系统 VM 的基础映像必须包含截至发布日期为止的最新更新。 在发布创建的操作系统 VHD 之前，请务必使用所有最新的安全和维护修补程序来更新 OS 和所有已安装的服务。

对于 Windows Server，请运行“检查更新”命令。

对于 Linux 分发版，通常可以通过命令行工具或图形实用工具来下载和安装更新。 例如，Ubuntu Linux 提供 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理器](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具用于更新 OS。

### <a name="perform-additional-security-checks"></a>执行附加的安全检查

为 Azure 市场中的解决方案映像保持高级别的安全性。 以下文章提供了有助于实现此目标的安全配置和过程的清单：[适用于 Azure 市场映像的安全建议](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。 其中的某些建议特定于基于 Linux 的映像，但大部分建议适用于任何 VM 映像。

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

如果需要其他配置，请使用在启动时运行的计划任务，以便在部署 VM 之后对它进行任何最终更改。 另请考虑以下建议：

* 对于一次性运行的任务，在成功完成该任务后，它应自行删除。
* 配置不应依赖于 C 或 D 以外的驱动器，因为只有这两个驱动器始终存在（驱动器 C 是操作系统磁盘，驱动器 D 是临时本地磁盘）。

有关 Linux 自定义项的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

### <a name="windows"></a>Windows

Windows OS 磁盘已使用 [sysprep 工具](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)通用化。 如果后来更新或重新配置了 OS，则必须重新运行 sysprep。

> [!WARNING]
> 由于运行 sysprep 后更新可能自动运行，因此，请在部署 VM 之前将其关闭。 此关闭操作可避免后续更新对操作系统或安装的服务做出特定于实例的更改。 有关运行 sysprep 的详细信息，请参阅[通用化 VHD 的步骤](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="linux"></a>Linux

以下过程将通用化 Linux VM，并将其重新部署为单独的 VM。 有关详细信息，请参阅[如何创建虚拟机或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)。 当到达“从捕获的映像创建 VM”部分时，可以停止。

1. **删除 Azure Linux 代理**

    1. 使用 SSH 客户端连接到 Linux VM。
    2. 在 SSH 窗口中，输入以下命令：`sudo waagent -deprovision+user`。
    3. 键入 Y 以继续操作（可将“-force”参数添加到前一个命令，以避免确认步骤） 。
    d. 该命令完成后，键入“Exit”关闭 SSH 客户端。

2. **停止虚拟机**

    1. 在 Azure 门户，选择资源组 (RG) 并取消分配 VM。
    2. VHD 现已通用化，你可以使用此 VHD 创建新的 VM。

## <a name="next-steps"></a>后续步骤

如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅[在创建 VHD 期间出现的常见问题](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)。

否则：

* [验证 VM 映像](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri)说明了如何测试和提交 VM 映像进行 Azure 市场认证，包括从何处获取用于 Azure 认证的认证测试工具，以及如何使用它来验证 VM 映像。
