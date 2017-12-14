---
title: "Azure 标识开发人员的支持和帮助选项 | Microsoft Docs"
description: "了解在创建与 Microsoft Azure 标识（Azure Active Directory 和 MSA）集成的应用程序时，如何获取所遇到的开发相关问题的帮助和支持"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/27/2017
ms.author: andret
ms.custom: aaddev
ms.openlocfilehash: 06da07aa699d19602449dc365abb971867214a31
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="support-and-help-options-for-developers"></a>开发人员的支持和帮助选项 

不管是刚刚开始与 Azure Active Directory、Microsoft 标识或 Microsoft 图形 API 集成，还是正在应用程序中实现新功能，有时，都需要从社区获得帮助，或了解开发人员可用的支持选项。 本文帮助开发人员了解这些选项，摘要如下：

> [!div class="checklist"]
> * 通过搜索确定自己的问题是否尚未由社区做出解答，或者尝试实现的功能是否已存在现有的文档
> * 在某些情况下，我们只想使用自己的支持工具来帮助调试特定的问题
> * 如果在所需的资源中找不到解答，可在 *Stack Overflow* 上提问
> * 如果发现某个身份验证库出现问题，请提出 *GitHub* 问题
> * 最后，如果需要与某人交流，可以提出支持请求


## <a name="search"></a>搜索

如果遇到开发相关的问题，也许能够在我们的文档、[github 示例](https://github.com/azure-samples)或 [Stack Overflow](https://www.stackoverflow.com) 问题解答中找到所需的答案。

### <a name="scoped-search"></a>范围搜索
为了更快地找到结果，请在[偏好的搜索引擎](https://bing.com)中使用以下语句，将搜索范围限定为 Stack Overflow、我们的文档和我们的代码示例：
```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/en-us/graph)
```
其中，*{Your Search Terms}* 是搜索关键字。
<br/>

## <a name="use-our-development-support-tools"></a>使用开发支持工具

|工具  |说明  |
|---------|---------|
|[jwt.ms](https://jwt.ms)| 粘贴 ID 或访问令牌来解码声明名称和值 |
|[错误代码分析器](https://apps.dev.microsoft.com/portal/tools/errors)| 粘贴在登录或许可页面期间收到的错误代码，以查看可能的原因和补救措施 |
|[Microsoft Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)| 用于针对 Microsoft 图形 API 发出请求和查看响应的工具|

<br/>

[![Stack Overflow](media/active-directory-develop-help-support/stackoverflow-logo.png)](https://www.stackoverflow.com)
## <a name="post-a-question-to-stack-overflow"></a>在 Stack Overflow 上发布问题

Stack Overflow 是解决开发相关问题的首选渠道 - 社区成员和 Microsoft 团队成员都直接涉身其中帮助解决问题。

如果通过搜索找不到问题的解答，可将新问题提交到 Stack Overflow：提问时请使用以下标记之一，以帮助社区识别问题并及时解答问题：

|组件/方面  |标记  |
|---------|---------|
|ADAL 库 |[[adal]](http://stackoverflow.com/questions/tagged/adal)|
|MSAL 库     |[[msal]](http://stackoverflow.com/questions/tagged/msal)|
|OWIN 中间件  |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)|
|[Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  |[[azure-ad-b2b]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  |[[azure-ad-b2c]](http://stackoverflow.com/questions/tagged/azure-ad-b2b)|
|[Microsoft 图形 API](https://developer.microsoft.com/graph/) |[[microsoft-graph]](http://stackoverflow.com/questions/tagged/microsoft-graph)
|与身份验证或授权主题相关的其他任何方面 |[[azure-active-directory]](http://stackoverflow.com/questions/tagged/azure-active-directory)
<br/>
> [!TIP]
> Stack Overflow 中的以下文章包含有关如何提问以及添加源代码的提示 - 遵循这些准则有助于提高社区成员快速评估和回复问题的可能性：  
> - [如何正确提问](https://stackoverflow.com/help/how-to-ask)
> - [如何创建最小、完整且可验证的示例](https://stackoverflow.com/help/mcve)

<br/>


[![Stack Overflow](media/active-directory-develop-help-support/github-logo.png)](https://www.github.com)
## <a name="create-a-github-issue"></a>创建 GitHub 问题

 如果发现了与库相关的 bug 或问题，请提出有关 GitHub 存储库的问题。 由于我们的库是开源的，因此，你也可以任意提交拉取请求。 以下文章包含库及其 GitHub 存储库的列表：

- [ADAL、MSAL 和 Owin 中间件](active-directory-authentication-libraries.md)库和 GitHub 存储库

<br/>

## <a name="open-a-support-request"></a>提出支持请求

如果需要与某人交流，可以提出支持请求。 Azure 客户可以使用多个支持选项。 若要比较计划，请参阅[此页](https://azure.microsoft.com/support/plans/)。 开发人员支持也适用于 Azure 客户。 有关如何购买开发人员支持计划的信息，请参阅[此页](https://azure.microsoft.com/support/plans/developer/)。

- 如果已有 Azure 支持计划，请[在此处提出支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

- 如果你不是 Azure 客户，也可以通过[我们的商业支持](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial)向 Microsoft 提出支持请求。

还可以尝试通过[我们的虚拟代理](https://support.microsoft.com/contactus/?ws=support)获取支持或提问。

### <a name="free-chat-support-for-a-limited-time"></a>获取限时免费聊天支持

Microsoft 合作伙伴还可以使用限时免费聊天支持。 如果你的公司不是 Microsoft 合作伙伴，可在[此处](https://partners.microsoft.com/PartnerProgram/simplifiedenrollment.aspx)免费将其登记并获取其他权益。

登记公司后，可在[此处](https://aka.ms/devchat)发起聊天请求。