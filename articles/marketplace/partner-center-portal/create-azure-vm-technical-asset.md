---
title: 为 Azure Marketplace 虚拟机产品/服务创建技术资产
description: 了解如何为 Azure 市场的虚拟机 (VM) 产品/服务创建并配置技术资产。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: a83532e2dd6fc8e83206a3b4a055170b40d131fd
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803511"
---
# <a name="create-technical-assets-for-an-azure-marketplace-virtual-machine-offer"></a>为 Azure Marketplace 虚拟机产品/服务创建技术资产

向 Azure Marketplace 发布虚拟机 (VM) 映像时，Azure 团队将验证 VM 映像，确保其 bootability、安全性和 Azure 兼容性。 如果任何高质量测试失败，则发布将失败并出现一条消息，其中包含错误和可能的 [纠正步骤](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions)。

本文介绍如何为 Azure 市场的虚拟机 (VM) 产品/服务创建并配置技术资产。 VM 包含两个组件：操作系统虚拟硬盘 (VHD)，以及可选的关联数据磁盘 VHD：

- **操作系统 VHD**：包含与产品/服务一起部署的操作系统和解决方案。 准备 VHD 的过程会有所不同，具体取决于它是基于 Linux 的、基于 Windows 的虚拟机还是基于自定义的 VM。

- **数据磁盘 vhd**： VM 的专用持久存储。 请不要使用操作系统 VHD（例如 C: 驱动器）来存储持久性信息。

一个 VM 映像包含一个操作系统磁盘以及多达 16 个数据磁盘。 每个数据磁盘使用一个 VHD，即使磁盘为空。

> [!NOTE]
> 无论使用何种操作系统，都仅添加解决方案所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。

> [!IMPORTANT]
> 计划中的每个 VM 映像必须具有相同的数据磁盘数。

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、生成和测试这些资产需要一些时间，并在 Azure 平台和用于生成产品/服务的技术方面有一定的专业知识。 除了解决方案领域以外，工程团队还应该了解以下 Microsoft 技术：

