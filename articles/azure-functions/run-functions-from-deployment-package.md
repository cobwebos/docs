---
title: 从包运行 Azure Functions | Microsoft Docs
description: 通过装载包含函数应用项目文件的部署包文件，让 Azure Functions 运行时运行函数。
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: glenga
ms.openlocfilehash: 549768473460dcb97b66c3589d71c02039220605
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389950"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>从包文件运行 Azure Functions

在 Azure 中，可以直接从函数应用中的部署包文件运行函数。 另一种做法是在函数应用的 `d:\home\site\wwwroot` 目录中部署文件。

本文介绍从包运行函数的好处。 此外，介绍如何在函数应用中启用此功能。

> [!IMPORTANT]
> 将函数部署到[高级计划](functions-scale.md#premium-plan)中的 Linux 函数应用时，应始终从包文件运行并[使用 Azure Functions Core Tools 发布应用](functions-run-local.md#project-file-deployment)。

## <a name="benefits-of-running-from-a-package-file"></a>从包文件运行的好处
  
从包文件运行函数可提供多种好处：

+ 减少文件副本锁定问题的风险。
+ 可部署到生产应用（需重启）。
+ 可以确定哪些文件在应用中运行。
+ 提高 [Azure 资源管理器部署](functions-infrastructure-as-code.md)的性能。
+ 可以减少冷启动时间，特别是对于具有大型 npm 包树的 JavaScript 函数。

有关详细信息，请参阅[此公告](https://github.com/Azure/app-service-announcements/issues/84)。

## <a name="enabling-functions-to-run-from-a-package"></a>使函数从包运行

若要使函数应用从包运行，只需将 `WEBSITE_RUN_FROM_PACKAGE` 设置添加到函数应用设置。 `WEBSITE_RUN_FROM_PACKAGE` 设置可以使用以下值之一：

| Value  | 描述  |
|---------|---------|
| **`1`**  | 建议用于在 Windows 上运行的函数应用。 从函数应用的 `d:\home\data\SitePackages` 文件夹中的某个包文件运行。 如果不[使用 zip 部署进行部署](#integration-with-zip-deployment)，则此选项要求文件夹还具有一个名为 `packagename.txt` 的文件。 此文件仅包含文件夹中包文件的名称（没有任何空白字符）。 |
|**`<url>`**  | 要运行的特定包文件的位置。 使用 Blob 存储时，应通过[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 使用专用容器，使 Functions 运行时能够访问包。 可以使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将包文件上传到 Blob 存储帐户。         |

> [!CAUTION]
> 在 Windows 上运行函数应用时，external URL 选项会产生更糟的冷启动性能。 将 function app 部署到 Windows 时，应将 `WEBSITE_RUN_FROM_PACKAGE` 设置为 `1`，并随 zip 部署进行发布。

下面显示了配置为从 Azure Blob 存储中托管的 .zip 文件运行的函数应用：

![WEBSITE_RUN_FROM_ZIP 应用设置](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前仅支持 .zip 包文件。

## <a name="integration-with-zip-deployment"></a>与 zip 部署集成

[Zip 部署][Zip deployment for Azure Functions]是 Azure App Service 的一项功能，可用于将 function App 项目部署到 @no__t 目录。 项目打包为 .zip 部署文件。 可以使用相同的 API 将包部署到 `d:\home\data\SitePackages` 文件夹。 对 `WEBSITE_RUN_FROM_PACKAGE` 应用设置使用值 `1` 时，zip 部署 API 会将包复制到 `d:\home\data\SitePackages` 文件夹，而不是将文件提取到 `d:\home\site\wwwroot`。 它还会创建 `packagename.txt` 文件。 重新启动之后，包将装载到 @no__t 为只读文件系统。 有关 zip 部署的详细信息，请参阅 [Azure Functions 的 Zip 部署](deployment-zip-push.md)。

## <a name="adding-the-website_run_from_package-setting"></a>添加 WEBSITE_RUN_FROM_PACKAGE 设置

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>故障排除

- 从包中运行会使 @no__t 为0只读，因此，在将文件写入此目录时，会收到错误。
- Tar 和 gzip 格式不受支持。
- 此功能不是用本地缓存构成的。
- 为了改进冷启动性能，请使用本地 Zip 选项（`WEBSITE_RUN_FROM_PACKAGE` = 1）。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
