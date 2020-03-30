---
title: 互联网分析器常见问题 |微软文档
description: Azure Internet 分析器的常见问题解答。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184258"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure 互联网分析器常见问题解答（预览版）

这是 Azure Internet 分析器的常见问题解答 - 如果您有其他问题，请转到[反馈论坛](https://aka.ms/internetAnalyzerFeedbackForum)并发布您的问题。 当问题经常被问到时，我们会将其添加到本文中，以便快速轻松地找到它。

## <a name="how-do-i-participate-in-the-preview"></a>如何参与预览？

预览版可用于选择客户。 如果您有兴趣加入预览版，请执行以下操作：

1. 登录到 Azure[门户](https://ms.portal.azure.com)。
2. 导航到 **"订阅"** 页。
3. 单击计划使用 Internet 分析器的 Azure 订阅。
4. 转到**订阅的资源提供程序**设置。
5. 搜索**Microsoft.Network**并单击 **"注册**（或**重新注册**）"按钮。
![访问请求](./media/ia-faq/request-preview-access.png)

6. 通过向我们提供您的电子邮件地址和用于发出访问请求的 Azure 订阅 ID[来请求批准](https://aka.ms/internetAnalyzerContact)。
7. 请求获得批准后，您将收到一封电子邮件确认，并能够从新允许的 Azure 订阅创建/更新/修改 Internet 分析器资源。

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>我需要嵌入客户端来运行测试吗？

是的，必须在应用程序中安装 Internet 分析器客户端来收集特定于用户的指标。 [了解如何安装客户端。](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>我参加预览会需要收费吗？
否，Azure Internet 分析器可免费在预览版中使用。 预览期间没有服务级别协议。

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>互联网分析器设计用于解决哪些方案？

互联网分析器旨在根据您的用户群为您提供网络性能见解。 为了帮助用户做出最佳性能决策，Internet Analyzer 使用不同的用户群比较了两个 Internet 终结点的性能。 虽然 Internet 分析器可以回答许多问题，但最常见的问题有：

* 迁移到云对性能有什么影响？ 
    * *建议测试：自定义（当前本地基础结构）与 Azure（任何预配置终结点）*
* 将数据放在边缘与数据中心的价值是什么？ 
    *  *建议测试：Azure 与 Azure 前门，Azure 与来自 Microsoft 的 Azure CDN*
* Azure Front Door 的性能优势是什么？
    *  *建议测试：自定义/Azure/CDN 与 Azure 前门*
* Microsoft 推出的 Azure CDN 的性能优势是什么？ 
    *  *建议测试：自定义/Azure/AFD 与来自 Microsoft 的 Azure CDN*
* Microsoft 推出的 Azure CDN 如何堆叠？ 
    *  *建议测试：自定义（其他 CDN 终结点）与来自 Microsoft 的 Azure CDN*
* 对于每个地区的最终用户群来说，最好的云是什么？ 
    *  *建议测试：自定义（其他云服务）与 Azure（任何预配置终结点）*

## <a name="which-tests-can-i-run-in-preview"></a>我可以在预览中运行哪些测试？

在 Internet 分析器中创建的每个测试由两个终结点组成：端点 A 和终结点 B。以下任何组合都可以作为测试运行：  
* 两个预配置的终结点，
* 一个自定义和一个预配置终结点，或
* 两个[自定义终结点](internet-analyzer-custom-endpoint.md)（一个自定义终结点必须驻留在 Azure 中）。

预览期间提供以下预配置终结点：
* **Azure 区域**
    * 巴西南部
    * 印度中部
    * 美国中部
    * 东亚
    * 美国东部
    * 日本西部
    * 北欧
    * 南非北部
    * 东南亚
    * 阿拉伯联合酋长国北部
    * 英国西部  
    * 西欧
    * 美国西部
    * 美国西部 2
* **多个 Azure 区域组合**
    * 美国东部、巴西南部
    * 美国东部、东亚
    * 欧洲西部、巴西南部
    * 欧洲西部、东南亚
    * 欧洲西部、阿联酋北部
    * 美国西部、美国东部
    * 美国西部、欧洲西部
    * 美国西部、阿拉伯联合酋长国北部
    * 欧洲西部、阿联酋北部、东南亚
    * 美国西部、欧洲西部、东亚
    * 美国西部、欧洲北部、东南亚、阿联酋北部、南非北部 
* **Azure + Azure Front Door** - 部署在上面列出的任何单个或多个 Azure 区域组合上
* **Azure + Microsoft 推出的 Azure CDN** - 部署在上面列出的任何单一 Azure 区域组合上
* **Azure + Azure 流量管理器** - 部署在上面列出的任何多个 Azure 区域组合上

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet 分析器与 Azure 提供的其他监视服务有什么不同？

互联网分析器可帮助您了解最终用户的性能，并帮助您做出决策以提高其性能。 虽然其他 Azure 监视工具提供对 Azure 服务的见解，但 Internet 分析器侧重于测量用户的端到端 Internet 性能。

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>互联网分析仪如何处理测量数据？

Azure 有[严格的安全过程，并符合各种法规标准](https://azure.microsoft.com/support/trust-center/)。 只有你和指定的团队可以访问数据。 Microsoft 工作人员只会在知情的情况下和受限的具体情况下，才对数据拥有受限的访问权限。 将对传输中的静态数据加密。

## <a name="next-steps"></a>后续步骤

要了解更多信息，请参阅我们的[互联网分析器概述](internet-analyzer-overview.md)。
