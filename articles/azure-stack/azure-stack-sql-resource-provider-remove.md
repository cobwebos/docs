---
title: 在 Azure Stack 上删除 SQL 资源提供程序 | Microsoft Docs
description: 了解如何从 Azure Stack 部署中删除 SQL 资源提供程序。
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
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: quying
ms.openlocfilehash: b79d64cc063105cb8ecce537a09a7f39a78eef4c
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275021"
---
# <a name="remove-the-sql-resource-provider"></a>删除 SQL 资源提供程序

删除 SQL 资源提供程序之前，必须删除该提供程序的所有依赖项。 你还需要用来安装资源提供程序的部署包的副本。

> [!NOTE]
> 您可以在提供程序安装程序的资源中找到下载链接[部署的资源提供程序先决条件](.\azure-stack-sql-resource-provider-deploy.md#prerequisites)。

删除 SQL 资源提供程序不会从托管服务器中删除租户数据库。

## <a name="dependency-cleanup"></a>依赖项清理

有几个清除任务运行 DeploySqlProvider.ps1 脚本以删除资源提供程序之前的准备工作。

Azure Stack 操作员负责以下清理任务：

* 删除引用 SQL 适配器的所有计划。
* 删除与 SQL 适配器相关联的任何配额。

## <a name="to-remove-the-sql-resource-provider"></a>删除 SQL 资源提供程序

1. 确认已删除所有现有的 SQL 资源提供程序依赖项。

   > [!NOTE]
   > 即使依赖资源当前正在使用 SQL 资源提供程序，也将继续卸载该资源提供程序。
  
2. 获取 SQL 资源提供程序安装包的副本，然后运行自解压程序，将内容提取到临时目录。

3. 打开新的提升权限的 PowerShell 控制台窗口并将更改为提取 SQL 资源提供程序安装文件的目录。

4. 使用以下参数运行 DeploySqlProvider.ps1 脚本：

    * **Uninstall**。 删除资源提供程序和所有关联的资源。
    * **PrivilegedEndpoint**。 特权终结点的 IP 地址或 DNS 名称。
    * **AzureEnvironment**。 用于部署 Azure Stack 的 Azure 环境。 仅对于 Azure AD 部署是必需的。
    * **CloudAdminCredential**。 访问特权终结点时所需的云管理员凭据。
    * **AzCredential**。 Azure Stack 服务管理员帐户的凭据。 使用部署 Azure Stack 时所用的相同凭据。

## <a name="next-steps"></a>后续步骤

[提供应用服务作为 PaaS](azure-stack-app-service-overview.md)
