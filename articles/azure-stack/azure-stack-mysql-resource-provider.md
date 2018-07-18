---
title: 将 MySQL 数据库用作 Azure Stack 上的 PaaS | Microsoft Docs
description: 了解如何在 Azure Stack 上部署 MySQL 资源提供程序，并提供 MySQL 数据库即服务。
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
ms.openlocfilehash: 24ba595413cde07c420a94de234d7926e0eb0e7f
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36310049"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>使用 Microsoft Azure 堆栈上的 MySQL 数据库

你可以部署 MySQL 资源提供程序 API 用于部署 Azure 堆栈中的 MySQL 数据库。 有关资源提供程序 API 的详细信息，请参阅[Windows Azure 包 MySQL 资源提供程序 REST API 参考](https://msdn.microsoft.com/library/dn528442.aspx)。

MySQL 数据库共有在网站上，并支持多个网站平台。 例如，你可以创建使用 Web Apps 平台即服务 (PaaS) 外接程序的 WordPress 网站。

在部署资源提供程序后，你可以：

* 创建 MySQL 服务器和数据库使用 Azure 资源管理器部署模板。
* 作为一种服务提供 MySQL 数据库。  

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 资源提供程序适配器体系结构

资源提供程序具有以下组件：

* **MySQL 资源提供程序适配器虚拟机 (VM)**，这是正在运行的提供程序服务的 Windows Server VM。
* **资源提供程序**，其处理请求并访问数据库资源。
* **托管 MySQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。 你可以自己，创建 MySQL 实例，或者提供对外部的 MySQL 实例的访问。 [Azure 堆栈快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)具有可用于示例模板：

  * 创建 MySQL 服务器。
  * 下载并部署从 Azure 应用商店创建 MySQL 服务器。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集成系统上的宿主服务器， 而不能通过默认提供商订阅创建。 必须通过租户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器是计费的 Vm，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>所需的特权

系统帐户必须拥有以下特权：

* **数据库：** 创建、 删除
* **登录名：** 创建、 设置、 删除、 授予、 撤消  

## <a name="next-steps"></a>后续步骤

[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
