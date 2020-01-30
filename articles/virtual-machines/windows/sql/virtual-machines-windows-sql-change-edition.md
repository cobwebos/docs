---
title: SQL Server 版的就地更改
description: 了解如何在 Azure 中更改 SQL Server 虚拟机的版本。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 7d096f721869e43e9a860733d0f6893f224a6776
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772569"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Azure VM 上 SQL Server 版的就地更改

本文介绍如何在 Azure 中的 Windows 虚拟机上更改 SQL Server 版本。 

SQL Server 的版本由产品密钥确定，并在使用安装媒体的安装过程中指定。 本版本规定 SQL Server 产品中提供了哪些[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)。 你可以使用安装媒体更改 SQL Server 版本，并降级以降低成本或升级以启用更多功能。

在内部将 SQL Server 的版本更改为 SQL Server VM 后，你必须更新 Azure 门户中 SQL Server 的版本属性，以便进行计费。 

## <a name="prerequisites"></a>必备组件

若要对 SQL Server 版本进行就地更改，需要满足以下要求： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- 在[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)中注册的[Windows 上的 SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) 。
- 用所需的 SQL Server**版本**设置媒体。 具有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客户可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获得其安装媒体。 没有软件保障的客户可以使用具有所需版本的 Azure Marketplace SQL Server VM 映像（通常位于 `c:\SQLInstalls`）中的设置媒体。 


## <a name="upgrade-an-edition"></a>升级版本

> [!WARNING]
> 升级版本的 SQL Server 将会重新启动 SQL Server 的服务，以及任何关联的服务，如 Analysis Services 和 R Services。 

若要升级 SQL Server 的版本，请获取所需版本 SQL Server 的 SQL Server 安装介质，然后执行以下操作：

1. 从 SQL Server 安装媒体中打开 Setup.exe。 
1. 请参阅 "**维护**" 并选择 "**版本升级**" 选项。 

   ![用于升级 SQL Server 版本的选择](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 选择 "**下一步**"，直到到达 "**准备升级版本**" 页，然后选择 "**升级**"。 在更改生效之前，安装窗口可能会停止响应几分钟。 **完整**的页面将确认版本升级已完成。 

升级 SQL Server 版本后，在 Azure 门户中修改 SQL Server 虚拟机的 edition 属性。 这将更新与此 VM 关联的元数据和计费。

## <a name="downgrade-an-edition"></a>降级版本

若要降级 SQL Server 的版本，你需要完全卸载 SQL Server，然后再次安装所需版本的安装媒体。 

> [!WARNING]
> 卸载 SQL Server 可能会导致额外的停机时间。 

可以按照以下步骤降级 SQL Server 的版本：

1. 备份所有数据库，包括系统数据库。 
1. 将系统数据库（master、model 和 msdb）移至新位置。 
1. 完全卸载 SQL Server 和所有关联的服务。 
1. 重启虚拟机。 
1. 使用具有所需版本的 SQL Server 的媒体安装 SQL Server。
1. 安装最新的 service pack 和累积更新。  
1. 将在安装过程中创建的新系统数据库替换为之前移动到不同位置的系统数据库。 

降级 SQL Server 版本后，在 Azure 门户中修改 SQL Server 虚拟机的 edition 属性。 这将更新与此 VM 关联的元数据和计费。

## <a name="change-edition-in-portal"></a>在门户中更改版本 

更改了使用安装媒体的 SQL Server 版本，并使用[SQL VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册了 SQL Server VM 后，就可以使用 Azure 门户来修改 SQL Server VM 的版本属性以进行计费。 为此，请执行下列步骤： 

1. 登录 [Azure 门户](https://portal.azure.com)。 
1. 中转到 SQL Server 虚拟机资源。 
1. 在 "**设置**" 下，选择 "**配置**"。 然后从 "**版本**" 下的下拉列表中选择所需的 SQL Server 版本。 

   ![更改版本元数据](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 查看警告，指出必须先更改 SQL Server 版，并且 "版本" 属性必须与 SQL Server 版本相匹配。 
1. 选择 "**应用**" 以应用版本元数据更改。 


## <a name="remarks"></a>备注

- SQL Server VM 的 edition 属性必须与为所有 SQL Server 虚拟机安装的 SQL Server 实例的版本相匹配，包括即用即付和自带许可证类型的许可证。
- 如果删除 SQL Server VM 资源，则会返回到映像的硬编码版本设置。
- 更改版本的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure Marketplace 映像会自动向资源提供程序注册 SQL Server VM。 但是，自行安装 SQL Server 的客户需要手动[注册其 SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)。
- 若要将 SQL Server VM 添加到可用性集，则需要重新创建 VM。 添加到可用性集的所有 Vm 都将返回到默认版本，需要重新修改该版本。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上 SQL Server 的定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上 SQL Server 的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


