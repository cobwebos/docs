---
title: 创建 Azure 媒体服务帐户
description: 本教程指导你完成创建 Azure 媒体服务帐户的步骤。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49cac230363750e481e165712bf4f619e5cba7ae
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017839"
---
# <a name="create-a-media-services-account"></a>创建媒体服务帐户

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

若要开始加密、编码、分析、管理和流式处理 Azure 中的媒体内容，需要创建媒体服务帐户。 媒体服务帐户需与一个或多个存储帐户相关联。 本文介绍创建新 Azure 媒体服务帐户的步骤。

> [!NOTE]
> 媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议在媒体服务帐户所在的位置使用存储帐户，避免额外的延迟和数据出口成本。

 可以使用 Azure 门户或 CLI 来创建媒体服务帐户。 为要使用的方法选择选项卡。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="portal"></a>[Portal](#tab/portal/)

## <a name="use-the-azure-portal"></a>使用 Azure 门户

<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->
使用 Azure 门户，可以快速创建 Azure 媒体服务帐户。 可以使用自己的帐户访问媒体服务，通过这些服务可以在 Azure 中存储、加密、编码、管理和流式传输媒体内容。

目前，可以使用 [Azure 门户](https://portal.azure.com/)执行以下操作：

* 管理媒体服务 v3 [直播活动](live-events-outputs-concept.md)、 
* 查看（而不是管理）v3 [资产](assets-concept.md)、 
* [获取有关访问 API 的信息](./access-api-howto.md)。 

对于其他所有管理任务（例如，[转换和作业](transforms-jobs-concept.md)和[内容保护](content-protection-overview.md)），请使用 [REST API](/rest/api/media/accountfilters)、[CLI](/cli/azure/ams) 或某个受支持的 [SDK](media-services-apis-overview.md#sdks)。
<!-- Move this section. This section should be moved to conceptual.  It doesn't belong in task based -->

### <a name="use-the-azure-portal-to-create-a-media-services-account"></a>使用 Azure 门户创建媒体服务帐户

1. 登录到 [Azure 门户](https://portal.azure.com/)。
1. 单击“+创建资源” > “媒体” > “媒体服务”。
1. 在“创建媒体服务帐户”部分中输入所需的值。

    | 名称 | 说明 |
    | ---|---|
    |**帐户名**|输入新的媒体服务帐户的名称。 媒体服务帐户名称由小写字母或数字构成（不含空格），长度为 3 到 24 个字符。|
    |**订阅**|如果有多个订阅，请从有权访问的 Azure 订阅的列表中选择一个订阅。|
    |**资源组**|选择新的或现有的资源。 资源组是共享生命周期、权限和策略的资源的集合。 在[此处](../../azure-resource-manager/management/overview.md#resource-groups)了解更多信息。|
    |**位置**|选择用于存储媒体服务帐户的媒体和元数据记录的地理区域。 此区域用于处理和流式传输媒体。 下拉列表中仅显示可用的媒体服务区域。 |
    |**存储帐户**|选择一个存储帐户，以便为媒体服务帐户中的媒体内容提供 Blob 存储。 可以选择位于媒体服务帐户所在的地理区域内的现有存储帐户，也可以创建一个新的存储帐户。 在同一区域内会创建一个新的存储帐户。 适用于存储帐户名的规则对媒体服务帐户同样适用。<br/><br/>必须具有一个主存储帐户，并且可以拥有任意数量的与媒体服务帐户关联的辅助存储帐户 。 可以使用 Azure 门户来添加辅助存储帐户。 有关详细信息，请参阅 [Azure 存储帐户与 Azure 媒体服务帐户](storage-account-concept.md)。<br/><br/>媒体服务帐户和所有关联的存储帐户必须位于同一 Azure 订阅中。 强烈建议在媒体服务帐户所在的位置使用存储帐户，避免额外的延迟和数据出口成本。|

1. 选择“固定到仪表板”以查看帐户部署进度。
1. 单击窗体底部的“创建”。

    创建媒体服务帐户后，会将一个处于“已停止”状态的**默认**流式处理终结点添加到帐户。 若要开始对内容进行流式处理并利用[动态打包](dynamic-packaging-overview.md)和[动态加密](content-protection-overview.md)功能，必须确保要从其流式获取内容的流式处理终结点处于“正在运行”状态。 

## <a name="cli"></a>[CLI](#tab/cli/)

## <a name="use-the-azure-cli"></a>使用 Azure CLI

<!-- NOTE: The following are in the includes file and are reused in other How To articles. All task based content should be in the includes folder with the task- prefix prepended to the file name. -->

### <a name="set-the-azure-subscription"></a>设置 Azure 订阅

[!INCLUDE [Set the Azure subscription with CLI](./includes/task-set-azure-subscription-cli.md)]

### <a name="create-a-resource-group"></a>创建资源组

[!INCLUDE [Create a resource group with CLI](./includes/task-create-resource-group-cli.md)]

### <a name="create-a-storage-account"></a>创建存储帐户

[!INCLUDE [Create a storage account with CLI](./includes/task-create-storage-account-cli.md)]

### <a name="create-a-media-services-account"></a>创建媒体服务帐户

[!INCLUDE [Create a Media Services account with CLI](./includes/task-create-media-services-account-cli.md)]

### <a name="see-also"></a>另请参阅

* [Azure CLI](/cli/azure/ams?view=azure-cli-latest)
* [将辅助存储附加到媒体服务帐户](/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

---

## <a name="next-steps"></a>后续步骤

[流式传输文件](stream-files-dotnet-quickstart.md)