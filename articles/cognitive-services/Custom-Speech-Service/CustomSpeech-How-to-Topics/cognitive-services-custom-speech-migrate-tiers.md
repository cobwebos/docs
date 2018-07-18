---
title: 从 Azure 上的自定义语音服务迁移终结点的定价层 | Microsoft Docs
description: 了解如何将认知服务中自定义语音服务终结点的部署从 S0 和 S1 层迁移到 S2 层。
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 07/05/2017
ms.author: panosper
ms.openlocfilehash: 6d92459deb3464cd97c215cbf9a8320628b6da80
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365743"
---
# <a name="migrate-deployments-to-the-new-pricing-model"></a>将部署迁移到新的定价模型
自 2017 年 7 月起，自定义语音服务提供[新的定价模型](https://azure.microsoft.com/pricing/details/cognitive-services/custom-speech-service/)。 新模型*更易于理解*、*更易于计算成本*，且在缩放方面*更灵活*。 为了缩放，Microsoft 引入了缩放单元的概念。 每个缩放单元可处理五个并发请求。 旧模型中并发请求的缩放设置为层 S0 5 个并发请求，并且设置为层 S1 12个并发请求。 我们已经开放了这些限制，以便为你的用例需求提供更大的灵活性。

如果你运行旧的 S0 或 S1 层，我们建议你将现有部署迁移到新的 S2 层。 新的 S2 层涵盖了 S0 和 S1 层。 可以在下图中看到可用选项：

![“选择定价层”页](../../../media/cognitive-services/custom-speech-service/custom-speech-pricing-tier.png)

Microsoft 以半自动方式处理迁移。 首先，你通过选择新的定价层来触发迁移。 然后，我们会自动迁移你的部署。

从旧层到缩放单元的映射如下表所示：

| 层 | 并发请求（旧模型） | 迁移 | 并发请求 |
|----- | ----- | ---- | ---- |
| S0 |  5   |   => 具有 1 个缩放单元的 **S2** |   5 |
| S1 |  12  |   => 具有 3 个缩放单元的 **S2** |  15 |

若要迁移到新层，请执行以下操作：

## <a name="step-1-check-your-existing-deployment"></a>步骤 1：检查现有部署
转到[自定义语音服务门户](http://cris.ai)，并检查现有部署。 在我们的示例中，有两个部署。 一个部署在 S0 层上运行，另一个部署在 S1 层上运行。 部署显示在下表的“部署选项”列中：

![“部署”页](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments.png)

## <a name="step-2-select-your-new-pricing-tier-in-the-azure-portal"></a>步骤 2：在 Azure 门户中选择新的定价层
1. 打开新的浏览器标签页，然后登录 [Azure 门户](http://ms.portal.azure.com/)。 

2. 在“认知服务”窗格的“订阅”列表中，选择自定义语音订阅。 

3. 在订阅的窗格中，选择“定价层”。

    ![“定价层”链接](../../../media/cognitive-services/custom-speech-service/custom-speech-update-tier.png)

4. 在“选择定价层”页上，选择“S2 标准”。 此定价层是新式已简化且更灵活的定价层。

5. 选择“选择”按钮。

    ![“选择定价层”页](../../../media/cognitive-services/custom-speech-service/custom-speech-update-pricing.png)

## <a name="step-3-check-the-migration-status-in-the-custom-speech-service-portal"></a>步骤 3：在自定义语音服务门户中检查迁移状态
返回自定义语音服务门户，检查你的部署。 （如果浏览器窗口仍处于打开状态，请刷新它。） 

相关部署的状态应已切换到“正在处理”。 还可以通过选中“部署选项”列来验证迁移。 现在你可以在那里找到有关缩放单元和日志记录的信息。 缩放单元应反映你以前的定价层。 还应启用“日志记录”，如下表所示：

![“部署选项”列](../../../media/cognitive-services/custom-speech-service/custom-speech-deployments-new.png)


> [!NOTE]
> 如果在迁移期间有任何问题，请与我们联系。
>

## <a name="next-steps"></a>后续步骤
有关更多教程，请参阅：
* [创建自定义声学模型](cognitive-services-custom-speech-create-acoustic-model.md)
* [创建自定义语言模型](cognitive-services-custom-speech-create-language-model.md)
* [创建自定义语音转文本终结点](cognitive-services-custom-speech-create-endpoint.md)
