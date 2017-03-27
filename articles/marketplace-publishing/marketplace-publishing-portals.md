---
title: "为应用商店创建产品/服务所需的各种门户概述 | Microsoft Docs"
description: "为应用商店创建供应项所需的各种门户的概述"
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 89ce82b3-c28a-4b0d-b37a-db3112160a4e
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 8087f27a692e1e2d0f2d9434b065ddb902de0d57


---
# <a name="portals-you-will-need"></a>所需的门户
在开始发布供应项的过程之前，让我们先介绍你所需要的各种门户。 下面是有关门户的简要概述，包括：开发人员中心、Azure 发布门户和 Azure 门户（按照你将与其进行交互的顺序显示）。                                                                            

## <a name="developer-center"></a>开发人员中心
[http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure)

### <a name="description"></a>说明
创建 Microsoft 开发人员中心帐户是一次性的任务。 在创建开发人员帐户前，请确保公司没有开发人员中心帐户。 在该过程中，我们会收集银号帐户信息、税务信息和公司地址信息。

> [!NOTE]
> 如果仅发布免费供应项（或提供自带的许可），则不需要税务和银行信息。
> 
> 

### <a name="identityaccount-used"></a>所用的身份/帐户
理想情况下，这是通讯组列表或安全组（例如，azurepublishing@*partnercompany*.com）。 通讯组列表或安全组**必须**已注册为 Microsoft 帐户。

> [!TIP]
> 建议使用通讯组列表或安全组，因为它会删除任何个人帐户的依赖性，尽管也可以使用个人帐户。
> 
> 

## <a name="publishing-portal"></a>发布门户
[https://publish.windowsazure.com](https://publish.windowsazure.com)

### <a name="description"></a>说明
此门户可让你处理供应项并进行发布（不论是市场营销、定价、发布、证书（如适用）等等）。

### <a name="identityaccount-used"></a>所用的身份/帐户
首次登录到发布门户时，必须使用上述的通讯组列表或安全组。 稍后，即可将其他用户添加为共同管理员。 这是映射到开发人员中心注册数据的方式。

## <a name="azure-portal"></a>Azure 门户
[https://portal.azure.com](https://portal.azure.com)

### <a name="description"></a>说明
在这个梦中，你可以在 Azure 应用商店中查看暂存和已发布的供应项（适用于VM、 解决方案模板和基于 Azure Resource Manager 的开发人员服务）。

### <a name="identityaccount-used"></a>所用的身份/帐户
从发布的门户中暂存某供应项时，需要将订阅 ID 列入允许列表。 登录到此门户来测试暂存的供应项时，必须使用同一个订阅（具有与之关联的用户名和密码）。

## <a name="see-also"></a>另请参阅
* [入门教程：如何将供应项发布到 Azure 应用商店](marketplace-publishing-getting-started.md)




<!--HONumber=Nov16_HO3-->


