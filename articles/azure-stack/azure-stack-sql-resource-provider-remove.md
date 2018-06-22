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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 150d1c40463aa04527bdd6e356a4c24ef68b02ef
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301892"
---
# <a name="remove-the-sql-resource-provider"></a>删除 SQL 资源提供程序

删除 SQL 资源提供程序之前，你必须删除所有提供程序依赖关系。 你还需要用于安装资源提供程序的部署包的副本。

## <a name="to-remove-the-sql-resource-provider"></a>若要删除 SQL 资源提供程序

1. 验证已删除所有现有 SQL 资源提供程序依赖关系。

   > [!NOTE]
   > 即使从属资源当前正在使用的资源提供程序，则卸载 SQL 资源提供程序将继续操作。
  
2. 获取二进制 SQL 资源提供程序的副本，然后运行自解压缩程序中，若要将内容提取到临时目录。

3. 打开新的提升权限的 PowerShell 控制台窗口并将更改为你在哪里提取的 SQL 资源提供程序二进制文件的目录。

4. 运行 DeploySqlProvider.ps1 脚本使用以下参数：

    - **卸载**。 删除资源提供程序和所有关联的资源。
    - **PrivilegedEndpoint**。 特权终结点的 IP 地址或 DNS 名称。
    - **CloudAdminCredential**。 有关访问特权终结点所需的云管理员凭据。
    - **AzCredential**。 Azure 堆栈服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。

## <a name="next-steps"></a>后续步骤

[作为 PaaS 产品/服务应用程序服务](azure-stack-app-service-overview.md)
