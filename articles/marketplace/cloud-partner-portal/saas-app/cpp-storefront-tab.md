---
title: Azure SaaS 应用程序产品/服务的店面配置 |Microsoft Docs
description: 在 Azure 市场为 SaaS 应用程序产品/服务配置店面。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 25fdce2381cc6dd65559a2450a42de5389589863
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53195370"
---
# <a name="saas-application-storefront-details-tab"></a>SaaS 应用程序店面详细信息选项卡

本文介绍如何使用店面详细信息选项卡来描述 SaaS 应用，并提供市场营销资产。 此选项卡包括下列窗体：概述、营销项目、潜在顾客管理和法律。 


## <a name="overview"></a>概述

“概述”窗体包含下一个屏幕截图中显示的必填字段和可选字段。 名称旁边附有星号 (*) 的字段表示必填字段。

![店面“概述”窗体](./media/saas-storefront-overview.png)

下表介绍可为产品/服务提供的店面详细信息。

|  **产品/服务字段**   |  **说明**  |
|  ---------------   |  ---------------  |
|      套餐摘要              |        概述产品/服务的价值主张。 它将显示在产品/服务的搜索页上。 此内容不应超过 100 个字符。           |
|      套餐说明              |      显示在应用程序详细信息页上的描述。 允许的最大长度为 1300 个字符。 可以使用基本 HTML 标志标记来设置内容的格式。 例如，&lt;p&gt;、&lt;h1&gt;、&lt;h2&gt; 以及 &lt;li&gt;。 要查看格式化描述的外观，请使用联机实时 HTML 工具，例如 [http://htmledit.squarefree.com](http://htmledit.squarefree.com)             |
|          行业          |        选择产品/服务最适合的行业。 如果应用与多个行业相关，可选择其中最相关的两项。           |
|          建议的类别（最多 3 个）         |        选择产品/服务最适合的类别。 最多可以选择三个类别。           |
|           应用版本         |        输入应用程序的版本号。           |
|          搜索关键字（最多 3 个）          |      最多可输入三个搜索关键字，客户可用于在市场店面网站中查找你的应用程序。             |

## <a name="marketing-artifacts"></a>营销项目

使用“营销项目”窗体标识 Azure 市场的营销资产，例如徽标、视频、屏幕截图和文档。

![店面“营销项目”窗体](./media/saas-storefront-artifacts.png)

下表描述了“营销项目”字段。

|  **产品/服务字段**   |  **说明**  |
|  ---------------   |  ---------------  |
|                    |                   |
|                    |                   |
|                    |                   |
|          徽标          |        如果这是“通过 Microsoft 销售”SaaS 应用，应提供所有徽标图像。 如果只是一个列表，则仅需要提供 2 个徽标。 对于上传到云合作伙伴门户中的徽标，请遵循以下准则：<br><ul><li>保持徽标上的主要和辅助颜色数较低。 Azure 设计具有简单的调色板。 </li><li>请避免将黑色或白色用作徽标的背景色。 Azure 门户的主题颜色为白色和黑色。 请改用可使徽标在 Azure 门户中突出显示的颜色。 建议使用简单的主颜色。 如果使用的是透明背景，请确保徽标和文本不使用白色、黑色或蓝色。 </li><li>不要在徽标上使用渐变背景。 </li><li>避免在徽标上放置文本，即使是公司或品牌名称也不可以。 徽标的外观应“平整”，并且应避免渐变。</li><li>徽标图像不应拉伸。</li></ul>            |
|          视频          |         可添加产品/服务的视频链接。 可以使用 YouTube 和/或 Vimeo 视频链接，这将随产品/服务一起呈现给客户。 此外，还需要输入视频的缩略图像，采用 1280 x 720 像素的 png 图像。 每个产品/服务最多可附带 4 个视频。          |
|          文档          |          可将营销文档添加到产品/服务。 所有文档必须采用 PDF 格式，每个产品/服务最多可以有三个文档。         |
|        屏幕截图            |         可添加产品/服务的屏幕截图。 每个产品/服务最多可添加五张屏幕截图。 图像最大尺寸为 1280 x 720 像素。          |
|       有用链接             |       可添加产品/服务的外部 URL ，帮助指向客户想要查看的体系结构图或其他网站。            |

### <a name="marketing-examples"></a>市场营销示例

接下来的屏幕截图显示市场搜索结果的示例。

![市场搜索结果](./media/saas-marketplace-search-result.png)

下图显示了在客户单击搜索结果中的产品/服务磁贴后，商品/服务在市场中的显示方式。

![市场搜索结果详细信息](./media/saas-marketplace-search-result-details.png)

## <a name="lead-management"></a>线索管理

若要配置潜在顾客管理，请从下拉列表中选择“潜在顾客目标”。 接下来的屏幕截图显示可用的目标。

![店面详细信息潜在顾客管理](./media/saas-storefront-lead-destination.png)

>[!TIP] 
>选择信息图标以查看此消息：“选择用于存储潜在顾客的系统。 在[此处](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)了解如何连接 CRM 系统。”

## <a name="legal"></a>合法

使用“法律”窗体提供每个产品/服务所需的法律文档。

![店面详细信息“法律”窗体](./media/saas-storefront-lead-legal.png)

提供以下信息：

- 隐私策略 URL - 输入应用的隐私策略的链接。
- 使用条款 - 输入应用的使用条款。 客户必须接受这些条款才能试用应用。

## <a name="next-steps"></a>后续步骤

[“联系人”选项卡](./cpp-contacts-tab.md)
