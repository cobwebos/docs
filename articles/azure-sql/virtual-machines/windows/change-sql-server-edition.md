---
title: SQL Server 版的就地更改
description: 了解如何在 Azure 中更改 SQL Server 虚拟机的版本。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 228f76c9ecb262ffca7851f4339b73c5574f09dc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669158"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM 上 SQL Server 版的就地更改
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍如何在 Azure 中的 Windows 虚拟机上更改 SQL Server 版本。 

SQL Server 的版本由产品密钥确定，并在使用安装媒体的安装过程中指定。 该版本规定了 SQL Server 产品中可用的[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)。 可以使用安装媒体更改 SQL Server 版本，并降级以降低成本或升级以启用更多功能。

在内部将 SQL Server 的版本更改为 SQL Server VM 后，必须更新 Azure 门户中 SQL Server 的版本属性，以便进行计费。 

## <a name="prerequisites"></a>先决条件

若要对 SQL Server 版本进行就地更改，需要满足以下要求： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 已注册到 [Windows 上的 SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 的 [SQL VM 资源提供程序](sql-vm-resource-provider-register.md)。
- 具有**所需版本** SQL Server 的安装介质。 如果客户有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)，则可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保障的客户可以使用具有所需版本的 Azure 市场 SQL Server VM 映像中的安装介质（通常位于 `C:\SQLServerFull` 中）。 


## <a name="upgrade-an-edition"></a>升级版本

> [!WARNING]
> 升级 SQL Server 的版本将重新启动 SQL Server 的服务，以及任何关联的服务，如 Analysis Services 和 R Services。 

若要升级 SQL Server 的版本，请获取所需版本 SQL Server 的 SQL Server 安装介质，然后执行以下操作：

1. 从 SQL Server 安装介质打开 Setup.exe。 
1. 转到**维护**并选择**版本升级**选项。 

   ![用于升级 SQL Server 版本的选择](./media/change-sql-server-edition/edition-upgrade.png)

1. 选择“下一步”，直到达到“准备升级 版本”页，然后选择“升级”。   在更改生效之前，安装窗口可能会停止响应几分钟。 “完成”页将确认版本升级已完成。 

升级 SQL Server 版本后，在 Azure 门户中修改 SQL Server 虚拟机的版本属性。 这将更新与此 VM 关联的元数据和计费。

## <a name="downgrade-an-edition"></a>降级版本

若要降级 SQL Server 的版本，需要完全卸载 SQL Server，然后再次安装所需版本的安装介质。 

> [!WARNING]
> 卸载 SQL Server 可能会导致额外的停机时间。 

可以按照以下步骤降级 SQL Server 的版本：

1. 备份所有数据库，包括系统数据库。 
1. 将系统数据库（master、model 和 msdb）移动到新位置。 
1. 完全卸载 SQL Server 和所有关联的服务。 
1. 重启虚拟机。 
1. 使用具有所需版本的 SQL Server 的介质安装 SQL Server。
1. 安装最新的 Service Pack 和累积更新。  
1. 将在安装过程中创建的新系统数据库替换为之前移动到不同位置的系统数据库。 

降级 SQL Server 版本后，在 Azure 门户中修改 SQL Server 虚拟机的版本属性。 这将更新与此 VM 关联的元数据和计费。

## <a name="change-edition-in-portal"></a>在门户中更改版本 

使用安装介质更改了 SQL Server 的版本，并使用 [SQL VM 资源提供程序](sql-vm-resource-provider-register.md)注册了 SQL Server VM 后，就可以使用 Azure 门户来修改 SQL Server VM 的版本属性，以便进行计费。 为此，请执行下列步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。 
1. 转到 SQL Server 虚拟机资源。 
1. 在“设置”下，选择“配置” 。 然后从**版本**下的下拉列表中选择所需的 SQL Server 版本。 

   ![更改版本元数据](./media/change-sql-server-edition/edition-change-in-portal.png)

1. 查看警告，指出必须先更改 SQL Server 版本，并且版本属性必须与 SQL Server 版本相匹配。 
1. 选择**应用**以应用版本元数据更改。 


## <a name="remarks"></a>备注

- SQL Server VM 的版本属性必须与为所有 SQL Server 虚拟机安装的 SQL Server 实例的版本相匹配，包括即用即付和自带许可证类型的许可证。
- 如果删除了 SQL Server VM 资源，则会回退到映像的硬编码版本设置。
- 更改版本是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure 市场映像会自动将 SQL Server VM 注册到资源提供程序。 但自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](sql-vm-resource-provider-register.md)。
- 若要将 SQL Server VM 添加到可用性集，则需要重新创建 VM。 添加到可用性集的所有 VM 都将返回到默认版本，需要重新修改该版本。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](frequently-asked-questions-faq.md)
* [Windows VM 上的 SQL Server 定价指南](pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](doc-changes-updates-release-notes.md)


