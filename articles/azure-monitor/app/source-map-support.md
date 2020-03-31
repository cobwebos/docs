---
title: JavaScript 应用程序的源映射支持 - Azure 监视器应用程序见解
description: 了解如何使用应用程序见解将源映射上载到您自己的存储帐户 Blob 容器。
ms.topic: conceptual
author: markwolff
ms.author: marwolff
ms.date: 03/04/2020
ms.openlocfilehash: 4b452b31338760a8f53eed54420319101836bc00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474877"
---
# <a name="source-map-support-for-javascript-applications"></a>对 JavaScript 应用程序的源映射支持

应用程序见解支持将源映射上载到您自己的存储帐户 Blob 容器。
源映射可用于解分端到端事务详细信息页上的调用堆栈。 [JavaScript SDK][ApplicationInsights-JS]或[Node.js SDK][ApplicationInsights-Node.js]发送的任何异常都可以使用源映射进行解码化。

![通过链接存储帐户来解化呼叫堆栈](./media/source-map-support/details-unminify.gif)

## <a name="create-a-new-storage-account-and-blob-container"></a>创建新的存储帐户和 Blob 容器

如果您已有现有的存储帐户或 Blob 容器，则可以跳过此步骤。

1. [新建存储帐户][create storage account]
2. [在][create blob container]存储帐户中创建 Blob 容器。 请确保将"公共访问级别"设置为`Private`，以确保源地图不公开访问。

> [!div class="mx-imgBorder"]
>![容器访问级别必须设置为专用](./media/source-map-support/container-access-level.png)

## <a name="push-your-source-maps-to-your-blob-container"></a>将源映射推送到 Blob 容器

应通过配置连续部署管道自动将源映射上载到配置的 Blob 容器，将连续部署管道与存储帐户集成。 不应将源映射上载到 Blob 容器中的子文件夹;因此，不应将源映射上载到"Blob"容器中的子文件夹。目前，源映射将仅从根文件夹提取。

### <a name="upload-source-maps-via-azure-pipelines-recommended"></a>通过 Azure 管道上载源映射（推荐）

如果使用 Azure 管道持续生成和部署应用程序，请将[Azure 文件复制][azure file copy]任务添加到管道以自动上载源映射。

> [!div class="mx-imgBorder"]
> ![将 Azure 文件复制任务添加到管道，以将源映射上载到 Azure Blob 存储](./media/source-map-support/azure-file-copy.png)

## <a name="configure-your-application-insights-resource-with-a-source-map-storage-account"></a>使用源地图存储帐户配置应用程序见解资源

### <a name="from-the-end-to-end-transaction-details-page"></a>从端到端交易记录详细信息页

在端到端事务详细信息选项卡中，可以单击 *"取消分钟化"，* 并且将显示一个提示，用于配置资源是否未配置。

1. 在门户中，查看已详细化的异常的详细信息。
2. 单击 *"取消最小化"*
3. 如果资源尚未配置，将显示一条消息，提示您进行配置。

### <a name="from-the-properties-page"></a>从属性页

如果要配置或更改链接到应用程序见解资源的存储帐户或 Blob 容器，可以通过查看应用程序见解资源的 *"属性*"选项卡来执行此操作。

1. 导航到应用程序见解资源的"*属性*"选项卡。
2. 单击 *"更改源映射 blob 容器*"。
3. 选择其他 Blob 容器作为源映射容器。
4. 单击 `Apply`。

> [!div class="mx-imgBorder"]
> ![通过导航到属性边栏选项卡重新配置选定的 Azure Blob 容器](./media/source-map-support/reconfigure.png)

## <a name="troubleshooting"></a>疑难解答

### <a name="required-role-based-access-control-rbac-settings-on-your-blob-container"></a>Blob 容器上所需的基于角色的访问控制 （RBAC） 设置

门户上使用此功能的任何用户必须至少作为[存储 Blob 数据读取器][storage blob data reader]分配给 Blob 容器。 您必须将此角色分配给将通过此功能使用源映射的任何其他角色。

> [!NOTE]
> 根据容器的创建方式，这可能尚未自动分配给您或您的团队。

### <a name="source-map-not-found"></a>源映射未找到

1. 验证相应的源映射是否上载到正确的 blob 容器
2. 验证源映射文件是否以映射到的 JavaScript 文件命名，并加上`.map`。
    - 例如，`/static/js/main.4e2ca5fa.chunk.js`将搜索名为`main.4e2ca5fa.chunk.js.map`
3. 检查浏览器的控制台，查看是否记录任何错误。 将其包含在任何支持票证中。

## <a name="next-steps"></a>后续步骤

* [Azure 文件复制任务](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)


<!-- Remote URLs -->
[create storage account]: https://docs.microsoft.com/azure/storage/common/storage-account-create?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal
[create blob container]: https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal
[storage blob data reader]: https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader
[ApplicationInsights-JS]: https://github.com/microsoft/applicationinsights-js
[ApplicationInsights-Node.js]: https://github.com/microsoft/applicationinsights-node.js
[azure file copy]: https://aka.ms/azurefilecopyreadme