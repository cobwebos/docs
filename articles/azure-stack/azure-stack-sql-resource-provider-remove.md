---
title: 删除 Azure 堆栈上的 SQL 资源提供程序 |Microsoft 文档
description: 了解如何可以从你的 Azure 堆栈部署中删除 SQL 资源提供程序。
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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: b73deebb10d0c81a06df9cd192eaa2ef28de744d
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083032"
---
# <a name="remove-the-sql-resource-provider"></a>删除 SQL 资源提供程序

删除 SQL 资源提供程序之前，你必须删除所有提供程序依赖关系。 你还需要用于安装资源提供程序的部署包的副本。

有几个用于执行在运行之前的清除任务_DeploySqlProvider.ps1_脚本以删除资源提供程序。
租户是负责以下清理任务：

* 从资源提供程序中删除其所有数据库。 （删除租户数据库不会删除数据。）
* 从资源提供程序命名空间中注销。

管理员负责以下清理任务：

* 从 SQL 资源提供程序中删除宿主服务器。
* 删除任何引用 SQL 资源提供程序的计划。
* 删除与 SQL 资源提供程序关联的任何配额。

## <a name="to-remove-the-sql-resource-provider"></a>若要删除 SQL 资源提供程序

1. 验证已删除所有现有 SQL 资源提供程序依赖关系。

   > [!NOTE]
   > 即使从属资源当前正在使用的资源提供程序，则卸载 SQL 资源提供程序将继续操作。
  
2. 获取二进制 SQL 资源提供程序的副本，然后运行自解压缩程序中，若要将内容提取到临时目录。

3. 打开新的提升权限的 PowerShell 控制台窗口并将更改为你在哪里提取的 SQL 资源提供程序二进制文件的目录。

4. 运行 DeploySqlProvider.ps1 脚本使用以下参数：

    * **卸载**。 删除资源提供程序和所有关联的资源。
    * **PrivilegedEndpoint**。 特权终结点的 IP 地址或 DNS 名称。
    * **CloudAdminCredential**。 有关访问特权终结点所需的云管理员凭据。
    * **AzCredential**。 Azure 堆栈服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。

## <a name="next-steps"></a>后续步骤

[作为 PaaS 产品/服务应用程序服务](azure-stack-app-service-overview.md)
