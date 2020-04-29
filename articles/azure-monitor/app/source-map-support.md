---
title: JavaScript 应用程序的源映射支持-Azure Monitor Application Insights
description: 了解如何使用 Application Insights 将源映射上传到自己的存储帐户 Blob 容器。
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474877"
---
# <a name="source-map-support-for-javascript-applications"></a>JavaScript 应用程序的源映射支持

Application Insights 支持将源映射上载到你自己的存储帐户 Blob 容器。
源映射可用于 unminify 在端到端事务详细信息页上找到的调用堆栈。 [JAVASCRIPT sdk][ApplicationInsights-JS]或 node.js [sdk][ApplicationInsights-Node.js]发送的任何异常可以与源映射 unminified。

![通过与存储帐户链接 Unminify 调用堆栈](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>创建新的存储帐户和 Blob 容器

如果已有存储帐户或 blob 容器，则可以跳过此步骤。

1. [新建存储帐户][create storage account]
2. 在存储帐户中[创建一个 blob 容器][create blob container]。 确保将 "公共访问级别" 设置为`Private`，以确保源映射不可公开访问。

> [!div class="mx-imgBorder"]
>![容器访问级别必须设置为 "专用"](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>将源映射推送到 Blob 容器

你应将你的持续部署管道配置为自动将源映射上传到已配置的 Blob 容器，从而将你的持续部署管道集成到你的存储帐户。 不应将源映射上载到 Blob 容器中的子文件夹;目前，仅从根文件夹提取源映射。

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>通过 Azure Pipelines 上传源映射（推荐）

如果使用 Azure Pipelines 持续生成和部署应用程序，请将[Azure 文件复制][azure file copy]任务添加到管道，以自动上传源映射。

> [!div class="mx-imgBorder"]
> ![将 Azure 文件复制任务添加到管道，以将源映射上载到 Azure Blob 存储](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>使用源映射存储帐户配置 Application Insights 资源

### <a name="from-the-end-to-end-transaction-details-page"></a>从端到端事务详细信息页

在 "端到端事务详细信息" 选项卡上，可以单击 " *Unminify* "，如果未配置资源，将显示一条配置提示。

1. 在门户中，查看缩小的异常的详细信息。
2. 单击*Unminify*
3. 如果尚未配置资源，将显示一条消息，提示你进行配置。

### <a name="from-the-properties-page"></a>从 "属性" 页

如果要配置或更改链接到 Application Insights 资源的存储帐户或 Blob 容器，则可以通过查看 Application Insights 资源的 "*属性*" 选项卡来执行此操作。

1. 导航到 Application Insights 资源的 "*属性*" 选项卡。
2. 单击 "*更改源地图 blob 容器*"。
3. 选择与源地图容器不同的 Blob 容器。
4. 单击 `Apply`。

> [!div class="mx-imgBorder"]
> ![通过导航到 "属性" 边栏选项卡，重新配置所选的 Azure Blob 容器](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>疑难解答

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Blob 容器上所需的基于角色的访问控制（RBAC）设置

门户中使用此功能的任何用户至少必须作为[存储 Blob 数据读取器][storage blob data reader]分配给 Blob 容器。 必须将此角色分配给将通过此功能使用源映射的任何其他人。

> [!NOTE]
> 根据创建容器的方式，这可能不会自动分配给你或你的团队。

### <a name="source-map-not-found"></a>找不到源映射

1. 验证相应的源映射是否已上传到正确的 blob 容器
2. 验证源映射文件是否以其映射到的 JavaScript 文件命名，并`.map`为后缀。
    - 例如， `/static/js/main.4e2ca5fa.chunk.js`将搜索名为`main.4e2ca5fa.chunk.js.map`
3. 查看浏览器的控制台，查看是否记录了任何错误。 在任何支持票证中包含此项。

## <a name="next-steps"></a>后续步骤

* [Azure 文件复制任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme