---
title: 教程：从必应地图迁移到 Azure Maps | Microsoft Azure Maps
description: 介绍如何从必应地图迁移到 Microsoft Azure Maps 的教程。 本指南介绍如何切换到 Azure Maps API 和 SDK。
author: rbrundritt
ms.author: richbrun
ms.date: 9/10/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 643e49bdba76051c873ed549d5f6c21487f34056
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90107918"
---
# <a name="migrate-from-bing-maps-to-azure-maps"></a>从必应地图迁移到 Azure Maps

本指南介绍如何将基于 Web、移动和服务器的应用程序从必应地图迁移到 Azure Maps 平台。 本指南包括有关如何迁移到 Azure Maps 的比较代码示例、迁移建议和最佳做法。

## <a name="azure-maps-platform-overview"></a>Azure Maps 平台概述

Azure Maps 为各行各业的开发人员提供强大的地理空间功能，并搭配了可用于为 Web 和移动应用程序提供地理环境的最新地图数据。 Azure Maps 是一组兼容 Azure One API 的 REST API，适用于地图、搜索、路线规划、交通、时区、地理围栏、地图数据、天气数据以及其他随 Web 和 Android SDK 一起提供的更多服务，可使开发变得简单、灵活且可跨多个平台移植。 [Azure Maps 在 Power BI 也可用](power-bi-visual-getting-started.md)。

## <a name="high-level-platform-comparison"></a>概略性的平台比较

下表提供了必应地图功能的概略性列表，以及针对 Azure Maps 中的这些功能的相对支持。 此列表不包含其他 Azure Maps 功能，如辅助功能、地理围栏 API、交通服务、空间操作、直接地图图块访问和批处理服务。

| 必应地图功能                     | Azure Maps 支持 |
|---------------------------------------|:------------------:|
| Web SDK                               | ✓                  |
| Android SDK                           | ✓                  |
| iOS SDK                               | 已计划            |
| UWP SDK                               | 已计划            |
| WPF SDK                               | 已计划            |
| REST 服务 API                     | ✓                  |
| 自动建议                           | ✓                  |
| 走向（包括卡车）          | ✓                  |
| 距离矩阵                       | ✓                  |
| 提升                            | 已计划            |
| 图像 - 静态地图                  | ✓                  |
| 图像元数据                      | ✓                  |
| 等时线                            | ✓                  |
| 本地见解                        | ✓                  |
| 本地搜索                          | ✓                  |
| 位置识别                  | ✓                  |
| 位置（正向/反向地理编码） | ✓                  |
| 优化的行程路线            | 已计划            |
| 对齐道路                         | ✓                  |
| 空间数据服务 (SDS)           | 部分            |
| 时区                             | ✓                  |
| 交通事故                     | ✓                  |
| 配置驱动的地图             | 空值                |

必应地图提供基本的基于密钥的身份验证。 Azure Maps 提供基本的基于密钥的身份验证以及高度安全的 Azure Active Directory 身份验证。

## <a name="licensing-considerations"></a>许可注意事项

从必应地图迁移到 Azure Maps 时，应考虑与许可相关的以下内容。

-   Azure Maps 针对交互式地图的使用量（基于所加载地图图块的数量）收费，而必应地图则针对地图控件（会话）的加载收费。 在 Azure Maps 上，地图图块会自动进行缓存以降低开发人员的成本。 每加载 15 个地图图块，就会生成一个 Azure Maps 事务。 交互式 Azure Maps SDK 使用 512 像素的图块，平均每个页面视图生成一个或不到一个事务。

-   Azure Maps 允许其平台中的数据存储在 Azure 中。 根据[使用条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)，还可以在其他位置缓存它，时间长达六个月。

下面是 Azure Maps 的一些许可相关资源：

-   [Azure Maps 定价页](https://azure.microsoft.com/pricing/details/azure-maps/)
-   [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
-   [Azure Maps 使用条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)（包括在 Microsoft Online Services 条款中）
-   [在 Azure Maps 中选择适当的定价层](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>建议的迁移计划

下面是概略性迁移计划。

1.  对应用程序所使用的必应地图 SDK 和服务进行清点，并验证 Azure Maps 是否提供可供你迁移到其中的替代 SDK 和服务。
2.  在 <https://azure.com> 创建 Azure 订阅（如果还没有）。
3.  创建 Azure Maps 帐户（[文档](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)）和身份验证密钥或 Azure Active Directory（[文档](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)）。
4.  迁移应用程序代码。
5.  测试已迁移的应用程序。
6.  将已迁移的应用程序部署到生产环境。

## <a name="azure-maps-technical-resources"></a>Azure Maps 技术资源

下面是 Azure Maps 的有用技术资源的列表。

-   概述： https://azure.com/maps
-   文档：<https://aka.ms/AzureMapsDocs>
-   Web SDK 代码示例：<https://aka.ms/AzureMapsSamples>
-   开发人员论坛：<https://aka.ms/AzureMapsForums>
-   视频：<https://aka.ms/AzureMapsVideos>
-   博客：<https://aka.ms/AzureMapsBlog>
-   Azure Maps 反馈 (UserVoice)：<https://aka.ms/AzureMapsFeedback>

## <a name="migration-support"></a>迁移支持

开发人员可通过[论坛](https://aka.ms/AzureMapsForums)或众多 Azure 支持选项之一寻求迁移支持：<https://azure.microsoft.com/support/options/>

## <a name="new-terminology"></a>新术语 

下面列出了常见必应地图术语，这些术语在 Azure Maps 中描述为不同的术语。

| 必应地图术语                    | Azure Maps 术语                                                |
|-----------------------------------|----------------------------------------------------------------|
| “鸟瞰图”                            | 卫星图或鸟瞰图                                            |
| 方向                        | 也可称为路线规划                             |
| 实体                          | 几何图形或特征                                         |
| `EntityCollection`                | 数据源或层                                           |
| `Geopoint`                        | 位置                                                       |
| `GeoXML`                          | 空间 IO 模块中的 XML 文件                             |
| 地面叠加层                    | 图像层                                                    |
| 混合（参考地图类型） | 道路卫星图                                           |
| Infobox                           | 弹出项                                                          |
| 位置                          | 位置                                                       |
| `LocationRect`                    | 边界框                                                   |
| 映射类型                          | 地图样式                                                      |
| 导航栏                    | “地图样式”选取器、“缩放”控件、“间距”控件、“指南针”控件 |
| Pushpin                           | 气泡层、符号层或 HTML 标记                      |

## <a name="next-steps"></a>后续步骤

请参阅以下文章，详细了解如何迁移必应地图应用程序：

> [!div class="nextstepaction"]
> [迁移 Web 应用](migrate-from-bing-maps-web-app.md)

> [!div class="nextstepaction"]
> [迁移 Web 服务](migrate-from-bing-maps-web-services.md)
