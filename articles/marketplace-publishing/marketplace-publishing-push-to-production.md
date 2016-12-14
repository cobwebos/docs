---
title: "将产品/服务部署到 Azure 应用商店 | Microsoft Docs"
description: "了解并分步遵循说明，以便将产品/服务（虚拟机映像、开发人员服务、数据服务等）部署到 Azure 应用商店。"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 8f79b891-84e2-4f41-ba0d-66420e2c6b2e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a91e8b3d64ec68ae43e7237c27488e5240585a4e


---
# <a name="deploy-your-offer-to-the-azure-marketplace"></a>将产品/服务部署到 Azure 应用商店
如果你对产品/服务感到满意（即，已测试客户方案、市场营销内容等）并为启动做好准备，请在“发布”选项卡上请求“推动到生产”。  

1. 在发布门户中，“演练”页下的四个步骤应该已完成并且为绿色。 对于虚拟机产品/服务，请确保遵循以下准则。
   
    ![绘制][img-pubportal-walkthru-checked]
2. 从左侧列表中选择“发布”选项卡。
   
    ![绘制][img-pubportal-menu-publish]
3. 单击按钮“请求批准推送到生产”。 发出请求后，批准团队会执行最终审查，随后产品/服务将在 Azure 应用商店中可用。
   
    ![绘制][img-pubportal-publish-pushproduction]

> [!IMPORTANT]
> 如果使用的是虚拟机，当单击按钮“请求批准推送到生产”时，后续步骤将在后台执行。 你将能够在发布门户的“发布”选项卡下查看每个步骤的进度。 必须定期查看此页面（直到状态显示“已列出”），了解需要更正的任何失败信息。
> 
> * 最初生产请求将发送到验证 vhd 的认证团队。 但是，如果要更新已列出的产品/服务并且请求仅做了营销更改，可以跳过认证步骤。
> * 在下一步中，请求将发送到内容验证团队，该团队验证产品/服务的市场营销内容。
> * 如果上述步骤都成功，产品/服务将批准用于生产。 在此期间，在发布门户中状态将变为“已列出”。 但是，此“已列出”状态并不表示过程已完成。 需要先完成以下步骤，然后产品/服务才能在 Azure 应用商店中可用。
> * 在上述步骤中产品/服务批准用于生产之后，产品/服务的复制可以跨所有 Azure 数据中心启动。 通常，需要 24-48 小时的时间才能完成复制，不过可能需要最多一周，具体取决于 vhd 的大小。 但是，如果要更新已列出的产品/服务并且它仅做了营销更改，可以更快地进行复制。
> * 复制完成后，产品/服务将在 Azure 应用商店中可用。
> 
> 当产品/服务处于“草稿”状态时（即，永远不会“推送到过渡”或“推送到生产”），始终可以将其删除。 在“历史记录”选项卡上，单击页面底部的“放弃草稿”按钮，可以删除草稿。
> 
> 

## <a name="production-checklist-for-all-virtual-machine-offers"></a>所有虚拟机产品/服务的生产清单
* 确保你是 Microsoft Azure 认证的合作伙伴
* 在 SKU 选项卡下，仅当 SKU 是解决方案模板的一部分时，才可将“针对应用商店隐藏此 SKU，因为它始终应通过解决方案模板购买”选项标记为“是”。 在其他所有情况下，此选项应始终标记为“否”。
* 请记住：在 SKU 列出后，不应更改 SKU 可见性设置。 我们不支持此功能。
* 确保徽标遵守下面给出的 Azure 应用商店徽标准则。
* 产品/服务和 SKU 描述不应该相同。
* SKU 标题和产品/服务详细摘要不应该相同。
* SKU 标题和产品/服务摘要不应该相同。
* 对于具有多个 SKU 的产品/服务，SKU 标题不应该相同。

**Azure 应用商店徽标准则**

* Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。
* Azure 门户的主题颜色为白色和黑色。 因此，应避免将这些颜色用作徽标的背景色。 使用一些颜色来使徽标在 Azure 门户中突出显示。 我们建议使用简单的主要颜色。 如果使用的是透明背景，请确保徽标/文本不为白色或黑色。
* 不要在徽标上使用渐变背景。
* 避免在徽标上放置文本，即使是公司或品牌名称也不可以。
* 徽标的外观应平整，并且应避免渐变。
* 徽标应未被拉伸。

**特大徽标的其他准则：**

* 特大徽标是可选项。 发布者可以选择不上载特大徽标。 **但是，在上载特大图标后，将无法将其从发布门户中删除。此时，合作伙伴必须遵循特大图标的 Azure 应用商店准则，否则产品/服务将不批准用于生产。**
* 发布者显示名称、SKU 标题和产品/服务详细摘要将以白色字体颜色显示。 因此，应避免在特大图标的背景中保留任何浅色。 特大图标不允许使用黑色、白色和透明背景。
* 在产品/服务推出后，发布者显示名称、SKU 标题、产品/服务详细摘要和创建按钮将以编程方式嵌入在特大徽标内。 因此，在设计特大徽标时，不应输入任何文本。 只需在右侧留出空白区域，因为文本（即 发布者显示名称、SKU 标题、产品/服务详细摘要）将以编程方式包含在内。 右侧文本空白区域应为 415x100（并按 370px 从左侧偏移）。

## <a name="additional-production-checklist-for-already-listed-virtual-machine-offers"></a>已列出的虚拟机产品/服务的附加生产清单
* 检查是否已经存在与你公司的产品/服务同名的产品/服务。 如果存在，应在现有产品/服务中添加新的 SKU 版本，而不是创建新的重复产品/服务。
* 数据磁盘不应在同一 SKU 的两个版本之间进行更改。
* Azure 应用商店不支持对已列出 SKU 进行定价更改，因为它会影响现有客户的计费。 确保不在 SKU 可用的区域中更改已列出 SKU 的定价。 但是，可以添加新的 SKU 或将新区域添加到现有 SKU。

## <a name="next-steps"></a>后续步骤
一旦产品/服务可用，便会测试客户方案，以验证所有合同和功能在生产环境中正常运行，因为已在过渡环境中进行测试和验证。

## <a name="see-also"></a>另请参阅
* [入门：如何将产品/服务发布到 Azure 应用商店](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]:media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]:media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]:media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png



<!--HONumber=Nov16_HO3-->


