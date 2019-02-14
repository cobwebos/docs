---
title: Power BI 应用产品/服务的店面详细信息 - Azure 市场 | Microsoft Docs
description: 配置 Power BI 应用产品/服务在 Microsoft AppSource 市场中的“店面详细信息”字段。
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: pbutlerm
ms.openlocfilehash: ffc0d8e9e41cf4d202b4040ce3f09e7180101c8b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665838"
---
# <a name="power-bi-apps-storefront-details-tab"></a>Power BI 应用“店面详细信息”选项卡

使用“新建产品/服务”页的“店面详细信息”选项卡，可以向潜在客户提供市场营销、销售和法律信息。 此选项卡还指定如何管理市场带来的潜在客户。 这一长窗体分为六个部分：“产品/服务详细信息”、“应用一览详细信息”、“市场营销项目”、“法律”、“客户支持”和“潜在客户管理”。  如果字段标签上追加​​有星号 (*)，表示此为必填字段。


## <a name="offer-details-section"></a>“产品/服务详细信息”部分

在此部分中，输入 AppSource 产品/服务的常规信息。

![“店面详细信息”选项卡的“产品/服务详细信息”部分](./media/offer-details-section.png)

下表介绍了这些字段的名称和用途。

|   字段               |   说明                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------|
| **套餐摘要**     | 应用用途简述。 最大长度为 100 个字符。                             |
| **套餐说明** | 应用说明。 最大长度为 3000 个字符，支持简单 HTML 格式。 |
|                       |                                                                                         |


## <a name="listing-details-section"></a>“商品详细信息”部分

在这第二部分中，可以提供应用的其他上下文：它通常用于什么行业、最适合归入什么类别、兼容产品以及关联搜索词。

![“店面详细信息”选项卡的“商品详细信息”部分](./media/listing-details-section.png)

 下表介绍了这些字段的名称和用途。
 
|   字段                                  |   说明                                                        |
| --------------                           | ---------------------                                                |
| **行业**                           | 选择应用最适合的行业。 如果应用与多个行业相关，可以将此字段留空。      |
| **类别**                           | 选择与应用相关的类别。 最多选择 3 个类别。     |
| **应用的帮助链接**               | 提供应用联机帮助的页面的 URL           |
| **与应用兼容的产品(最多 3 个)** | 列出与应用兼容的具体产品。 最多可列出 3 个产品。 若要列出产品，请单击“新建”旁边的加号，此时会创建一个新的文本字段，请在其中输入可与应用配合使用的产品名称。      |
| **搜索关键字(最多 3 个)**              | AppSource 允许客户基于关键字执行搜索。 可以输入关键字集，搜索时会根据这些关键字向用户显示你的应用程序。 例如，如果应用名为“我的电子邮件应用”，那么“电子邮件”、“邮件”和“邮件应用”都可以是关键字。 请选择用户在 AppSource 搜索框中搜索应用时可能使用的字词。 |
|  |  |


## <a name="marketing-artifacts-section"></a>“营销项目”部分

在这第三部分中，可以上传品牌和市场营销材料。  它分为四个子部分：“徽标”、“视频”、“文档”和“屏幕截图”。 徽标和屏幕截图是必需的营销项目，但为了充分吸引客户的注意，强烈建议填写所有字段。

![“店面详细信息”选项卡的“营销项目”部分](./media/marketing-artifacts-section.png)

 
|    字段                             |    说明                                                    |
|   -----------                        |    -------------                                                  |
| *徽标*                              |                                                                   |
| **产品/服务徽标(png 格式，48x48)**   | 显示在 AppSource 上的应用概览或应用结果（当搜索完成时）中。 仅支持分辨率为 48px\*48px 的 png 格式。  |
| **产品/服务徽标(png 格式，216x216)** | 显示在 AppSource 上的应用详细信息页中。  仅支持分辨率为 216px\*216px 的 png 格式。  |
| *视频*                             |                                                                   |
| **名称**                             | 应用名称或标题                                          |
| **URL**                              | 托管在 YouTube 或 Vimeo 中的视频 URL                              |
| **缩略图**                        | 应用的缩略图图像。  仅支持分辨率为 1280px\*720px 的 png 格式。   |
| 文档                          | 可选，但最多可上传 3 个文档。 此处上传的文档显示在 AppSource 中的“了解更多”下。  |
| **名称**                             | 支持文档的名称或标题                              |
| **文件**                             | 上传文档必须采用 pdf 格式                             |
| *屏幕截图*                        | 可选，但最多可上传 5 个屏幕截图。                        |
| **名称**                             | 屏幕截图的名称或标题                                       |
| **图像**                            | 上传屏幕截图，必须采用 png 格式，且分辨率必须为 1280px\*720px  | 
|   |   |


### <a name="logo-guidelines"></a>徽标准则

所有上传到[云合作伙伴门户](https://cloudpartner.azure.com)的徽标都应遵循以下准则：

- 不要在徽标上使用渐变背景。
- 避免在徽标上添加文本（包括公司名称或品牌名称）。 徽标的外观应“平整”，并且应避免渐变。
- 不要拉伸徽标。


## <a name="legal-section"></a>“法律”部分

在这第四部分中，可以提供每个产品/服务的两个必需法律文档：“隐私策略”和“使用条款”。

![“店面详细信息”选项卡的“法律”部分](./media/legal-section.png)

|   字段                |   说明                           |
|------------------------|--------------------------------------   |
| **隐私政策 URL** | 已发布的隐私政策的 URL       |
| **使用条款**       | 纯文本或简单 HTML 格式的策略     |
|  |  |


## <a name="customer-support-section"></a>“客户支持”部分

提供联机客户支持页的“支持 URL”。  此联机支持页最好为客户提供多种联系方式（如电话、电子邮件和实时聊天）。 


## <a name="lead-management-section"></a>“潜在顾客管理”部分

在最后一个部分中，可以收集 AppSource 产品/服务带来的潜在客户。 其中为此类潜在客户信息提供了以下存储选项（在下拉列表中）。

|    字段               |   潜在客户目标                               |
|------------------------|--------------------------------------            |
|  无              | 不收集潜在客户（默认设置）。  |
| **Azure Blob (已弃用)** | 由容器名称和连接字符串指定的 [Azure blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)。  此选项已遭弃用；请改用“Azure 表”。  |
| **Azure 表**        | 由连接字符串指定的 [Azure 表](https://docs.microsoft.com/azure/cosmos-db/table-storage-overview)  |
| **Dynamics CRM Online** | 由 URL 和身份验证凭据指定的 [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) 实例 |
| **HTTPS 终结点**     | 作为 JSON 有效负载的指定 HTTPS 终结点   |
| **Marketo**            | 由服务器 ID、munchkin ID 和窗体 ID 指定的 [Marketo](https://www.marketo.com/) 实例   |
| **Salesforce**         | 由对象标识符指定的 [Salesforce](https://www.salesforce.com/) 数据库 |
|  |  |

在你发布产品/服务后，潜在客户连接会经过验证，并且测试潜在客户会自动发送到指定目标。 应持续管理潜在客户信息，并及时更新这些设置，以反映当前客户管理体系结构。


## <a name="next-steps"></a>后续步骤

在下一个[“联系人”](./cpp-contacts-tab.md)选项卡中，提供产品/服务的技术资源和用户支持资源。
