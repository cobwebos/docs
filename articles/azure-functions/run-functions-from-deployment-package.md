---
title: 从包运行 Azure Functions | Microsoft Docs
description: 通过装载包含函数应用项目文件的部署包文件，让 Azure Functions 运行时运行函数。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
editor: ''
tags: ''
ms.service: functions
ms.devlang: multiple
ms.topic: conceptual
ms.workload: na
ms.date: 08/22/2018
ms.author: glenga
ms.openlocfilehash: 2b9cc3618bf21eac268e3c25f08b80124d52e6af
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669285"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>从包文件运行 Azure Functions

> [!NOTE]
> 本文所述的功能目前以预览版提供，不适用于 Linux 上的 Functions。

在 Azure 中，可以直接从函数应用中的部署包文件运行函数。 另一种做法是在函数应用的 `d:\home\site\wwwroot` 目录中部署函数项目文件。

本文介绍从包运行函数的好处。 此外，介绍如何在函数应用中启用此功能。

## <a name="benefits-of-running-from-a-package-file"></a>从包文件运行的好处
  
从包文件运行函数可提供多种好处：

+ 减少文件副本锁定问题的风险。
+ 可部署到生产应用（需重启）。
+ 可以确定哪些文件在应用中运行。
+ 提高 [Azure 资源管理器部署](functions-infrastructure-as-code.md)的性能。
+ 可以减少 JavaScript 函数的冷启动时间。

有关详细信息，请参阅[此公告](https://github.com/Azure/app-service-announcements/issues/84)。

## <a name="enabling-functions-to-run-from-a-package"></a>使函数从包运行

若要使函数应用从包运行，只需将 `WEBSITE_RUN_FROM_ZIP` 设置添加到函数应用设置。 `WEBSITE_RUN_FROM_ZIP` 设置可以使用以下值之一：

| 值  | Description  |
|---------|---------|
|**`<url>`**  | 要运行的特定包文件的位置。 使用 Blob 存储时，应通过[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) 使用专用容器，使 Functions 运行时能够访问包。 可以使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)将包文件上传到 Blob 存储帐户。         |
| **`1`**  | 从函数应用的 `d:\home\data\SitePackages` 文件夹中的某个包文件运行。 这种做法要求该文件夹同时包含名为 `packagename.txt` 的文件。 此文件仅包含文件夹中包文件的名称（没有任何空白字符）。 |

下面显示了配置为从 Azure Blob 存储中托管的 .zip 文件运行的函数应用：

![WEBSITE_RUN_FROM_ZIP 应用设置](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前仅支持 .zip 包文件。

## <a name="integration-with-zip-deployment"></a>与 zip 部署集成

[Zip 部署][Zip deployment for Azure Functions]是 Azure 应用服务的一项功能，可用于将函数应用项目部署到 `wwwroot` 目录。 项目打包为 .zip 部署文件。 可以使用相同的 API 将包部署到 `d:\home\data\SitePackages` 文件夹。 对 `WEBSITE_RUN_FROM_ZIP` 应用设置使用值 `1` 时，zip 部署 API 会将包复制到 `d:\home\data\SitePackages` 文件夹，而不是将文件提取到 `d:\home\site\wwwroot`。 它还会创建 `packagename.txt` 文件。 然后，函数应用在重启后会从该包运行，而 `wwwroot` 变为只读。 有关 zip 部署的详细信息，请参阅 [Azure Functions 的 Zip 部署](deployment-zip-push.md)。

## <a name="adding-the-websiterunfromzip-setting"></a>添加 WEBSITE_RUN_FROM_ZIP 设置

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
