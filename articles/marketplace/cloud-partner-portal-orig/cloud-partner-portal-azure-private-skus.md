---
title: 私有 SKU 和计划 |Azure 应用商店
description: 如何使用专用 SKU 来管理套餐可用性。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280382"
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

可以重复使用另一 SKU 中的磁盘，并修改定价或说明。 要重用磁盘，请选择 **"是**"作为响应"此 SKU 是否重用来自公共 SKU 的图像"提示。

如果 SKU 已标记为专用，并且套餐包含带有可重复使用磁盘的其他 SKU，则你需要指明该 SKU 将重复使用另一 SKU 中的磁盘。 此外，需要指定专用 SKU 的目标受众。

>[!NOTE]
>发布某个公共 SKU 后，无法将其设为专用 SKU。

<a name="select-an-image"></a>选择映像
------------------

可为专用 SKU 提供新磁盘，或重复使用另一 SKU 中已提供的相同磁盘，只需修改定价或说明即可。 要重用磁盘，请选择 **"是**"作为响应"此 SKU 是否从公共 SKU 重用映像"提示。

![指示映像重用](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

确认 SKU 重用映像后，为映像选择源或*基础*SKU：

![选择映像](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

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

<a name="managing-private-audiences"></a>管理私人受众
-------------------------

**为了在不重新发布整个产品/服务的情况下更新受众，您可以对所需的受众进行更改（使用 UI 或 API），然后启动"同步专用受众"操作。**

如果受众为 10 个或更少的订阅，则可以完全使用 CPP UI 对其进行管理。

如果访问群体超过 10 个订阅，则可以使用 CSV 文件对其进行管理，该文件可以上载到 CPP UI 或使用 API。

如果您使用的是 API，并且不想维护 CSV 文件，则可以根据以下说明直接使用 API 管理访问群体。

> [!NOTE]
> 使用 Azure 订阅 ID（计划和 SKU）或租户 ID（仅限计划）向私人产品/服务添加访问群体。

###  <a name="managing-subscriptions-with-the-api"></a>使用 API 管理订阅

您可以使用 API 上传 CSV 或直接管理受众（不使用 CSV）。 通常，您只需检索产品/服务，更新`restrictedAudience`对象，然后将这些更改提交回您的产品/服务，以便添加或删除受众成员。

以下是以编程方式更新访问群体列表的方式：

1. [检索您的产品/服务](cloud-partner-portal-api-retrieve-specific-offer.md)数据：

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. 使用此 JPath 查询查找产品/服务的每个 SKU 中的受限访问对象：

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. 更新优惠的受限受众对象。

    **如果您最初从 CSV 文件上传了私人优惠的订阅列表：**

    受限*访问对象*如下所示。
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    对于每个受限访问对象：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 下载 的内容`restrictedAudience.uploadedCsvUri`。 内容只是带有标头的 CSV 文件。 例如：

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b.保留“数据库类型”设置，即设置为“共享”。 根据需要在下载的 CSV 文件中添加或删除订阅。

    c. 将更新的 CSV 文件上载到位置（如[Azure Blob 存储](../../storage/blobs/storage-blobs-overview.md)或[OneDrive），](https://onedrive.live.com)并创建指向文件的只读链接。 这将是你的新*SasUrl。*

    d.单击“下一步”。 使用新的`restrictedAudience.uploadedCsvUri` *SasUrl*更新密钥。

    **如果您从云合作伙伴门户手动输入了私人产品/服务的原始订阅列表：**

    受限*访问对象*如下所示：

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

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于每个受限访问对象，根据需要添加或删除列表中的`restrictedAudience.manualEntries`条目。

4. 更新完私人产品/服务的每个 SKU 的所有*受限访问对象*后，[请更新产品/服务](cloud-partner-portal-api-creating-offer.md)：

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    这样，您更新的受众列表现在生效。

<a name="previewing-private-offers"></a>预览私人优惠
-------------------------

在执行预览/暂存步骤期间，只有套餐级的预览订阅才能访问 SKU。 在此测试阶段，您可以预览产品/服务，因为它会显示在目标客户中。

有权访问暂存套餐的套餐级预览订阅：

![预览订阅 ID](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

套餐上线后，只有受限制的受众订阅（手动输入或使用 CSV 输入）才能查看和部署专用 SKU。 我们建议**始终在专用 SKU 的受限制受众中包含你自己的订阅**，以进行验证。

>[!NOTE]
>为了进行调试，Microsoft 支持和工程团队也有权访问这些专用套餐。
