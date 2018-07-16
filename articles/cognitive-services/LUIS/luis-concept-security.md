---
title: 了解 LUIS 应用程序访问权限 - Azure | Microsoft Docs
description: 了解如何访问 LUIS 创作。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 44380e12e6d095e8d40675af0b6b2fddc5e4c4e9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264261"
---
# <a name="authoring-and-endpoint-user-access"></a>创作和终结点用户访问权限
所有者和协作者具有创作访问权限。 对于专用应用，所有者和协作者具有终结点访问权限。 对于公共应用，任何具有 LUIS 帐户和公共应用 ID 的人员均具有终结点访问权限。 

## <a name="access-to-authoring"></a>访问创作
从 [LUIS][LUIS] 网站或[创作 API](https://aka.ms/luis-authoring-apis) 访问应用的权限由应用的所有者控制。 

所有者和所有协作者具有创作应用的权限。 

|创作访问权限包括|说明|
|--|--|
|添加或删除终结点密钥||
|导出版本||
|导出终结点日志||
|导入版本||
|公开应用|如果应用公开，任何拥有创作或终结点密钥的人员都可以查询应用。|
|修改模型|
|发布|
|查看用于[主动学习](label-suggested-utterances.md)的终结点陈述|
|训练|

## <a name="access-to-endpoint"></a>访问终结点
用以查询 LUIS 的终结点访问权限由“设置”页上应用的“公共”设置控制。 检查专有应用的终结点查询是否有具有配额有余的授权密钥。 公共应用的终结点查询也必须提供终结点密钥（无论何人发出此查询），并且也会检查该密钥是否具有剩余配额。 

终结点密钥可以在 GET 请求的 querystring 中传递，也可以在 POST 请求的标头中传递。

![将应用设置为公共](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>专有应用终结点安全
专有应用的终结点仅适用于下述情况：

|密钥和用户|说明|
|--|--|--|
|所有者的创作密钥| 最多 1000 个终结点命中数|
|协作者的创作密钥| 最多 1000 个终结点命中数|
|从[发布](publishapp.md)页添加的终结点密钥|所有者和协作者可以添加终结点密钥|

其他创作或终结点密钥没有访问权限。

### <a name="public-app-endpoint-access"></a>公共应用终结点访问权限
在应用的“设置”页上将应用配置为“公共”。 应用配置为公共后，任何有效的 LUIS 创作密钥或 LUIS 终结点密钥都可以查询应用，只要该密钥未使用整个终结点配额。

如果用户不是所有者或协作者，则只有在给定应用 ID 时才能访问公共应用。 LUIS 不提供公共市场或其他搜索公共应用的方式。  

## <a name="microsoft-user-accounts"></a>Microsoft 用户帐户
创建者和协作者可以在“发布”页上向 LUIS 添加密钥。 用以在 Azure 门户中创建 LUIS 密钥的 Microsoft 用户帐户需为应用所有者或应用协作者。 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>保护终结点安全 
可以通过在服务器到服务器环境中调用终结点密钥来控制可以查看 LUIS 终结点的人员。 如果在机器人上使用 LUIS，则机器人和 LUIS 之间的连接已经安全。 如果直接调用 LUIS 终结点，则应创建具有受控访问权限（如 [AAD](https://azure.microsoft.com/services/active-directory/)）的服务器端 API（如 Azure [函数](https://azure.microsoft.com/services/functions/)）。 如果调用服务器端 API 并且身份验证和授权得到验证，则将调用传递到 LUIS。 尽管此策略不会防止中间人攻击，但它针对用户模糊化处理终结点，允许跟踪访问，并允许添加终结点响应日志记录（如 [Application Insights](https://azure.microsoft.com/services/application-insights/)）。  

## <a name="security-compliance"></a>安全性和符合性
LUIS 成功通过了 ISO 27001:2013 和 ISO 27018:2014 审核，审核报表中不含任何不符合项（结果）。 此外，LUIS 还获得了 CSA STAR 认证，并很可能在成熟度和功能评估方面获得金奖。 Azure 是唯一获得这一认证的主要公有云服务提供商。 有关详细信息，请参阅 Azure 主要[符合性概述](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)文档的更新范围声明中关于 LUIS 的部分（[信任中心](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001) ISO 页上引用了该文档）。  

## <a name="next-steps"></a>后续步骤

请参阅[最佳做法](luis-concept-best-practices.md)，了解如何使用意向和实体进行最佳预测。

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website