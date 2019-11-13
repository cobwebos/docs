---
title: 重写门户中的 HTTP 请求和响应标头-Azure 应用程序网关
description: 了解如何使用 Azure 门户配置应用程序网关，重写通过网关传递的请求和响应中的 HTTP 标头
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: b90736b3ed1c1f69488fde4a386cf215d751c362
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012863"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>重写 Azure 应用程序网关中的 HTTP 请求和响应标头 - Azure 门户

本文介绍如何使用 Azure 门户配置[应用程序网关 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) 实例，重写请求和响应中的 HTTP 标头。

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要有应用程序网关 v2 SKU 实例。 v1 SKU 不支持重写标头。 如果没有 v2 SKU，请在开始之前创建[应用程序网关 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) 实例。

## <a name="create-required-objects"></a>创建所需对象

若要配置 HTTP 标头重写，需要完成以下步骤。

1. 创建 HTTP 标头重写所需的对象：

   - **重写操作**：用于指定要重写的请求和请求标头字段和标头的新值。 可将一个或多个重写条件关联到一个重写操作。

   - **重写条件**：可选配置。 重写条件评估 HTTP(S) 请求和响应的内容。 如果 HTTP(S) 请求或响应与重写条件匹配，则会发生重写操作。

     如果将多个条件关联到一个操作，仅当满足所有条件时，才会发生该操作。 换言之，操作属于逻辑 AND 运算。

   - **重写规则**：包含多个重写操作/重写条件组合。

   - **规则序列**：帮助确定重写规则的执行顺序。 在一个重写集中使用多个重写规则时，此配置非常有用。 规则顺序值较小的重写规则最先运行。 如果为两个重写规则分配了相同的规则顺序值，则执行顺序是不确定的。

   - **重写集**：包含将与请求路由规则关联的多个重写规则。

2. 将重写集附加到路由规则。 重写配置将通过路由规则附加到源侦听器。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 在这种情况下，该规则只会应用到站点的特定路径区域。

可以创建多个 HTTP 标头重写集，并将每个重写集应用到多个侦听器。 但是，对于一个特定的侦听器，只能应用一个重写集。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/) 。

## <a name="configure-header-rewrite"></a>配置标头重写

在此示例中，我们将修改重定向 URL，方法是：重写由后端应用程序发送的 HTTP 响应中的 location 标头。

1. 选择“所有资源”，然后选择你的应用程序网关。

2. 在左窗格中，选择“重写”。

3. 选择“重写集”：

   ![添加重写集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 为重写集提供一个名称，并将其与路由规则相关联：

   - 在“名称”框中输入重写集的名称。
   - 选择在“关联的路由规则”列表中列出的一个或多个规则。 只能选择尚未与其他重写集关联的规则。 已经与其他重写集关联的规则处于灰显状态。
   - 选择“**下一步**”。
   
     ![添加名称和关联](media/rewrite-http-headers-portal/name-and-association.png)

5. 创建重写规则：

   - 选择“添加重写规则”。

     ![添加重写规则](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 在“重写规则名称”框中输入重写规则的名称。 在“规则顺序”框中输入一个数字。

     ![添加重写规则名称](media/rewrite-http-headers-portal/rule-name.png)

6. 在此示例中，仅当 location 标头包含对 azurewebsites.net 的引用时，才会重写该标头。 为此，请添加一个条件，用于评估响应中的 location 标头是否包含 azurewebsites.net：

   - 选择“添加条件”，然后选择包含 **If** 指令的框将其展开。

     ![添加条件](media/rewrite-http-headers-portal/add-condition.png)

   - 在“要检查的变量的类型”列表中，选择“HTTP 标头”。

   - 在“标头类型”列表中选择“响应”。

   - 由于在此示例中我们要评估 location 标头，该标头是一个常用标头，因此请在“标头名称”下选择“常用标头”。

   - 在“常用标头”列表中选择“Location”。

   - 在“区分大小写”下选择“否”。

   - 在“运算符”列表中选择“等于(=)”。

   - 输入正则表达式模式。 在此示例中，我们将使用 `(https?):\/\/.*azurewebsites\.net(.*)$` 模式。

   - 选择“确定”。

     ![配置 If 条件](media/rewrite-http-headers-portal/condition.png)

7. 添加操作来重写 location 标头：

   - 在“操作类型”列表中，选择“设置”。

   - 在“标头类型”列表中选择“响应”。

   - 在“标头名称”下选择“常用标头”。

   - 在“常用标头”列表中选择“Location”。

   - 输入标头值。 在此示例中，我们将使用 `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` 作为标头值。 此值将用 location 标头中的*contoso.com*替换*azurewebsites.net* 。

   - 选择“确定”。

     ![添加操作](media/rewrite-http-headers-portal/action.png)

8. 选择“创建”以创建重写集：

   ![选择“创建”](media/rewrite-http-headers-portal/create.png)

9. 此时会打开“重写集”视图。 验证已创建的重写集是否位于重写集列表中：

   ![“重写集”视图](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>后续步骤

若要详细了解如何设置某些常见用例，请参阅[常见标头重写方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。