---
title: 将虚拟机设置 Azure 门户
description: 本操作指南介绍用于在 Azure 门户中创建 Windows SQL Server 2017 虚拟机的选项。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: 1aff691f-a40a-4de2-b6a0-def1384e086e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 11/07/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 1fdf776570b6f10a363fb98dfe343387d86219d6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389063"
---
# <a name="how-to-provision-a-windows-sql-server-virtual-machine-in-the-azure-portal"></a>如何在 Azure 门户中预配 Windows SQL Server 虚拟机

本指南详细介绍在 Azure 门户中创建 Windows SQL Server 虚拟机时可用的不同选项。 本文介绍更多 [SQL Server VM 快速入门](quickstart-sql-vm-create-portal.md)中未提及的配置选项，为单个预配任务提供更丰富的选择。 

借助本指南创建你自己的 SQL Server VM。 或使用它作为参考，了解 Azure 门户中可用的选项。

> [!TIP]
> 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a id="select"></a>SQL Server 虚拟机库映像

创建 SQL Server 虚拟机时，可从虚拟机库中选择一个预配置的映像。 以下步骤演示如何选择一个 SQL Server 2017 映像。

1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果**AZURE sql**不在列表中，请选择 "**所有服务**"，然后在搜索框中键入 "Azure sql"。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 您可以通过选择 "**显示详细**信息" 查看其他信息。 
1. 在 " **SQL 虚拟机**" 磁贴的 "SQL Server 图像搜索" 框中键入 `2017`，然后在下拉框中选择 "**免费 SQL Server 许可证 SQL Server： Windows Server 2016 上的2017开发人员**"。 


   ![选择 SQL VM 映像](media/virtual-machines-windows-portal-sql-server-provision/select-sql-vm-image-portal.png)

   > [!TIP]
   > 本演示使用 Developer（开发人员）版，因为该版本是 SQL Server 的完整功能版本，并且可免费用于开发测试。 只需支付运行 VM 的成本。 但是，本教程中使用的映像可随意选择。 有关可用映像的说明，请参阅 [SQL Server Microsoft 虚拟机概述](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo)。

   > [!TIP]
   > SQL Server 的许可费用包含在所创建的 VM 的每秒定价中，并随版本和内核而有所不同。 但是，SQL Server 开发人员版可免费用于开发/测试（不可用于生产），而 SQL Express 可免费用于轻型工作负荷（1 GB 内存以内，10 GB 存储以内）。 还可选择自带许可 (BYOL)，只支付 VM 费用。 这些映像名称的前缀为 {BYOL}。 
   >
   > 有关这些选项的详细信息，请参阅 [SQL Server Azure VM 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)。


1. 选择“创建”。


## <a name="1-configure-basic-settings"></a>1. 配置基本设置


在“基本信息”选项卡中提供以下信息：

* 在 "**项目详细信息**" 下，确保选择了正确的订阅。 
*  在 "**资源组**" 部分中，从列表中选择现有的资源组，或选择 "**新建**" 以创建新的资源组。 资源组是 Azure（虚拟机、存储帐户、虚拟网络等）中相关资源的集合。 

    ![订阅](media/quickstart-sql-vm-create-portal/basics-project-details.png)

  > [!NOTE]
  > 如果只是测试或了解 Azure 中的 SQL Server 部署，使用新的资源组很有帮助。 测试完成后，删除资源组会自动删除 VM 和与该资源组相关联的所有资源。 有关资源组的详细信息，请参阅 [Azure 资源管理器概述](../../../azure-resource-manager/management/overview.md)。


* 在“实例详细信息”下：
    1. 输入唯一的**虚拟机名称**。  
    1. 为“区域”选择一个位置。 
    1. 出于本指南的目的，请将 "**可用性选项**" 设置为 "_无需基础结构冗余_"。 有关可用性选项的详细信息，请参阅[可用性](../../windows/availability.md)。 
    1. 在 "**映像**" 列表中，选择 "_免费 SQL Server 许可证： Windows Server 2016 上的 SQL Server 2017 开发人员_"。  
    1. 对于虚拟机**大小**，请选择“更改大小”，然后选择“A2 基本”产品/服务。 用完资源后，请务必清理资源，以免产生任何意外的费用。 对于生产工作负荷，请参阅 [Azure 虚拟机中 SQL Server 的性能最佳做法](virtual-machines-windows-sql-performance.md)中建议的计算机大小和配置。

    ![实例详细信息](media/quickstart-sql-vm-create-portal/basics-instance-details.png)

