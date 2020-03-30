---
title: SQL 服务器版本的就地更改
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
ms.openlocfilehash: bd9622d3e34198402bc45e5d855d05b0c8e35eb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605462"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>在 Azure VM 上就地更改 SQL Server 版本

本文介绍如何在 Azure 中的 Windows 虚拟机上更改 SQL Server 版本。 

SQL Server 的版本由产品密钥确定，并在使用安装介质的安装过程中指定。 该版本规定了 SQL Server 产品中可用的[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)。 您可以使用安装介质更改 SQL Server 版本，并降级以降低成本或升级以启用更多功能。

在内部将 SQL Server 版本更改为 SQL Server VM 后，必须更新 Azure 门户中的 SQL Server 的版本属性，以便计费。 

## <a name="prerequisites"></a>先决条件

要对 SQL Server 版本进行就地更改，您需要执行以下操作： 

- [Azure 订阅](https://azure.microsoft.com/free/)。
- 在 SQL [VM 资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)注册的[Windows 上的 SQL Server VM。](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)
- 使用**所需版本的**SQL Server 设置媒体。 拥有[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)的客户可以从[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)获取其安装介质。 没有软件保证的客户可以使用 Azure 应用商店 SQL Server VM 映像中的设置媒体，该映像具有所需的版本（通常位于`C:\SQLServerFull`中）。 


## <a name="upgrade-an-edition"></a>升级版本

> [!WARNING]
> 升级 SQL Server 版本将重新启动 SQL Server 的服务以及任何关联的服务，如分析服务和 R 服务。 

要升级 SQL Server 版本，获取所需版本的 SQL Server 的 SQL Server 设置媒体，然后执行以下操作：

1. 从 SQL 服务器安装介质打开安装程序.exe。 
1. 转到**维护**并选择 **"版本升级**"选项。 

   ![升级 SQL 服务器版本的选择](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 选择 **"下一步**"，直到到达 **"准备升级版**"页面，然后选择 **"升级**"。 当更改生效时，设置窗口可能会停止响应几分钟。 **"完整"** 页面将确认您的版本升级已完成。 

升级 SQL Server 版本后，修改 Azure 门户中的 SQL Server 虚拟机的版本属性。 这将更新与此 VM 关联的元数据和帐单。

## <a name="downgrade-an-edition"></a>降级版本

要降级 SQL Server 版本，您需要完全卸载 SQL Server，然后使用所需的版本设置媒体重新安装它。 

> [!WARNING]
> 卸载 SQL Server 可能会带来额外的停机时间。 

您可以按照以下步骤降级 SQL Server 版本：

1. 备份所有数据库，包括系统数据库。 
1. 将系统数据库（主数据库、模型和 msdb）移动到新位置。 
1. 完全卸载 SQL Server 和所有相关服务。 
1. 重启虚拟机。 
1. 使用具有所需版本的 SQL Server 的媒体安装 SQL Server。
1. 安装最新的服务包和累积更新。  
1. 将安装期间创建的新系统数据库替换为以前移动到其他位置的系统数据库。 

降级 SQL Server 版本后，在 Azure 门户中修改 SQL Server 虚拟机的版本属性。 这将更新与此 VM 关联的元数据和帐单。

## <a name="change-edition-in-portal"></a>门户中的更改版本 

使用安装介质更改 SQL Server 版本，并将 SQL Server VM 注册到 SQL VM[资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)后，可以使用 Azure 门户修改 SQL Server VM 的 Edition 属性进行计费。 为此，请执行下列步骤： 

1. 登录到 Azure[门户](https://portal.azure.com)。 
1. 转到 SQL Server 虚拟机资源。 
1. 在 **"设置"** 下，选择 **"配置**"。 然后从 **"版本**"下的下拉列表中选择所需的 SQL Server 版本。 

   ![更改版元数据](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 查看警告，指出必须首先更改 SQL Server 版本，并且版本属性必须与 SQL Server 版本匹配。 
1. 选择 **"应用"** 以应用版本元数据更改。 


## <a name="remarks"></a>备注

- SQL Server VM 的版本属性必须匹配为所有 SQL Server 虚拟机安装的 SQL Server 实例的版本，包括即用即付和自带许可证类型的许可证。
- 如果删除 SQL Server VM 资源，将返回映像的硬编码版本设置。
- 更改版本的功能是 SQL VM 资源提供程序的一项功能。 通过 Azure 门户部署 Azure 应用商店映像会自动向资源提供程序注册 SQL Server VM。 但是，自行安装 SQL Server 的客户将需要手动[注册其 SQL Server VM。](virtual-machines-windows-sql-register-with-resource-provider.md)
- 将 SQL Server VM 添加到可用性集需要重新创建 VM。 添加到可用性集的任何 VM 都将回到默认版本，并且需要再次修改该版本。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL 服务器概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL 服务器常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL 服务器定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL 服务器的发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


