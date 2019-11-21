---
title: Run your Azure Functions from a package
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
> When deploying your functions to a Linux function app in a [Premium plan](functions-scale.md#premium-plan), you should always run from the package file and [publish your app using the Azure Functions Core Tools](functions-run-local.md#project-file-deployment).

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
| **`1`**  | Recommended for function apps running on Windows. 从函数应用的 `d:\home\data\SitePackages` 文件夹中的某个包文件运行。 If not [deploying with zip deploy](#integration-with-zip-deployment), this option requires the folder to also have a file named `packagename.txt`. 此文件仅包含文件夹中包文件的名称（没有任何空白字符）。 |
|**`<URL>`**  | 要运行的特定包文件的位置。 使用 Blob 存储时，应通过[共享访问签名 (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) 使用专用容器，使 Functions 运行时能够访问包。 可以使用 [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md)将包文件上传到 Blob 存储帐户。 When you specify a URL, you must also [sync triggers](functions-deployment-technologies.md#trigger-syncing) after you publish an updated package. |

> [!CAUTION]
> When running a function app on Windows, the external URL option yields worse cold-start performance. When deploying your function app to Windows, you should set `WEBSITE_RUN_FROM_PACKAGE` to `1` and publish with zip deployment.

下面显示了配置为从 Azure Blob 存储中托管的 .zip 文件运行的函数应用：

![WEBSITE_RUN_FROM_ZIP 应用设置](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> 目前仅支持 .zip 包文件。

## <a name="integration-with-zip-deployment"></a>与 zip 部署集成

[Zip deployment][Zip deployment for Azure Functions] is a feature of Azure App Service that lets you deploy your function app project to the `wwwroot` directory. 项目打包为 .zip 部署文件。 可以使用相同的 API 将包部署到 `d:\home\data\SitePackages` 文件夹。 对 `WEBSITE_RUN_FROM_PACKAGE` 应用设置使用值 `1` 时，zip 部署 API 会将包复制到 `d:\home\data\SitePackages` 文件夹，而不是将文件提取到 `d:\home\site\wwwroot`。 它还会创建 `packagename.txt` 文件。 After a restart, the package is mounted to `wwwroot` as a read-only filesystem. 有关 zip 部署的详细信息，请参阅 [Azure Functions 的 Zip 部署](deployment-zip-push.md)。

## <a name="adding-the-website_run_from_package-setting"></a>添加 WEBSITE_RUN_FROM_PACKAGE 设置

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

## <a name="troubleshooting"></a>故障排除

- Run From Package makes `wwwroot` read-only, so you will receive an error when writing files to this directory.
- Tar and gzip formats are not supported.
- This feature does not compose with local cache.
- For improved cold-start performance, use the local Zip option (`WEBSITE_RUN_FROM_PACKAGE`=1).

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 的连续部署](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