> [!IMPORTANT]
> 在“选择大小”窗口中显示的每月估计成本不包括 SQL Server 许可成本。 此预估费用只是 VM 的成本。 对于 SQL Server 的 Express 和开发人员版本，这是估计的总成本。 对于其他版本，请参阅[“Windows 虚拟机定价”页](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)并选择 SQL Server 的目标版本。 另请参阅[SQL Server Azure vm](virtual-machines-windows-sql-server-pricing-guidance.md)和[虚拟机大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的定价指南。

* 在 "**管理员帐户**" 下提供用户名和密码。 密码必须至少 12 个字符长，且符合[定义的复杂性要求](../../windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)。

   ![管理员帐户](media/quickstart-sql-vm-create-portal/basics-administrator-account.png)

* 在“入站端口规则”下，选择“允许所选端口”，然后从下拉列表中选择“RDP (3389)”。 

   ![入站端口规则](media/quickstart-sql-vm-create-portal/basics-inbound-port-rules.png)


## <a name="2-configure-optional-features"></a>2. 配置可选功能

### <a name="disks"></a>磁盘

在 "**磁盘**" 选项卡上，配置磁盘选项。 

* 在 " **os 磁盘类型**" 下，从下拉类型中选择要用于操作系统的磁盘类型。 建议为生产系统使用高级版，但不适用于基本 VM。 若要使用高级 SSD，请更改虚拟机大小。 
* 在 "**高级**" 下的 "使用**托管磁盘**" 下选择 **"是"** 。

   > [!NOTE]
   > Microsoft 建议为 SQL Server 使用托管磁盘。 托管磁盘在后台处理存储。 此外，当使用托管磁盘的虚拟机位于同一可用性集中时，Azure 会分发存储资源以提供适当冗余。 有关详细信息，请参阅[Azure 托管磁盘概述](../managed-disks-overview.md)。 有关可用性集中托管磁盘的具体信息，请参阅[为可用性集中的 VM 使用托管磁盘](../manage-availability.md)。

![SQL VM 磁盘设置](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-disks.png)
  
  
### <a name="networking"></a>网络

在 "**网络**" 选项卡上，配置网络选项。 

* 创建新的**虚拟网络**，或使用现有的 vNet 作为 SQL Server VM。 同时指定一个**子网**。 

* 在 " **NIC 网络安全组**" 下，选择 "基本" 安全组或 "高级" 安全组。 选择 "基本" 选项后，可以为 SQL Server VM 选择入站端口（在 "**基本**" 选项卡上配置的值相同）。 选择 "高级" 选项，可以选择现有的网络安全组，也可以创建一个新的安全组。 

* 你可以对网络设置进行其他更改，也可以保留默认值。

![SQL VM 网络设置](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-networking.png)

#### <a name="monitoring"></a>监视

在 "**监视**" 选项卡上，配置监视和自动关闭。 

* 默认情况下，Azure 会启用使用为 VM 指定的同一个存储帐户的**启动诊断**。 你可以在此处更改这些设置，并启用**OS 来宾诊断**。 
* 也可以在此选项卡上启用**系统分配的托管标识**和**自动关闭**。 

![SQL VM 管理设置](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-management.png)


## <a name="3-configure-sql-server-settings"></a>3. 配置 SQL Server 设置

在 " **SQL Server 设置**" 选项卡上，配置 SQL Server 的特定设置和优化。 可为 SQL Server 配置的设置包括以下各项：

| 设置 |
| --- |
| [连接](#connectivity) |
| [身份验证](#authentication) |
| [Azure 密钥保管库集成](#azure-key-vault-integration) |
| [存储配置](#storage-configuration) |
| [自动修补](#automated-patching) |
| [自动备份](#automated-backup) |
| [机器学习服务](#machine-learning-services) |


### <a name="connectivity"></a>连接

在“ **SQL 连接**”下，指定要对此 VM 上的 SQL Server 实例进行的访问类型。 对于本演示，请选择“公共(Internet)”以允许从 Internet 上的计算机或服务连接到 SQL Server。 选中此选项后，Azure 会自动将防火墙和网络安全组配置为允许所选端口上的流量。

> [!TIP]
> SQL Server 默认在已知端口 1433 上侦听。 为了提高安全性，请更改上一对话框中的端口，改成在非默认端口（例如 1401）上侦听。 如果更改端口，则必须在任何客户端工具（例如 SSMS）中使用该端口进行连接。

![SQL VM 安全性](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-security.png)

若要通过 Internet 连接到 SQL Server，还必须启用下一部分中所述的“SQL Server 身份验证”。

如果不希望启用通过 Internet 连接到数据库引擎这一功能，可选择以下选项之一：

* **本地(仅在 VM 中)** ：仅允许从 VM 内部连接到 SQL Server。
* **私有(虚拟机内)** ：允许从同一虚拟网络中的虚拟机或服务连接到 SQL Server。

一般情况下，选择方案允许的最严格连接可提高安全性。 但是，通过网络安全组规则和 SQL/Windows 身份验证的所有选项都是安全的。 可以在创建 VM 后编辑“网络安全组”。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的安全注意事项](virtual-machines-windows-sql-security.md)。



### <a name="authentication"></a>Authentication

如果需要 SQL Server 身份验证，请在**SQL Server 设置**"选项卡上单击" **SQL 身份验证**"下的"**启用**"。

![SQL Server 身份验证](./media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-authentication.png)

> [!NOTE]
> 如果计划通过 Internet 来访问 SQL Server（“公共”连接选项），则必须在此处启用 SQL 身份验证。 对 SQL Server 进行公共访问需要使用 SQL 身份验证。

如果启用 SQL Server 身份验证，请指定“登录名”和“密码”。 此登录名配置为 SQL Server 身份验证登录名和**sysadmin**固定服务器角色的成员。 若要详细了解身份验证模式，请参阅[选择身份验证模式](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)。

如果未启用 SQL Server 身份验证，可以在 VM 上使用本地管理员帐户连接到 SQL Server 实例。


### <a name="azure-key-vault-integration"></a>Azure 密钥保管库集成

若要将安全机密存储在 Azure 中进行加密，请选择 " **SQL Server 设置**"，并向下滚动到 " **azure 密钥保管库集成**"。 选择 "**启用**"，并填写所需的信息。 

![Azure 密钥保管库集成](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-akv.png)

下表列出了配置 Azure 密钥保管库集成所需的参数。

| 参数 | DESCRIPTION | 示例 |
| --- | --- | --- |
| **密钥保管库 URL** |密钥保管库的位置。 |https：\//contosokeyvault.vault.azure.net/ |
| **主体名称** |Azure Active Directory 服务主体名称。 该名称也称为客户端 ID。 |fde2b411-33d5-4e11-af04eb07b669ccf2 |
| **主体密码** |Azure Active Directory 服务主体密码。 该密码也称为客户端密码。 |9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM= |
| **凭据名称** |**凭据名称**：AKV 集成在 SQL Server 内创建一个凭据，使 VM 具有对密钥保管库的访问权限。 为此凭据选择一个名称。 |mycred1 |

有关详细信息，请参阅 [为 Azure VM 上的 SQL Server 配置 Azure 密钥保管库集成](virtual-machines-windows-ps-sql-keyvault.md)。

### <a name="storage-configuration"></a>存储配置

在 " **SQL Server 设置**" 选项卡上的 "**存储配置**" 下，选择 "**更改配置**" 以打开 "性能优化存储配置" 页，并指定存储要求。

![SQL VM 存储配置](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

在“ **存储优化目标**”下，选择以下选项之一：

* **常规** ：默认设置，支持大多数工作负荷。
* **事务处理**针对传统数据库 OLTP 工作负荷优化存储。
* **数据仓库** ：针对分析和报告工作负荷优化存储。

![SQL VM 存储配置](media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration.png)

您可以选择将值保留为默认值，也可以根据 IOPS 需要手动更改存储拓扑。 有关详细信息，请参阅[存储配置](virtual-machines-windows-sql-server-storage-configuration.md)。 

### <a name="sql-server-license"></a>SQL Server 许可
如果你是软件保障客户，则可以利用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/)来引入你自己的 SQL Server 许可证并保存资源。 

![SQL VM 许可证](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-license.png)

### <a name="automated-patching"></a>自动修补

**自动修补** ：默认处于启用状态。 Azure 可以通过自动修补来自动修补 SQL Server 和操作系统。 指定进行维护的具体日期（星期几）、时间和时长。 Azure 会在维护时段进行修补。 维护时段计划使用 VM 的时间区域设置。 如果不希望 Azure 自动修补 SQL Server 和操作系统，请单击“ **禁用**”。  

![SQL VM 自动修补](media/virtual-machines-windows-portal-sql-server-provision/azure-sqlvm-automated-patching.png)

有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的自动修补](virtual-machines-windows-sql-automated-patching.md)。

### <a name="automated-backup"></a>自动备份

在“ **自动备份**”下启用针对所有数据库的自动数据库备份。 默认情况下，自动备份处于禁用状态。

启用 SQL 自动备份以后，即可进行以下设置的配置：

* 备份保留期（天）
* 用于备份的存储帐户
* 备份的加密选项和密码
* 备份系统数据库
* 配置备份计划

若要加密备份，请单击“ **启用**”。 然后指定“密码”。 Azure 创建一个证书来加密备份，并使用指定的密码来保护该证书。 默认情况下会自动设置计划，但你可以通过选择 "**手动**" 来创建手动计划。 

![SQL VM 自动备份](media/virtual-machines-windows-portal-sql-server-provision/automated-backup.png)

有关详细信息，请参阅 [针对 Azure 虚拟机中 SQL Server 的自动备份](virtual-machines-windows-sql-automated-backup.md)。


### <a name="machine-learning-services"></a>机器学习服务

你可以选择启用[机器学习服务](/sql/advanced-analytics/)。 使用此选项，你可以在 SQL Server 2017 中使用 Python 和 R 进行机器学习。 在**SQL Server 设置**"窗口中选择"**启用**"。


## <a name="4-review--create"></a>4. 查看 + 创建

在“查看 + 创建”选项卡中查看摘要，然后选择“创建”以创建为此 VM 指定的 SQL Server、资源组和资源。

可以从 Azure 门户监视部署情况。 屏幕顶部的“ **通知** ”按钮显示部署的基本状态。

> [!NOTE]
> 为了让你了解部署时间，我已使用默认设置将一个 SQL VM 部署到美国东部区域。 此测试部署花费了约 12 分钟才完成。 但是根据你所在的区域和选择的设置，花费的部署时间会更长或更短。

## <a id="remotedesktop"></a> 使用远程桌面打开 VM

使用以下步骤通过远程桌面连接到 SQL Server 虚拟机：

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

连接到 SQL Server 虚拟机以后，即可启动 SQL Server Management Studio 并使用本地管理员凭据通过 Windows 身份验证进行连接。 如果已启用 SQL Server 身份验证，也可以使用在预配期间配置的 SQL 登录名和密码来配合 SQL 身份验证进行连接。

通过访问计算机，可以根据要求直接更改计算机和 SQL Server 设置。 例如，可以配置防火墙设置或更改 SQL Server 配置设置。

## <a id="connect"></a> 远程连接到 SQL Server

在本演示中，选择了虚拟机的“公共”访问权限和“SQL Server 身份验证”。 这些设置将虚拟机自动配置为允许任何客户端通过 Internet 来与 SQL Server 建立连接（假设这些客户端可提供正确的 SQL 登录名）。

> [!NOTE]
> 如果没有在预配过程中选择“公共”，则可在预配后通过门户更改 SQL 连接设置。 有关详细信息，请参阅[更改 SQL 连接设置](virtual-machines-windows-sql-connect.md#change)。

以下部分说明如何通过 Internet 连接到 SQL Server VM 实例。

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

  > [!NOTE]
  > 此示例使用公共端口1433。 但是，如果在 SQL Server VM 部署过程中指定了其他端口（例如1401），则需要修改此值。 


## <a name="next-steps"></a>后续步骤

有关在 Azure 中使用 SQL Server 的其他信息，请参阅 [Azure 虚拟机上的 SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) 和[常见问题](virtual-machines-windows-sql-server-iaas-faq.md)。
