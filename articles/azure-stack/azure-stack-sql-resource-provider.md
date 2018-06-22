---
title: 在 Azure Stack 中使用 SQL 数据库 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署 SQL 数据库即服务，并通过便捷的步骤部署 SQL Server 资源提供程序适配器。
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 55d0e51606e8768a01c0b5a7766dbafe24d97a0d
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307819"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 SQL 数据库

使用 SQL Server 资源提供程序适配器 API 来公开 SQL 数据库作为一项服务的[Azure 堆栈](azure-stack-poc.md)。 安装资源提供程序并将它连接到一个或多个 SQL Server 实例之后，你和用户可以创建：

- 适用于云原生应用的数据库。
- 使用 SQL 的网站。
- 使用 SQL 的工作负荷。

资源提供程序不提供所有数据库的管理功能[Azure SQL 数据库](https://azure.microsoft.com/services/sql-database/)。 例如，自动分配资源的弹性池不受支持。 但是，类似资源提供程序支持创建、 读取、 更新和删除 (CRUD) 操作，在 SQL Server 数据库上。 有关资源提供程序 API 的详细信息，请参阅[Windows Azure 包 SQL Server 资源提供程序 REST API 参考](https://msdn.microsoft.com/library/dn528529.aspx)。

>[!NOTE]
SQL Server 资源提供程序 API 不与 Azure SQL 数据库兼容。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构

资源提供程序包括以下组件：

- **SQL 资源提供程序适配器虚拟机 (VM)**，这是一个 Windows Server 虚拟机运行的提供程序服务。
- **资源提供程序**，其处理请求并访问数据库资源。
- **托管 SQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。

你必须创建至少一个 SQL Server 实例，或提供对外部的 SQL Server 实例的访问。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集成系统上的宿主服务器， 而不能通过默认提供商订阅创建。 从租户门户或通过使用相应登录使用 PowerShell，必须创建它们。 所有宿主服务器都是计费的虚拟机，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

## <a name="next-steps"></a>后续步骤

[部署 SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
