---
title: 如何在 Azure VM 上执行就地升级的 SQL Server 版本 |Microsoft Docs
description: 了解如何更改 SQL Server VM 在 Azure 中的版本。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 05d2170fe9e6055179bf49d803d4739ddc05be89
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607541"
---
# <a name="how-to-perform-an-in-place-upgrade-of-sql-server-edition-on-azure-vm"></a>如何在 Azure VM 上执行就地升级的 SQL Server 版本

本文介绍如何更改现有 SQL Server 在 Azure 中的 Windows 虚拟机上的 SQL Server 版本。 

SQL Server 的版本由产品密钥，并指定与安装过程。 版本决定了什么[功能](/sql/sql-server/editions-and-components-of-sql-server-2017)适用于 SQL Server 产品。 你可以使用安装媒体，然后在任一降级以减少成本或升级，以启用更多的功能的 SQL Server 版本。

如果更新版本的 SQL Server 使用 SQL VM 资源提供程序注册后使用安装媒体，然后更新 Azure 相应地向你收费应将 SQL Server 版本属性设置 SQL VM 资源，如下所示：

1. 登录到 [Azure 门户](https://portal.azure.com)。 
1. 导航到 SQL Server 虚拟机资源。 
1. 下**设置**，选择**配置**，然后从下拉列表下选择您所需的 SQL Server 版本**Edition**。 

   ![更改版本元数据](media/virtual-machines-windows-sql-change-edition/edition-change-in-portal.png)

1. 查看警告，通知您必须首先，更改 SQL Server 版本和该版本属性必须与 SQL Server 版本匹配。 
1. 选择**应用**要应用版本元数据更改。 


## <a name="prerequisites"></a>先决条件

若要执行的 SQL Server 版本的就地更改，您需要： 

- 一个 [Azure 订阅](https://azure.microsoft.com/free/)。
- Windows [SQL Server 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)注册[SQL 虚拟机资源提供程序](virtual-machines-windows-sql-register-with-resource-provider.md)。
- 安装程序所需版本的 SQL Server 使用的介质。 具有客户[软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)可以获取从其安装介质[批量许可中心](https://www.microsoft.com/Licensing/servicecenter/default.aspx)。 客户不具有软件保障可以使用从 marketplace 的 SQL Server VM 映像具有其所需的版本的安装介质。


## <a name="upgrade-edition"></a>升级版本

  > [!WARNING]
  > - **升级的 SQL Server 版本，将重新启动 SQL Server 的服务，以及任何关联的服务，例如 Analysis Services 和 R Services。** 

若要升级的 SQL Server 版本，获取相应的 SQL Server 版本的 SQL Server 安装介质，然后执行以下：

1. 从 SQL Server 安装介质启动 Setup.exe。 
1. 导航到**维护**，然后选择**版本升级**选项。 

   ![升级 SQL Server 的版本](media/virtual-machines-windows-sql-change-edition/edition-upgrade.png)

1. 选择**下一步**直至到达**准备升级版本**页，，然后选择**升级**。 设置窗口的几分钟后，可能会挂起时更改已生效，，然后你将看到**完成**网页以确认你的版本升级已完成。 

SQL Server 版本升级后，您应修改"版本"属性的 Azure 门户上的 Sql 虚拟机，如上所示;这将更新元数据和与此 VM 关联的计费。

## <a name="downgrade-edition"></a>降级版本

  > [!WARNING]
  > - **降级的 SQL Server 版本需要完全卸载 SQL Server，可能会产生额外的停机时间**。 


若要降级版本的 SQL Server，将需要完全卸载 SQL Server，然后重新安装它与所需的版本安装介质。 

将 SQL Server 版本降级通过执行以下步骤：

1. 备份所有数据库，包括系统数据库。 
1. 将系统数据库 （master、 model 和 msdb） 移到新位置。 
1. 完全卸载 SQL Server 和所有关联的服务。 
1. 重启虚拟机。 
1. 安装媒体中使用相应的 SQL Server 版本的 SQL Server。
1. 安装最新 service pack 和累积更新。  
1. 将为与您以前移动到其他位置的系统数据库的安装过程中创建的新系统数据库。 

一旦将 SQL Server 版本降级，如上所示; 应修改 SQL 虚拟机在 Azure 门户中的版本属性这将更新元数据和与此 VM 关联的计费。

## <a name="remarks"></a>备注

 - SQL Server 虚拟机的 edition 属性必须与安装到虚拟机的所有 SQL 虚拟机包括 PAYG 和 BYOL 许可类型的 SQL Server 的版本匹配。
 - 如果您删除 SQL Server VM 资源，将返回到该映像的硬编码版本设置。
  - 若要更改版本的功能是 SQL 虚拟机资源提供程序的功能。 自动部署通过 Azure 门户的 marketplace 映像注册资源提供程序的 SQL Server VM。 但是，在自安装 SQL Server 的客户将需要手动[注册其 SQL Server 虚拟机](virtual-machines-windows-sql-register-with-resource-provider.md)。
- 添加到可用性集的 SQL Server 虚拟机需要重新创建 VM。 作为此类，则所有 Vm 添加到可用性集将返回到默认版本和版本将需要再次修改。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章： 

* [Windows VM 上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)
* [Windows VM 上的 SQL Server 常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)
* [Windows VM 上的 SQL Server 定价指南](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Windows VM 上的 SQL Server 发行说明](virtual-machines-windows-sql-server-iaas-release-notes.md)


