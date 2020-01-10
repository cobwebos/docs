---
title: 从 Google Maps 迁移 |Microsoft Docs
description: 有关如何从 Google Maps 迁移到 Microsoft Azure Maps 的教程。 指导指导如何切换到 Azure Maps Api 和 Sdk。
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6709c42b19b18c8cae783a6b4ecc2c0721e1217d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770299"
---
# <a name="migrate-from-google-maps"></a>从 Google Maps 迁移

本教程介绍如何将基于 web、移动和服务器的应用程序从 Google Maps 迁移到 Microsoft Azure Map 平台。 本教程包括有关迁移到 Azure Maps 的比较代码示例、迁移建议和最佳实践。

## <a name="azure-maps-platform-overview"></a>Azure Maps 平台概述

Azure Maps 为开发人员提供了所有行业强大的地理空间功能，并将其与定期更新的地图数据一起打包，为 web 和移动应用程序提供地理环境。 Azure Maps 提供了一个适用于地图、搜索、路由、流量、时区、地理位置、地理围栏、地图数据、天气、移动和空间操作的 Azure One API 兼容集，同时提供 Web 和 Android Sdk，使开发变得简单、灵活和跨多个平台移植。

## <a name="high-level-platform-comparison"></a>高级平台比较

下表提供了 Google Maps 功能的高级列表，以及 Azure Maps 中这些功能的相对支持。 此列表不包含其他 Azure Maps 功能，如辅助功能、地理围栏 Api、等时线、空间操作、直接地图磁贴访问、批处理服务和数据覆盖率比较（即图像覆盖面）。

| Google Maps 功能         | Azure Maps 支持                     |
|-----------------------------|:--------------------------------------:|
| Web SDK                     | ✓                                      |
| Android SDK                 | ✓                                      |
| iOS SDK                     | 已计划                                |
| REST 服务 Api           | ✓                                      |
| 方向（路由）        | ✓                                      |
| 距离矩阵             | ✓                                      |
| Elevation                   | 已计划                                |
| 地理编码（正向/反向） | ✓                                      |
| 地理位置                 | N/A                                    |
| 位置搜索               | ✓                                      |
| 位置详细信息              | 暂缺–网站 & 可用的电话号码 |
| 将照片               | N/A                                    |
| 放置自动完成          | ✓                                      |
| 静态映射                 | ✓                                      |
| 静态街道视图          | N/A                                    |
| 时区                   | ✓                                      |
| 地图嵌入的 API           | N/A                                    |
| 映射 Url                    | N/A                                    |

Google Maps 提供基本的基于密钥的身份验证。 Azure Maps 提供基本的基于密钥的身份验证以及高度安全 Azure Active Directory 身份验证。

## <a name="licensing-considerations"></a>许可注意事项

在从 Google Maps 迁移到 Azure Maps 时，应考虑以下几点。

- 根据所加载的地图磁贴的数量，Azure Maps 使用交互式地图的费用，而 Google Maps 则会为地图控件的加载收取费用。 在交互式 Azure Maps Sdk 中，地图磁贴会自动缓存以降低开发人员的成本。 为每个加载的15个地图磁贴生成一个 Azure Maps 的事务。 交互式 Azure Maps Sdk 使用512像素的磁贴，平均每个页面视图生成一个或少个事务。
- 将 Google Maps web 服务中的静态地图图像替换为 Azure Maps Web SDK 通常会更加经济高效，因为这样做会使用地图图块，除非用户平移并缩放地图，否则他们通常只为每个地图负载生成一小部分事务。 Azure Maps web SDK 提供了用于禁用平移和缩放的选项。 此外，Azure Maps web SDK 提供了比静态映射 web 服务更多的可视化选项。
- Azure Maps 允许其平台中的数据存储在 Azure 中。 它还可以在其他地方缓存多达六个月，按[使用条款使用](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)。

下面是 Azure Maps 的一些相关资源：

- [Azure Maps 定价页](https://azure.microsoft.com/pricing/details/azure-maps/)
- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/?service=azure-maps)
- [Azure Maps](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=46)使用条款（包括在 Microsoft Online Services 条款中）
- [在 Azure Maps 中选择适当的定价层](https://docs.microsoft.com/azure/azure-maps/choose-pricing-tier)

## <a name="suggested-migration-plan"></a>建议的迁移计划

下面是高级迁移计划。

1. 对你的应用程序所使用的 Google Maps Sdk 和服务进行清点，并验证 Azure Maps 提供可迁移到的替代 Sdk 和服务。
2. 在[https://azure.com](https://azure.com)创建 Azure 订阅（如果还没有）。
3. 创建 Azure Maps 帐户（[文档](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys)）和身份验证密钥或 Azure Active Directory （[文档](https://docs.microsoft.com/azure/azure-maps/how-to-manage-authentication)）。
4. 迁移你的应用程序代码。
5. 测试已迁移的应用程序。
6. 将已迁移的应用程序部署到生产环境。

## <a name="azure-maps-technical-resources"></a>Azure Maps 技术资源

下面是 Azure Maps 的有用技术资源的列表。

- 概述： [https://azure.com/maps](https://azure.com/maps)
- 文档： [https://aka.ms/AzureMapsDocs](https://aka.ms/AzureMapsDocs)
- Web SDK 代码示例： [https://aka.ms/AzureMapsSamples](https://aka.ms/AzureMapsSamples)
- 开发人员论坛： [https://aka.ms/AzureMapsForums](https://aka.ms/AzureMapsForums)
- 视频： [https://aka.ms/AzureMapsVideos](https://aka.ms/AzureMapsVideos)
- 博客： [https://aka.ms/AzureMapsBlog](https://aka.ms/AzureMapsBlog)
- Azure Maps 反馈（UserVoice）： [https://aka.ms/AzureMapsFeedback](https://aka.ms/AzureMapsFeedback)

## <a name="migration-support"></a>迁移支持

开发人员可以通过[论坛](https://aka.ms/AzureMapsForums)或众多 Azure 支持选项之一寻求迁移支持： [https://azure.microsoft.com/support/options](https://azure.microsoft.com/support/options)

## <a name="next-steps"></a>后续步骤

请参阅以下文章，了解如何迁移 Google Maps 应用程序：

> [!div class="nextstepaction"]
> [迁移 web 应用](migrate-from-google-maps-web-app.md)

> [!div class="nextstepaction"]
> [迁移 Android 应用](migrate-from-google-maps-android-app.md)

> [!div class="nextstepaction"]
> [迁移 web 服务](migrate-from-google-maps-web-services.md)
