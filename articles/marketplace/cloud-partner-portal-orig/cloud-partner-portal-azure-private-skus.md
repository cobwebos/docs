---
title: 私有 Sku 和计划 |Azure Marketplace
description: 如何使用专用 SKU 来管理套餐可用性。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: pabutler
ms.openlocfilehash: 940b50cf4a04abacd4d7be2104dd97fb8b3db736
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883120"
---
<a name="private-skus-and-plans"></a>专用 SKU 和计划
============

使用专用 SKU 可以限制为只有特定的客户才能使用 SKU。 将某个 SKU 标记为专用后，不会在任何公共目录中提供该 SKU，包括 [Azure 市场](https://azuremarketplace.microsoft.com)和 [Azure 门户](https://portal.azure.com)。 在 Azure 门户中，只有有权访问该 SKU 的客户才能看到它。 此外，这些客户的权限将会提升，有权访问专用的套餐。

>[!NOTE]
>专用 SKU 必须有新的唯一 SKU/计划 ID，以避免与公共 SKU 发生任何冲突。

可以使用专用 SKU 来处理以下方案：

1.  发布仅供特定客户公开使用的，而不是让公众使用的软件。
2.  以自定义的价格向特定的客户发布公共软件的变体。
3.  使用自定义的说明和术语（通过新套餐）发布公共软件的变体。

如果只想更改价格，可以重复使用同一套餐中另一 SKU 的磁盘。 使用专用 SKU 时，无需重新提交不同 SKU 的磁盘。

<a name="mark-a-sku-private"></a>将 SKU 标记为专用
---------------------

若要将某个 SKU 标记为专用，请切换询问 SKU 是否为专用的选项：

![将 SKU 标记为专用](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

可以重复使用另一 SKU 中的磁盘，并修改定价或说明。 若要重新使用磁盘，请选择 "**是**" 作为对 "此 sku 如何从公共 SKU 使用映像" 提示的响应。

如果 SKU 已标记为专用，并且套餐包含带有可重复使用磁盘的其他 SKU，则你需要指明该 SKU 将重复使用另一 SKU 中的磁盘。 此外，需要指定专用 SKU 的目标受众。

>[!NOTE]
>发布某个公共 SKU 后，无法将其设为专用 SKU。

<a name="select-an-image"></a>选择映像
------------------

可为专用 SKU 提供新磁盘，或重复使用另一 SKU 中已提供的相同磁盘，只需修改定价或说明即可。 若要重新使用磁盘，请选择 "**是**" 作为对 "此 sku 如何从公共 SKU 使用映像" 提示的响应。

![指示重新使用图像](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

在确认 SKU 重用图像后，为映像选择源或*基本*SKU：

![选择镜像](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

发布套餐时，选定 SKU 中的映像将在包含自定义费率/条款的专用 SKU ID 下提供。 只有目标受众可以看到专用 SKU。

进行映像更新时，只需更新基础 SKU 的映像。 在幕后，专用 SKU 的映像也会自动更新。 同样，如果从基础 SKU 中删除了映像，该映像也会从专用 SKU 中删除。

<a name="restricting-the-audience"></a>限制受众
------------------------

只有目标用户才能发现和部署专用套餐。
目前，我们支持使用订阅 ID 来指定目标用户。

可以通过手动输入窗体为**最多 10 个订阅**输入这些订阅，或者上传 CSV 文件，这样可为**最多 20,000 个订阅**输入订阅。

手动输入受限制的受众：

![手动限制受众](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

上传受限制受众的 CSV：

![使用 CSV 限制受众](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

示例 CSV 文件内容：

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

从手动输入切换为 CSV 上传视图，或者从 CSV 切换为手动输入时，不会保留有权访问 SKU 的订阅 ID 的旧列表。 此时会显示一条警告，并且只会在保存套餐时才覆盖该列表。

<a name="managing-private-audiences"></a>管理专用受众
-------------------------

**若要更新受众而不重新发布整个产品/服务，请使用 UI 或 API 进行所需的受众更改，然后启动 "同步专用受众" 操作。**

如果你的受众为10个或更少订阅，你可以使用 CPP UI 完全管理它。

如果你的受众超过10个订阅，你可以使用 CSV 文件对其进行管理，你可以将其上传到 CPP UI 或使用 API。

如果你使用的是 API，而不想维护 CSV 文件，则可以按照以下说明直接使用 API 来管理受众。

> [!NOTE]
> 使用 Azure 订阅 ID （计划和 Sku）或租户 ID （仅计划）将受众添加到专用产品/服务。

###  <a name="managing-subscriptions-with-the-api"></a>利用 API 管理订阅

你可以使用 API 上传 CSV 或直接管理你的受众（无需使用 CSV）。 一般情况下，只需检索产品/服务，更新`restrictedAudience`对象，然后将这些更改提交回产品/服务，以便添加或删除访问群体成员。

下面介绍如何以编程方式更新访问群体列表：

1. [检索产品/服务](cloud-partner-portal-api-retrieve-specific-offer.md)数据：

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 使用以下 JPath 查询，在产品/服务的每个 SKU 中查找受限制的受众对象：

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. 更新产品/服务的受限受众对象。

    **如果最初已从 CSV 文件上传专用产品/服务的订阅列表：**

    你的*restrictedAudience*对象将如下所示。
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    对于每个受限受众对象：

    a. 下载的内容`restrictedAudience.uploadedCsvUri`。 内容只是带有标头的 CSV 文件。 例如：

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. 根据需要添加或删除下载的 CSV 文件中的订阅。

    c. 将更新后的 CSV 文件上传到[Azure Blob 存储](../../storage/blobs/storage-blobs-overview.md)或[OneDrive](https://onedrive.live.com)等位置，并创建文件的只读链接。 这将是你的新*SasUrl*。

    d. 用新*SasUrl*更新密钥。`restrictedAudience.uploadedCsvUri`

    **如果从云合作伙伴门户手动输入专用产品/服务的原始订阅列表：**

    你的*restrictedAudience*对象将如下所示：

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. 对于每个受限受众对象，根据需要在`restrictedAudience.manualEntries`列表中添加或删除条目。

4. 完成更新专用产品/服务的每个 SKU 的所有*restrictedAudience*对象后，请[更新产品/服务](cloud-partner-portal-api-creating-offer.md)：

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    这样，更新的访问群体列表现在生效。

<a name="previewing-private-offers"></a>预览专用产品
-------------------------

在执行预览/暂存步骤期间，只有套餐级的预览订阅才能访问 SKU。 在此测试阶段，你可以预览产品/服务，因为它会显示给你的目标客户。

有权访问暂存套餐的套餐级预览订阅：

![预览订阅 ID](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

套餐上线后，只有受限制的受众订阅（手动输入或使用 CSV 输入）才能查看和部署专用 SKU。 我们建议**始终在专用 SKU 的受限制受众中包含你自己的订阅**，以进行验证。

>[!NOTE]
>为了进行调试，Microsoft 支持和工程团队也有权访问这些专用套餐。
