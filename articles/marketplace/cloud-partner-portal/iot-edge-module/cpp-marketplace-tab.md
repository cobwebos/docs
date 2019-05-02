---
title: Azure IoT Edge 模块的 marketplace 说明 |Azure Marketplace
description: 创建 IoT Edge 模块的市场说明。
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: aa0c908e18233926965d8dc3cd1b474f876b8dda
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942427"
---
# <a name="iot-edge-module-marketplace-tab"></a>IoT Edge 模块“市场”选项卡

在“新建套餐”页的“市场”选项卡中，可向潜在客户提供营销、销售和法律信息与协议，以及管理从市场生成的潜在顾客。 此长格式被划分为四个部分：**概述**，**市场营销项目**，**潜在客户管理**，并且**法律**。


## <a name="overview"></a>概述

在此部分输入有关 Azure 市场套餐的常规信息。  名称旁边附有星号 (*) 的字段表示必填字段。

![IoT Edge 模块“新建套餐”窗体上“市场”选项卡的“概述”部分](./media/iot-edge-module-marketplace-tab-overview.png)

下表描述了这些字段的用途和内容。 必填字段用星号 (*) 表示。

|  **字段**                |     **说明**                                                          |
|  ---------                |     ---------------                                                          |
| **标题\***                 | 套餐的标题。 将在市场中突出显示。  最大长度为 50 个字符。 <!--ADD PICTURE IN ACTION-->|
| **摘要\***               | 套餐的简短摘要。 最大长度为 100 个字符。 <!--ADD PICTURE IN ACTION-->|
| **长摘要\***          | 套餐的较长摘要（不过，其内容可与“摘要”相同）。  最大长度为 256 个字符。 <!--ADD PICTURE IN ACTION-->|
| **说明\***           | 套餐的说明。  最大长度为 3000 个字符，支持简单 HTML 格式。<br/> 必须在底部包含“最低硬件要求”段落。 例如： <br/> <p><u>最低硬件要求：</u>Linux x64 和 arm32 OS，1 GB 的 RAM，500 Mb 的存储</p>
| **市场营销标识符\***  | 与此套餐关联的唯一 URL，通常包含组织名称和解决方案名称，最大长度为 50 个字符。  例如： <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleIoTEdgeModule`  |
| **预览订阅 ID** | 为预览版添加 1 到 100 个订阅标识符。 在套餐发布之后、上线之前，这些已加入允许列表的订阅将有权访问该套餐。 |
| **有用链接**          | 可能最适合与该套餐关联的企业和技术类别的多项选择。  最多允许添加 10 个类别。 请务必至少添加一个文档链接，以及 [Azure IoT 设备目录](https://catalog.azureiotsolutions.com/)中兼容的 IoT Edge 设备的一个链接。 |
| **建议的类别\*** | 最多选择 5 个类别。 这些类别将显示在产品详细信息页上。 在浏览器页中，所有 IoT Edge 模块将显示在“物联网”\>“IoT Edge 模块”类别下。|
|  |  |


### <a name="offer-example"></a>套餐示例

 以下示例演示套餐的“标题”、“摘要”、“说明”、“徽标”和“屏幕截图”字段在不同视图中的显示方式。

 
#### <a name="on-the-azure-marketplace-website"></a>在 Azure 市场网站上：

- 浏览套餐时：

    ![市场套餐在 Azure 市场网站中的显示方式 - 浏览](./media/iot-edge-module-ampdotcom-card.png)

- 查看套餐详细信息时：

    ![在网站中查看产品详细信息时 IoT Edge 模块的显示方式](./media/iot-edge-module-ampdotcom-pdp.png)


#### <a name="on-the-azure-portal-website"></a>在 Azure 门户网站上：

- 浏览套餐时：

    ![浏览 Azure 门户时 IoT Edge 模块的显示方式 #1](./media/iot-edge-module-portal-browse.png)

    ![浏览 Azure 门户时 IoT Edge 模块的显示方式 #2](./media/iot-edge-module-portal-product-picker.png)

- 搜索套餐时：

    ![在 Azure 门户中搜索时 IoT Edge 模块的显示方式](./media/iot-edge-module-portal-search.png)

- 查看套餐详细信息时：

    ![在门户中查看产品详细信息时 IoT Edge 模块的显示方式](./media/iot-edge-module-portal-pdp.png)


## <a name="marketing-artifacts"></a>营销项目

本节包含以下小节：**徽标**，**屏幕截图**，和**视频**。 

>[!Note]
>只有徽标是必需的营销项目，但为了充分吸引客户的注意，我们强烈建议填写所有这些字段。

![虚拟机“新建套餐”窗体中“市场”选项卡的“营销项目”部分](./media/publishvm_009.png)

|  **字段**                |     **说明**                                                          |
|  ---------                |     ---------------                                                          |
| *徽标*  | 参阅上面的屏幕截图，了解徽标的用法和使用位置。  |
| **小型\***                 | 40x40 像素 PNG 格式                                                     |
| **中型\***                | 90x90 像素 PNG 格式                                                     |
| **大型\***                 | 115x115 像素 PNG 格式                                                  |
| **宽\***                  | 255x115 像素 PNG 格式                                                   |
| **主图**                  | 815x290 像素 PNG 格式  此徽标是可选的，但是，一旦上传主图，就无法将其删除。 |
| *屏幕截图*  | 屏幕截图显示在产品详细信息页上。 它们能够十分直观地展示 IoT Edge 模块的作用及其工作原理。 例如，可以显示体系结构关系图或用例插图。 屏幕截图是可选的，最多可为每个 SKU 上传 5 个屏幕快照。 |
| **名称**                  | 名称或标题。 最大长度为 100 个字符。                             |
| **图像**                 | 屏幕截图图像，533x324 像素 PNG 格式                               |
| *视频*  | 视频显示在产品详细信息页上。 它们能够十分直观地展示 IoT Edge 模块的作用及其工作原理。 |
| **名称**                  | 名称或标题。 最大长度为 100 个字符。                             |
| **链接**                  | 视频 URL，托管在 YouTube 或 Vimeo 中                                        |
| **缩略图**             | 533x324 像素 PNG 格式                                                     |
|  |  |


### <a name="logo-guidelines"></a>徽标准则

<!-- It seems like this section could be better located in some common area, maybe an AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

上传到云合作伙伴门户中的所有徽标都应遵循以下准则：

*  Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。
*  Azure 门户的主题颜色为白色和黑色。 请避免将这两种颜色用作徽标的背景色。 使用可使徽标在 Azure 门户中突出显示的颜色。 建议使用简单的主颜色。 如果使用透明背景，请确保徽标/文本不使用白色、黑色或蓝色。
*  不要在徽标上使用渐变背景。
*  避免在徽标上放置文本，即使是公司或品牌名称也不可以。 徽标的外观应“平整”，并且应避免渐变。
*  不要拉伸徽标。


#### <a name="hero-logo"></a>特大徽标

特大徽标是可选的。

>[!Important]
>上传主图徽标后无法将其删除。

主图徽标应遵循以下准则： 

*  不允许使用黑色、白色和透明背景。
*  避免在徽标的背景中使用任何亮色。  发布者显示名称、计划标题和套餐详细摘要将以白色字体显示，因此必须与背景色之间有一定的反差。
*  设计徽标时请避免使用过多的文本。 列出套餐后，发布者名称、计划标题、套餐详细摘要和“创建”按钮以编程方式嵌入在徽标内。 
* 在主图徽标的右侧留出一个不使用的矩形空间。 此空白区域为 415x100 像素，与左侧相距 370 像素。  

<!-- P2: woudl be nice to also have an picture int he context of iot edge of the hero image
As an example, the following hero icon is for the Azure Container Service.

![Example hero icon for Azure Container Service](./media/publishvm_010.png)
-->


## <a name="lead-management"></a>潜在顾客管理

在此部分可以设置用于收集从 Azure 市场套餐生成的潜在顾客的选项。 可从下拉列表中选择以下存储选项。

* **无** - 默认设置，即，不收集潜在顾客信息。
* Azure 表 - 写入到由某个连接字符串指定的 Azure 表。
* Dynamics CRM Online - 写入到由 URL 和身份验证凭据指定的 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 实例。
* HTTPS 终结点 - 作为 JSON 有效负载写入到指定的 HTTPS 终结点。
* Marketo - 写入到由服务器 ID、munchkin ID 和窗体 ID 指定的 [Marketo](https://www.marketo.com/) 实例。
* Salesforce - 写入到由对象标识符指定的 [Salesforce](https://www.salesforce.com/) 数据库。

成功发布套餐后，将验证潜在顾客连接，并将一个测试潜在顾客自动发送到配置的目标。 

>[!Note]
>应持续管理潜在顾客信息，每当对客户管理体系结构做了更改后，就应该尽快更新这些设置。

<!-- there is missing some marketing imagess
1. inside azure portal the wide logo/regular logo
2. inside azure portal the top curation section
3. amp.com the pricing tab
4. amp.com the tile -->


## <a name="legal"></a>合法

此部分中，你可提供所需的每个产品/服务的两个法律文档：“隐私策略”和“使用条款”。

|  **字段**                    |     **说明**                                                          |
|  ---------                    |     ---------------                                                          |
| **隐私策略 URL\***      | 已发布的隐私政策的 URL                                            |
| **使用标准结构来实现\***  | 是否要使用标准 Microsoft 协定模板。  有关详细信息，请参阅[标准协定](https://docs.microsoft.com/azure/marketplace/standard-contract)。   |
| **使用条款\***            | *使用条款*为内联简单 HTML 或链接到你已发布的使用条款的使用页     |
|  |  |


## <a name="next-steps"></a>后续步骤

使用 [支持](./cpp-support-tab.md) 选项卡提供套餐的技术资源和用户支持资源。