- 基本了解 [Azure 服务](https://azure.microsoft.com/services/)
- 如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
- [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
- [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
- [JSON](https://www.json.org/) 的实践知识

### <a name="optional-suggested-tools"></a>可选建议的工具

考虑使用以下其中一种脚本环境来帮助管理 VM 和 VHD：

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://code.visualstudio.com/)

此外，考虑将以下工具添加到开发环境：

- [Azure 存储浏览器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>使用批准的基础映像创建 VM 映像

若要使用在自己的本地构建的映像创建虚拟机技术资产，请参阅下面 [的使用批准的基础创建 VM 映像](#create-a-vm-image-using-an-approved-base) 。

此部分还介绍使用批准的基础映像的各个方面，例如，使用远程桌面协议 (RDP)、选择 VM 大小、安装最新的 Windows 更新，以及通用化 VHD 映像。

按照本文中的指导使用 Azure 创建包含预配置的已认可操作系统的 VM。 如果此内容不符合你的解决方案，可以使用批准的操作系统来创建并配置本地 VM。 然后可以根据[准备好要上传到 Azure 的 Windows VHD 或 VHDX](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中所述，对此 VM 进行配置并准备好上传。

### <a name="select-an-approved-base-image"></a>选择已批准的基本映像

选择 Windows 操作系统或 Linux 作为基础映像。

用于 VM 映像的操作系统 VHD 必须基于 Azure 批准的基本映像（包含 Windows Server 或 SQL Server）。

提交请求以重新发布包含更新的映像时，部件号验证测试用例可能会失败。 在该实例中，你的映像将不会获得批准。

当你使用属于另一发布服务器的基本映像并且已更新该映像时，将发生此错误。 在这种情况下，不允许发布映像。

若要解决此问题，请从 Azure Marketplace 检索最新的映像，并对该映像进行更改。 请参阅以下内容，查看可在其中搜索图像的已批准基准映像：

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2012 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)、 [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)) 
- SQL Server 2019 ([企业版](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)、 [标准](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)版、 [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview)) 
- SQL Server 2014 ([企业版](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、 [标准](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)版、 [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)) 
- SQL Server 2012 SP2 ([企业版](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)、 [标准](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)版、 [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance)) 

#### <a name="linux"></a>Linux

Azure 提供一系列已批准的 Linux 发行版。 有关最新列表，请参阅 [Azure 认可的 Linux 分发版](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)。

### <a name="create-vm-on-the-azure-portal"></a>在 Azure 门户上创建 VM

按照以下步骤在 [Azure 门户](https://ms.portal.azure.com/)上创建基本 VM 映像：

1. 使用要用于发布 VM 产品/服务的 Azure 订阅关联的 Microsoft 帐户登录到 [Azure 门户](https://ms.portal.azure.com/)。
2. 创建新资源组，并提供**资源组名称**、**订阅**和**资源组位置**。 有关详细信息，请参阅[管理资源](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)。

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="显示创建资源组的开始。&quot;:::

3. 选择左侧导航栏中的 &quot; **虚拟机** &quot; 以显示 &quot;虚拟机详细信息&quot; 页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择图像，或选择 " **浏览所有公用和专用映像** "，搜索或浏览所有可用的虚拟机映像。 示例：

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="显示创建资源组的开始。&quot;:::

3. 选择左侧导航栏中的 &quot; **虚拟机** &quot; 以显示 &quot;虚拟机详细信息&quot; 页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择图像，或选择 ":::

6. 根据以下建议选择要部署的 VM 的大小：
    1. 如果计划在本地开发 VHD，大小则无关紧要。 请考虑使用一个较小的 VM。
    2. 如果计划在 Azure 中开发映像，请考虑对所选映像使用一个建议的 VM 大小。

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="显示创建资源组的开始。&quot;:::

3. 选择左侧导航栏中的 &quot; **虚拟机** &quot; 以显示 &quot;虚拟机详细信息&quot; 页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择图像，或选择 ":::

7. 在“磁盘”部分中，展开“高级部分，并将“使用托管磁盘”选项设置为“否”   。

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="显示创建资源组的开始。&quot;:::

3. 选择左侧导航栏中的 &quot; **虚拟机** &quot; 以显示 &quot;虚拟机详细信息&quot; 页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择图像，或选择 ":::

8. 提供创建 VM 所需的其他详细信息。
9. 选择“查看 + 创建”可查看选择。 看到“验证通过”消息时，选择“创建” 。

Azure 随即开始预配所指定的虚拟机。 可以选择左侧的“虚拟机”选项卡来跟踪预配进度。 创建虚拟机后，状态将更改为“正在运行”。

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>在 Azure 门户上创建第2代 VM

在 Azure 门户中创建第2代 (Gen2) VM。

1. 在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。
2. 选择“创建资源”。 
3. 选择左侧的 "从 Azure Marketplace **查看全部** "。
4. 选择支持 Gen2 的映像。
5. 选择“创建”。
6. 在“高级”选项卡的“VM 代系”部分下，选择“Gen 2”选项。
7. 在“基本信息”选项卡的“实例详细信息”下，转到“大小”并打开“选择 VM 大小”边栏选项卡。
8. 选择 [受支持的第2代 VM](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) 和大小的建议大小。
9. 通过 [Azure 门户创建流](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)完成 VM 的创建。

    :::image type="content" source="media/vm/vm-generation.png" alt-text="显示创建资源组的开始。&quot;:::

3. 选择左侧导航栏中的 &quot; **虚拟机** &quot; 以显示 &quot;虚拟机详细信息&quot; 页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择图像，或选择 ":::

## <a name="connect-to-your-azure-vm"></a>连接到 Azure VM

本部分介绍如何连接到在 Azure 中创建的 VM 并登录到该 VM。 成功连接后，可以使用该 VM，就如同已本地登录到其主机服务器一样。

### <a name="connect-to-a-windows-based-vm"></a>连接到基于 Windows 的 VM

使用远程桌面客户端连接到托管在 Azure 上的基于 Windows 的 VM。 大多数 Windows 版本原生包含对远程桌面协议 (RDP) 的支持。 对于其他操作系统，可在[远程桌面客户端](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)中找到有关客户端的详细信息。

本文详细介绍如何使用内置的 Windows RDP 支持来连接到你的 VM： [如何连接并登录到运行 Windows 的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon)。

> [!TIP]
> 此过程中可能会出现安全警告。 例如，“.rdp 文件来自未知的发布者”或“无法验证你的用户凭据”等警告。 可以放心忽略这些警告。

### <a name="connect-to-a-linux-based-vm"></a>连接到基于 Linux 的 VM

若要连接到基于 Linux 的 VM，需要一个安全外壳协议 (SSH) 客户端。 以下步骤使用免费的 [PuTTY](https://www.ssh.com/ssh/putty/) SHH 终端。

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
2. 搜索并选择“虚拟机”。
3. 选择要连接到的 VM。
4. 如果该 VM 尚未运行，请将它启动。
5. 选择 VM 的名称打开其“概述”页。
6. 请注意 (VM 的公共 IP 地址和 DNS 名称如果未设置这些值，则必须 [创建网络接口](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)。
7. 打开 PuTTY 应用程序。
8. 在 PuTTY 的“配置”对话框中，输入 VM 的 IP 地址或 DNS 名称。

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="显示创建资源组的开始。&quot;:::

3. 选择左侧导航栏中的 &quot; **虚拟机** &quot; 以显示 &quot;虚拟机详细信息&quot; 页。
4. 选择“+ 添加”以打开“创建虚拟机体验” 。
5. 从下拉列表中选择图像，或选择 ":::

9. 选择“打开”以打开 PuTTY 终端。
10. 出现提示时，请输入 Linux VM 帐户的帐户名称和密码。

## <a name="configure-the-virtual-machine"></a>配置虚拟机

本部分介绍如何对 Azure VM 进行大小调整、更新和通用化。 必须执行这些步骤才能准备好要在 Azure 市场部署的 VM。

### <a name="sizing-the-vhds"></a>调整 VHD 大小

以下规则适用于操作系统磁盘大小的限制。 提交任何请求时，请确保 OS 磁盘大小在 Linux 或 Windows 的限制范围内。

| OS | 推荐的 VHD 大小 |
| --- | --- |
| Linux | 30到 1023 GB |
| Windows | 30到 250 GB |

虚拟机允许访问基础操作系统时，请确保 VHD 大小对于 VHD 足够大。 由于磁盘无法在无停机时间的情况下扩展，因此请使用介于30到 50 GB 之间的磁盘大小 &nbsp; 。

| VHD 大小 | 实际占用的大小 | 解决方案 |
| --- | --- | --- |
| >500 TB | 不适用 | 请联系支持团队以获取异常批准。 |
| 250-500 TB | >200 GB 不同于 blob 大小 | 请联系支持团队以获取异常批准。 |

### <a name="install-the-most-current-updates"></a>安装最新的更新

操作系统 VM 的基础映像必须包含截至发布日期为止的最新更新。 在发布创建的操作系统 VHD 之前，请务必使用所有最新的安全和维护修补程序来更新 OS 和所有已安装的服务。

- 对于 Windows Server，请运行“检查更新”命令。
- 对于 Linux 分发版，通常可以通过命令行工具或图形实用工具来下载和安装更新。 例如，Ubuntu Linux 提供 [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) 命令和[更新管理器](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html)工具用于更新 OS。

#### <a name="perform-additional-security-checks"></a>执行附加的安全检查

为 Azure 市场中的解决方案映像保持高级别的安全性。 有关安全配置和过程的清单，请参阅 [Azure Marketplace 映像的安全建议](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)。 其中的某些建议特定于基于 Linux 的映像，但大部分建议适用于任何 VM 映像。

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>执行自定义配置和计划任务

如果需要其他配置，请使用在启动时运行的计划任务，以便在部署 VM 后对其进行最终更改。 也请考虑以下要求：

- 对于一次性运行的任务，在成功完成该任务后，它应自行删除。
- 配置不应依赖于 C 或 D 以外的驱动器，因为只有这两个驱动器始终存在（驱动器 C 是操作系统磁盘，驱动器 D 是临时本地磁盘）。

有关 Linux 自定义项的详细信息，请参阅[适用于 Linux 的虚拟机扩展和功能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)。

## <a name="generalize-the-image"></a>通用化映像

Azure 市场中的所有映像必须可采用一般形式重复使用。 若要实现这种可重用性，必须将操作系统 VHD 通用化，此操作会从 VM 中删除所有特定于实例的标识符以及软件驱动程序。

### <a name="for-windows"></a>对于 Windows

Windows OS 磁盘是利用 [sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) 工具通用化的。 如果以后更新或重新配置了操作系统，则必须再次运行 sysprep。

> [!WARNING]
> 运行 sysprep 后，将 VM 关闭，直到部署完成，因为更新可能会自动运行。 此关闭操作可避免后续更新对操作系统或安装的服务做出特定于实例的更改。 有关运行 sysprep 的详细信息，请参阅[通用化 VHD 的步骤](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)。

### <a name="for-linux"></a>对于 Linux

以下过程将通用化 Linux VM，并将其重新部署为单独的 VM。 有关详细信息，请参阅[如何创建虚拟机或 VHD 的映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)。 当你到达称为 "从捕获的映像创建 VM" 的部分时，你可以停止。

1. 删除 Azure Linux 代理

    1. 使用 SSH 客户端连接到 Linux VM
    2. 在 SSH 窗口中，输入以下命令：`sudo waagent –deprovision+user`。
    3. 键入 Y 以继续操作（可将“-force”参数添加到前一个命令，以避免确认步骤） 。
    4. 该命令完成后，键入“Exit”关闭 SSH 客户端。

2. 停止虚拟机

    1. 在 Azure 门户，选择资源组 (RG) 并取消分配 VM。
    2. VHD 现已通用化，你可以使用此 VHD 创建新的 VM。

## <a name="next-steps"></a>后续步骤

- 如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅[在创建 VHD 期间出现的常见问题](common-issues-during-vhd-creation.md)。
- 如果没有，则 [从 VHD 部署的测试虚拟机 (vm) ](azure-vm-image-certification.md) 介绍了如何测试和提交 azure Marketplace 认证的 vm 映像，包括在何处获取适用于 azure 认证工具的认证测试工具，以及如何使用它来验证 vm 映像。
