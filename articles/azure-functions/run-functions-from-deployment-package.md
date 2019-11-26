---
title: 从包运行 Azure Functions
description: 通过装载包含函数应用项目文件的部署包文件，让 Azure Functions 运行时运行函数。
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: f5d3465e0899f7e5eab213bdb6234313128b7ec8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230351"
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

| 值  | 说明  |
|---------|---------|
| **`1`**  | 建议用于在 Windows 上运行的函数应用。 从函数应用的 `d:\home\data\SitePackages` 文件夹中的某个包文件运行。 如果不[使用 zip deploy 进行部署](#integration-with-zip-deployment)，则此选项要求该文件夹同时包含名为 `packagename.txt` 的文件。 此文件仅包含文件夹中包文件的名称（没有任何空白字符）。 |
|**`<URL>`**  | 要运行的特定包文件的位置。 使用 Blob 存储时，应通过[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 使用专用容器，使 Functions 运行时能够访问包。 可以使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将包文件上传到 Blob 存储帐户。 指定 URL 时，还必须在发布更新的包后[同步触发器](functions-deployment-technologies.md#trigger-syncing)。 |

> [!CAUTION]
> 在 Windows 上运行函数应用时，外部 URL 选项会导致糟糕的冷启动性能。 将函数应用部署到 Windows 时，应将 `WEBSITE_RUN_FROM_PACKAGE` 设置为 `1` 并通过 zip 部署进行发布。

下面显示了配置为从 Azure Blob 存储中托管的 .zip 文件运行的函数应用：

![WEBSITE_RUN_FROM_ZIP 应用设置](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前仅支持 .zip 包文件。

## <a name="integration-with-zip-deployment"></a>与 zip 部署集成

[Zip 部署][Zip deployment for Azure Functions]是 Azure 应用服务的一项功能，可用于将函数应用项目部署到 `wwwroot` 目录。 项目打包为 .zip 部署文件。 可以使用相同的 API 将包部署到 `d:\home\data\SitePackages` 文件夹。 对 `WEBSITE_RUN_FROM_PACKAGE` 应用设置使用值 `1` 时，zip 部署 API 会将包复制到 `d:\home\data\SitePackages` 文件夹，而不是将文件提取到 `d:\home\site\wwwroot`。 它还会创建 `packagename.txt` 文件。 重启后，该包将作为只读文件系统装载到 `wwwroot`。 有关 zip 部署的详细信息，请参阅 [Azure Functions 的 Zip 部署](deployment-zip-push.md)。

## <a name="adding-the-website_run_from_package-setting"></a>添加 WEBSITE_RUN_FROM_PACKAGE 设置

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>故障排除

- Run From Package 将 `wwwroot` 设为只读，因此在将文件写入此目录时将收到一个错误。
- 不支持 Tar 和 gzip 格式。
- 此功能不与本地缓存组合。
- 若要提高冷启动性能，请使用本地 Zip 选项 (`WEBSITE_RUN_FROM_PACKAGE`=1)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
