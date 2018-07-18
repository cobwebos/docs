---
title: 连接到 Azure Key Vault 时对 ASP.NET 项目所做的更改 | Microsoft Docs
description: 说明使用 Visual Studio 连接服务连接到 Key Vault 时，ASP.NET 项目会发生什么情况。
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2018
ms.locfileid: "33781556"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>我的 ASP.NET 项目（Visual Studio Key Vault 连接服务）发生了什么情况？

> [!div class="op_single_selector"]
> - [入门](vs-key-vault-aspnet-get-started.md)
> - [发生了什么情况](vs-key-vault-aspnet-what-happened.md)

本文介绍在[使用 Visual Studio 添加 Key Vault 连接服务](vs-key-vault-add-connected-service.md)时，对 ASP.NET 项目所做的具体更改。

有关使用连接服务的信息，请参阅[入门](vs-key-vault-aspnet-get-started.md)。

## <a name="added-references"></a>添加的引用

影响项目文件 *.NET 引用和 `packages.config`（NuGet 引用）。

| Type | 引用 |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>添加的文件

- 添加了 ConnectedService.json，其中记录了有关连接服务提供程序、版本和指向文档的链接的一些信息。

## <a name="project-file-changes"></a>项目文件更改

- 添加了连接服务 ItemGroup 和 ConnectedServices.json 文件。
- 请参考[添加的引用](#added-references)部分中所述的 .NET 程序集。

## <a name="webconfig-or-appconfig-changes"></a>web.config 或 app.config 发生更改

- 添加了以下配置条目：

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>对 Azure 的更改

- 创建了资源组（或使用了现有资源组）。
- 在指定的资源组中创建了密钥保管库。

