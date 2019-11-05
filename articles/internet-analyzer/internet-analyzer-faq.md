---
title: Internet 分析器常见问题 |Microsoft Docs
description: Azure Internet 分析器的常见问题解答。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 61cb6003549530f381b9cbbed74c1cb62c91431c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512894"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet 分析器常见问题解答（预览版）

这是 Azure Internet 分析器的常见问题解答-如果你有其他问题，请访问[反馈论坛](https://aka.ms/internetAnalyzerFeedbackForum)并发布你的问题。 如果问题是常见问题，我们会将其添加到本文中，以便可以快速轻松地找到它。

## <a name="how-do-i-participate-in-the-preview"></a>如何参与预览版？

预览版可供选定客户使用。 如果你有兴趣加入预览，请执行以下操作：

1. 登录到 [Azure 门户](https://ms.portal.azure.com)。
2. 导航到 "**订阅**" 页。
3. 单击计划将 Internet Analyzer 用于的 Azure 订阅。
4. 请访问订阅的**资源提供程序**设置。
5. 搜索 " **Microsoft** "，并单击 "**注册**" （或 "**重新注册**"）按钮。
![访问请求](./media/ia-faq/request-preview-access.png)

6. 通过向我们提供你的电子邮件地址和用于发出访问请求的 Azure 订阅 ID 来[请求批准](https://aka.ms/internetAnalyzerContact)。
7. 批准请求后，你将收到一封电子邮件确认，将能够从新允许的 Azure 订阅中创建/更新/修改 Internet 分析器资源。

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>是否需要嵌入用于运行测试的客户端？

是的，必须在应用程序中安装 Internet 分析器客户端，以收集特定于用户的指标。 [了解如何安装客户端。](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>我加入此预览版后是否需要付费？
不可以，Azure Internet 分析器可在预览中免费使用。 预览期间不提供服务级别协议。

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Internet 分析器旨在解决哪些方案？

Internet 分析器旨在基于用户群体为你的网络性能见解。 为了帮助用户做出最佳的性能决策，Internet 分析器使用不同的用户群体来比较两个 Internet 终结点的性能。 虽然 Internet 分析器可以回答许多问题，但其中一些最常见的问题是：

* 迁移到云的性能影响是多少？ 
    * *建议的测试：自定义（当前的本地基础结构）与 Azure （任何预配置的终结点）*
* 将我的数据放置在边缘与数据中心中的值是什么？ 
    *  *建议的测试： Azure 与 Azure 前门、Azure 与 Microsoft Azure CDN*
* Azure 前门的性能优势是什么？
    *  *建议的测试：自定义/Azure/CDN 与 Azure 前门*
* Azure CDN Microsoft 的性能好处是什么？ 
    *  *建议的测试：自定义/Azure/AFD 与 Microsoft Azure CDN*
* 如何从 Microsoft stack 向上 Azure CDN？ 
    *  *建议的测试：自定义（其他 CDN 终结点）与 Microsoft Azure CDN*
* 对于每个区域中的最终用户群体，最佳做法是什么？ 
    *  *建议的测试：自定义（其他云服务）与 Azure （任何预配置的终结点）*

## <a name="which-tests-can-i-run-in-preview"></a>可以在预览中运行哪些测试？

在 Internet 分析器中创建的每个测试都由两个终结点（终结点 A 和终结点 B）组成。以下任何组合都可以作为测试运行：  
* 两个预配置的终结点，
* 一个自定义和一个预配置的终结点，或
* 两个自定义终结[点](internet-analyzer-custom-endpoint.md)（一个自定义终结点必须驻留在 Azure 中）。

预览期间可以使用以下预先配置的终结点：
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
    * 欧洲西部
    * 美国西部
    * 美国西部 2
* **多个 Azure 区域组合**
    * 美国东部、巴西南部
    * 美国东部，东亚
    * 西欧，巴西南部
    * 西欧，东南亚
    * 西欧，阿拉伯联合酋长国北部
    * 美国西部、美国东部
    * 美国西部，西欧
    * 美国西部，阿拉伯联合酋长国北部
    * 西欧，阿拉伯联合酋长国北部，东南亚
    * 美国西部、西欧、东亚
    * 美国西部、北欧、东南亚、阿拉伯联合酋长国北部、南非北部 
* **Azure + Azure 前端**-部署在上面列出的任何单个或多个 Azure 区域组合上
* **来自 Microsoft 的 azure + Azure CDN** -部署于上面列出的任何单个 Azure 区域组合上
* **Azure + Azure 流量管理器**-部署在上面列出的任意多个 Azure 区域组合上

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet 分析器与其他 Azure 提供的监视服务有何不同？

Internet 分析器可帮助您了解最终用户的性能，并帮助做出决定以提高其性能。 虽然其他 Azure 监视工具可深入了解 Azure 服务，但 Internet 分析器侧重于度量用户的端到端 Internet 性能。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请参阅我们的[Internet 分析器概述](internet-analyzer-overview.md)。
