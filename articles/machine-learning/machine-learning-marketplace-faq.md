---
title: "（已弃用）常见问题解答 - 在 Azure 应用商店中发布和使用机器学习应用 | Microsoft Docs"
description: "（已弃用）在 Azure 应用商店中发布机器学习应用的相关常见问题解答"
services: machine-learning
documentationcenter: 
author: bharaths
manager: jhubbard
editor: cgronlun
ms.assetid: 26b3a1e7-8b9a-4004-98bc-17456d4c25e8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: bharaths
ROBOTS: NOINDEX
redirect_url: https://gallery.cortanaintelligence.com/
redirect_document_id: TRUE
ms.translationtype: Human Translation
ms.sourcegitcommit: 85a58e09e05fdb50984055ab25c4c2fe520dab4d
ms.openlocfilehash: 9e2d4a2d46bd461a71217cd2b2d75f5a50e8b617
ms.contentlocale: zh-cn
ms.lasthandoff: 01/10/2017


---
# <a name="deprecated-publishing-and-using-machine-learning-apps-in-the-azure-marketplace-faq"></a>（已弃用）在 Azure 机器应用商店中发布和使用机器学习应用：常见问题解答

> [!NOTE]
> 将停用 DataMarket 和数据服务，并从 2017/3/31 起停用和取消现有订阅。 因此将弃用这篇文章。 
> 
> 转而，可选择将机器学习试验发布到 [Cortana Intelligence 库](https://gallery.cortanaintelligence.com/)，便于数据科学社区使用。 有关详细信息，请参阅[在 Cortana Intelligence 库中共享和发现资源](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-gallery-how-to-use-contribute-publish)。


## <a name="questions-about-consuming-from-marketplace"></a>有关在应用商店中使用服务的问题
**1.键入 Web 服务的输入内容后，为什么收到以下错误消息：**

**该请求导致后端超时或后端错误。团队正在调查此问题。对此产生的不便，我们深表歉意。(500)**

输入参数可能不符合特定 Web 服务所需的格式。 请参阅相应的文档链接，查找输入参数的正确格式和此 Web 服务的限制。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**2.如果我复制在“浏览此数据集”页上显示的用于该 Web 服务的 API 链接，然后将其粘贴到另一个浏览器窗口，我应该使用哪个凭据访问结果，并且我如何看到它们？**

应使用应用商店帐户作为用户名，主帐户密钥作为密码。 主帐户密钥位于“浏览此数据集”页上的 Web 服务描述下（单击“显示”按钮）。 结果可能显示在浏览器中，也可能提供下载，具体取决于你所使用的浏览器。

**3.在“浏览此数据集”页上键入 Web 服务的输入内容后，为什么收到以下错误消息：** 

**处理你的请求时出现意外错误。请重试。**

在应用商店“浏览此数据集”页上使用 Web 服务时，Web 服务的一个或多个输入参数可能超出长度限制。 可以使用 HTTP POST 方法以较长输入长度的方式调用服务。 若要获取示例，请参阅[对机器学习使用 R 以及发布到应用商店的解决方案示例](machine-learning-r-csharp-web-service-examples.md)。

**4.为什么在 Azure 经典门户应用商店内的“API EXPLORER”选项卡中看不到任何内容？** 

这是 Azure 经典门户应用商店的已知问题。 团队正在努力解决此问题。 

## <a name="questions-about-publishing-from-azure-machine-learning-on-marketplace"></a>有关在应用商店中从 Azure 机器学习发布的问题
**1.为什么徽标或图像的事务未针对 Web 服务刷新？** 

徽标和图像在发布门户中缓存，新徽标或图像可能最多在 10 天内在门户上更新。

**2.为什么在应用商店中，我的 Web 服务的“详细信息”选项卡显示错误消息？**

连接到 Azure 机器学习获取服务详细信息时，应用商店存在一个已知问题。 团队正在努力解决此问题。

**3.为什么 Azure 机器学习 Web 服务中的 R 示例代码无法用于在应用商店中使用 Web 服务？**

与通过应用商店连接到 Azure 机器学习 Web 服务时相比，直接连接到这些服务时的身份验证系统有所不同。 应用商店中的服务是 OData 服务，可以使用 GET 或 POST 方法调用。 

**4.为什么我的 Web 服务产品的支持链接没有正确地针对某些产品/服务进行更新？**

全局支持链接以发布者而不是产品/服务为依据。 

**5.如何在应用商店中使用批处理输入模式发布 Web 服务？**

应用商店 Web 服务当前不支持批处理输入模式。

**6.如果对于如何成为数据发布者有疑问，或者在发布过程中遇到问题，应联系谁才能获得帮助？**

请通过 <mailto:datamarketbd@microsoft.com> 联系 Azure 应用商店团队获取更多信息。


