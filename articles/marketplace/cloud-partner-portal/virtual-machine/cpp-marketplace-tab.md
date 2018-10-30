---
title: Azure 云合作伙伴门户中的虚拟机“市场”选项卡 | Microsoft Docs
description: 介绍用于创建 Azure 市场 VM 套餐的“市场”选项卡。
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: aa43a6f1f7c757366e321e7da0fb1e47d4928cee
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638867"
---
# <a name="virtual-machine-marketplace-tab"></a>虚拟机“市场”选项卡

在“新建套餐”页的“市场”选项卡中，可向潜在客户提供营销、销售和法律信息与协议，以及管理从市场生成的潜在顾客。 这个较长的窗体划分为四个部分：“概述”、“营销项目”、“潜在顾客管理”和“法律条款”。 

## <a name="overview-section"></a>“概述”部分
在此部分输入有关 Azure 市场套餐的常规信息。  名称旁边附有星号 (*) 的字段表示必填字段。

![虚拟机“新建套餐”窗体上“市场”选项卡的“概述”部分](./media/publishvm_008.png)

下表描述了这些字段的用途和内容。

|  **字段**                |     **说明**                                                          |
|  ---------                |     ---------------                                                          |
| **标题**                 | 套餐的标题，通常是较长的正式名称。 此标题将在市场中突出显示。  最大长度为 50 个字符。 |
| **摘要**               | 解决方案用途或功能的简短描述。  最大长度为 100 个字符。 |
| **长摘要**          | 解决方案的用途或功能。  最大长度为 256 个字符。 |
| **说明**           | 解决方案的说明。  最大长度为 3000 个字符，支持简单 HTML 格式。 |
| **营销标识符**  | 与此套餐关联的唯一 URL，通常包含组织名称和解决方案名称，最大长度为 50 个字符。  例如： <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **预览订阅 ID** | 为预览版添加 1 到 100 个订阅标识符。 在套餐发布之后、上线之前，这些已加入白名单的订阅将有权访问该套餐。 |
| **有用链接**          | 可能最适合与该套餐关联的企业和技术类别的多项选择。  最多允许选择五个链接。  |
|  |  |


## <a name="marketing-artifacts-section"></a>“营销项目”部分

此第二部分划分为三个子部分：“徽标”、“屏幕截图”和“视频”。 只有徽标是必需的营销项目，但为了充分吸引客户的注意，我们强烈建议填写所有这些字段。

![虚拟机“新建套餐”窗体中“市场”选项卡的“营销项目”部分](./media/publishvm_009.png)

|  **字段**                |     **说明**                                                          |
|  ---------                |     ---------------                                                          |
| *徽标*  |  |
| **小型**                 | 40x40 像素 .ico 位图                                                      |
| **中型**                | 90x90 像素 .ico 位图                                                      |
| **大型**                 | 115x115 像素 .ico 位图                                                   |
| **宽型**                  | 255x115 像素 .ico 位图                                                    |
| **主图**                  | 815x290 位图  此徽标是可选的，但是，一旦上传主图，就无法将其删除。 |
| *屏幕截图*  | 屏幕截图是可选的，最多可为每个 SKU 上传 5 个屏幕快照。 |
| **名称**                  | 名称或标题 <!-- TODO - max char length? none specified in UI -->                               |
| **图像**                 | 屏幕截图图像，533x324 像素                                         |
| *视频*  |  |
| **名称**                  | 名称或标题 <!-- TODO - max char length? -->                              |
| **链接**                  | 视频 URL，托管在 YouTube 或 Vimeo 中                                        |
| **缩略图**             | 533x324 位图                                                               |
|  |  |


### <a name="logo-guidelines"></a>徽标准则

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

上传到云合作伙伴门户中的所有徽标都应遵循以下准则：

*  Azure 设计具有简单的调色板。 保持徽标上的主要和辅助颜色数较低。
*  Azure 门户的主题颜色为白色和黑色。 因此，应避免将这些颜色用作徽标的背景色。 使用一些可使徽标在 Azure 门户中突出显示的颜色。 建议使用简单的主颜色。 如果使用的是透明背景，请确保徽标/文本不使用白色、黑色或蓝色。
*  不要在徽标上使用渐变背景。
*  避免在徽标上放置文本，即使是公司或品牌名称也不可以。 徽标的外观应“平整”，并且应避免渐变。
*  不要拉伸徽标。

#### <a name="hero-logo"></a>特大徽标

主图徽标是可选的；但是，一旦上传主图，就无法将其删除。  主图徽标图标应遵循以下准则：

*  主图图标不允许使用黑色、白色和透明背景。
*  请避免在主图图标的背景中使用任何浅色。  发布者显示名称、计划标题和套餐详细摘要将以白色字体显示，因此必须与背景色之间有一定的反差。
*  设计主图徽标时请避免使用大量的文本。  列出套餐后，发布者名称、计划标题、套餐详细摘要和“创建”按钮以编程方式嵌入在主图图标内。 
* 在主图图标的右侧包含一个不使用的矩形，其大小为 415x100 像素，与左侧相距 370 像素。  

例如，以下主图图标适用于 Azure 容器服务。  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Azure 容器服务的示例主图图标](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>营销信息示例 

下图演示了营销信息在 Microsoft Windows Server 产品主页上的显示方式。

![适用于 Microsoft Windows Server 的示例产品页](./media/publishvm_011.png)


## <a name="lead-management-section"></a>“潜在顾客管理”部分
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

在第三个部分可以收集从 Azure 市场套餐生成的潜在顾客。 其中为此潜在顾客信息提供了以下存储选项（在下拉列表中）。

* **无** - 默认设置，即，不收集潜在顾客信息。
* Azure 表 - 写入到由某个连接字符串指定的 Azure 表。
* Dynamics CRM Online - 写入到由 URL 和身份验证凭据指定的 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 实例。
* HTTPS 终结点 - 作为 JSON 有效负载写入到指定的 HTTPS 终结点。
* Marketo - 写入到由服务器 ID、munchkin ID 和窗体 ID 指定的 [Marketo](https://www.marketo.com/) 实例。
* Salesforce - 写入到由对象标识符指定的 [Salesforce](https://www.salesforce.com/) 数据库。

成功发布套餐后，将验证潜在顾客连接，并将一个测试潜在顾客自动发送到配置的目标。 应持续管理潜在顾客信息，每当对客户管理体系结构做了更改后，就应该尽快更新这些设置。

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>“法律”部分

在最后一个部分，可以提供每个套餐所需的两个法律文档：“隐私政策”和“使用条款”。

|  **字段**                |     **说明**                                                          |
|  ---------                |     ---------------                                                          |
| **隐私政策 URL**    | 已发布的隐私政策的 URL                                            |
| **使用条款**          | 纯文本或简单 HTML 格式的政策。  <!-- TODO - max char length? -->       |
|  |  |

<br/>

在后面的 [支持](./cpp-support-tab.md) 选项卡中，提供套餐的技术资源和用户支持资源。

