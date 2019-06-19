---
title: 专用 Sku 和计划 |Azure Marketplace
description: 如何使用专用 SKU 来管理套餐可用性。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6efdb1c28777d9230727066fdba03d2850be62b0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935908"
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

可以重复使用另一 SKU 中的磁盘，并修改定价或说明。 若要重复使用磁盘，请在回答“此 SKU 是否重复使用公共 SKU 中的映像”提示时选择“是”。 

如果 SKU 已标记为专用，并且套餐包含带有可重复使用磁盘的其他 SKU，则你需要指明该 SKU 将重复使用另一 SKU 中的磁盘。 此外，需要指定专用 SKU 的目标受众。

>[!NOTE]
>发布某个公共 SKU 后，无法将其设为专用 SKU。

<a name="select-an-image"></a>选择映像
------------------

可为专用 SKU 提供新磁盘，或重复使用另一 SKU 中已提供的相同磁盘，只需修改定价或说明即可。 若要重复使用磁盘，请在回答“此 SKU 是否重复使用公共 SKU 中的映像”提示时选择“是”。 

![指明要重复使用映像](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

确认该 SKU 要重复使用另一 SKU 中的映像之后，指明用作映像源的 SKU。

以下屏幕截图中的提示显示了如何指明专用 SKU 将重复使用选定 SKU 中的映像：

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

<a name="sync-private-subscriptions"></a>同步专用订阅
-------------------------

使用专用 SKU 或计划将订阅添加到已发布的套餐时，无需重新发布该套餐来添加受众信息。 只需使用 Azure 订阅 ID（计划和 SKU）或租户 ID（仅计划）即可添加受众。

<a name="previewing-private-offers"></a>预览专用套餐
-------------------------

在执行预览/暂存步骤期间，只有套餐级的预览订阅才能访问 SKU。 这属于测试阶段，在此期间，可以验证目标客户所看到的套餐的大致形式，这是所有发布类型的标准做法。

有权访问暂存套餐的套餐级预览订阅：

![预览订阅 ID](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

套餐上线后，只有受限制的受众订阅（手动输入或使用 CSV 输入）才能查看和部署专用 SKU。 我们建议**始终在专用 SKU 的受限制受众中包含你自己的订阅**，以进行验证。

>[!NOTE]
>为了进行调试，Microsoft 支持和工程团队也有权访问这些专用套餐。
