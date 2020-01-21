---
title: 教程：从 Google Maps 迁移到 Azure Maps | Microsoft Azure Maps
description: 介绍如何从 Google Maps 迁移到 Microsoft Azure Maps 的教程。 本指南介绍如何切换到 Azure Maps API 和 SDK。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 0e841b1f386d45ddb4af8598855d8e739750307e
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910735"
---
# <a name="migrate-from-google-maps-to-azure-maps"></a>从 Google Maps 迁移到 Azure Maps

本教程介绍如何将基于 Web、移动和服务器的应用程序从 Google Maps 迁移到 Microsoft Azure Maps 平台。 本教程包括有关如何迁移到 Azure Maps 的比较代码示例、迁移建议和最佳做法。

## <a name="azure-maps-platform-overview"></a>Azure Maps 平台概述

Azure Maps 为各行各业的开发人员提供强大的地理空间功能，并搭配了定期更新的地图数据，用于为 Web 和移动应用程序提供地理环境。 Azure Maps 有一组兼容 Azure One API 的 REST API，适用于地图、搜索、路线、交通、时区、地理位置、地理围栏、地图数据、天气、出行和空间操作，随 Web 和 Android SDK 一起提供，可以使开发变得简单、灵活且可跨多个平台移植。

## <a name="high-level-platform-comparison"></a>概略性的平台比较

下表提供了 Google Maps 功能的概略性列表，以及针对 Azure Maps 中的这些功能的相对支持。 此列表不包含其他 Azure Maps 功能，如辅助功能、地理围栏 API、等时线、空间操作、直接地图图块访问、批处理服务和数据覆盖率比较（即图像覆盖率）。

| Google Maps 功能         | Azure Maps 支持                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 已计划                                |
| REST 服务 API           | ✓                                      |
| 方向（路线规划）        | ✓                                      |
| 距离矩阵             | ✓                                      |
| Elevation                   | 已计划                                |
| 地理编码（正向/反向） | ✓                                      |
| 地理位置                 | 空值                                    |
| 位置搜索               | ✓                                      |
| 位置详细信息              | 空值 – 网站和电话号码可用 |
| 位置照片               | 空值                                    |
| 位置自动完成          | ✓                                      |
| 静态地图                 | ✓                                      |
| 静态街道视图          | 空值                                    |
| 时区                   | ✓                                      |
| 嵌入了地图的 API           | 空值                                    |
| 地图 URL                    | 空值                                    |

Google Maps 提供基本的基于密钥的身份验证。 Azure Maps 提供基本的基于密钥的身份验证以及高度安全的 Azure Active Directory 身份验证。

## <a name="licensing-considerations"></a>许可注意事项

从 Google Maps 迁移到 Azure Maps 时，应考虑与许可相关的以下几点。

- Azure Maps 根据所加载地图图块的数量针对交互式地图的使用收费，而 Google Maps 则针对地图控件的加载收费。 在交互式 Azure Maps SDK 中，地图图块会自动进行缓存以降低开发人员的成本。 每加载 15 个地图图块，就会生成一个 Azure Maps 事务。 交互式 Azure Maps SDK 使用 512 像素的图块，平均每个页面视图生成一个或不到一个事务。
- 将 Google Maps Web 服务中的静态地图图像替换为 Azure Maps Web SDK 通常会更加经济高效，因为这样做会使用地图图块，除非用户平移并缩放地图，否则它们通常只为每个地图负载生成一小部分事务。 Azure Maps Web SDK 提供了禁用平移和缩放的选项。 此外，Azure Maps Web SDK 提供了比静态地图 Web 服务更多的可视化选项。
- Azure Maps 允许其平台中的数据存储在 Azure 中。 根据[使用条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)，还可以在其他位置缓存它，时间长达六个月。

下面是 Azure Maps 的一些相关资源：

- [Azure Maps 定价页](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps 使用条款](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)（包括在 Microsoft Online Services 条款中）
- [在 Azure Maps 中选择适当的定价层](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>建议的迁移计划

下面是概略性迁移计划。

1. 对应用程序所使用的 Google Maps SDK 和服务进行清点，并验证 Azure Maps 是否提供可供你迁移到其中的替代 SDK 和服务。
2. 在 [https://azure.com](https://azure.com) 创建 Azure 订阅（如果还没有）。
3. 创建 Azure Maps 帐户（[文档](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)）和身份验证密钥或 Azure Active Directory（[文档](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)）。
4. 迁移应用程序代码。
5. 测试已迁移的应用程序。
6. 将已迁移的应用程序部署到生产环境。

## <a name="azure-maps-technical-resources"></a>Azure Maps 技术资源

下面是 Azure Maps 的有用技术资源的列表。

- 概述：[https://azure.com/maps](https://azure.com/maps)
- 文档：[https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK 代码示例：[https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 开发人员论坛：[https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- 视频：[https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- 博客：[https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps 反馈 (UserVoice)：[https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>迁移支持

开发人员可通过[论坛](https://aka.ms/AzureMapsForums)或众多 Azure 支持选项之一寻求迁移支持：[https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何迁移 Google Maps 应用程序：

> [!div class="nextstepaction"]
> [迁移 Web 应用](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [迁移 Android 应用](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [迁移 Web 服务](migrate-from-google-maps-web-services.md)
