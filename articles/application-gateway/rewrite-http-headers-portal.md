---
title: 重写 HTTP 请求和响应标头与 Azure 应用程序网关-Azure 门户 |Microsoft Docs
description: 了解如何使用 Azure 门户配置 Azure 应用程序网关来重写中的请求和响应通过网关将传递的 HTTP 标头
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/10/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6afc07f98905469b06622e7829ec4a215b94845e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59994599"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>重写 HTTP 请求和响应标头与 Azure 应用程序网关-Azure 门户

本文介绍如何使用 Azure 门户来配置[应用程序网关 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)重写中的请求和响应的 HTTP 标头。

> [!IMPORTANT]
> 自动缩放和区域冗余应用程序网关 SKU 目前处于公共预览状态。 此预览版在提供时没有附带服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

需要 v1 SKU 不支持 SKU，因为标头重写功能的应用程序网关 v2。 如果您没有 v2 SKU，创建[应用程序网关 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)在开始之前。

## <a name="what-is-required-to-rewrite-a-header"></a>重写一个标头的要求

若要配置 HTTP 标头重写，将需要：

1. 创建重写 http 标头所需的新对象：

   - **重写操作**： 用于指定请求和你想要重写的请求标头字段和原始标头需要被重写为的新值。 您可以选择使用重写操作将一个或多个重写条件相关联。

   - **重写条件**:它是一项可选配置。 如果添加重写条件，则会评估的 HTTP (S) 请求和响应的内容。 是否使用重写的条件匹配的 HTTP (S) 请求或响应，将基于执行与重写条件关联的重写操作的决策。 

     如果与之关联多个条件将执行操作，则该操作，仅当满足所有条件时，即，将执行逻辑与运算。

   - **重写规则**： 重写规则包含多个重写操作-重写条件组合。

   - **规则序列**： 有助于确定在其中的不同重写规则的顺序执行。 当有多个重写规则集中重写时，这非常有用。 获取首先执行具有较低的规则序列值的重写规则。 如果你提供两个重写规则的相同规则序列的执行顺序将非确定性。

   - **重写集**： 包含多个重写规则将关联到的请求路由规则。

2. 你将需要附加设置与路由规则重写。 这是因为重写配置已附加到源侦听器通过路由规则。 使用基本路由规则时，标头重写配置与源侦听器相关联，并且是全局标头重写。 使用基于路径的路由规则时，将在 URL 路径映射中定义标头重写配置。 因此，它仅适用于站点的特定路径区域。

可以创建多个 http 标头重写集，并且每个重写集可应用于多个侦听器。 不过，您可以应用仅其中一个重写设置为特定的侦听程序。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="configure-header-rewrite"></a>配置标头重写

在此示例中，我们将通过重写由后端应用程序发送的 http 响应中的 location 标头修改重定向 URL。 

1. 选择**的所有资源**，然后选择应用程序网关。

2. 选择**重写**从左侧菜单中。

3. 单击 **+ 重写集**。 

   ![添加重写集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 提供到重写集的名称，并将其关联的路由规则：

   - 输入重写中设置的名称**名称**文本框中。
   - 选择一个或多个规则中列出**关联的路由规则**列表。 您只能选择尚未与其他重写集相关联的这些规则。 已与其他重写集相关联的规则将灰显。
   - 单击“下一步”。
   
     ![添加名称和关联](media/rewrite-http-headers-portal/name-and-association.png)

5. 创建一个重写规则：

   - 单击 **+ 添加重写规则**。![添加重写规则](media/rewrite-http-headers-portal/add-rewrite-rule.png)
   - 提供重写规则名称文本框中的重写规则的名称，并提供规则序列。![添加规则名称](media/rewrite-http-headers-portal/rule-name.png)

6. 此示例中，在仅包含对"azurewebsites.net"的引用时我们将重写的 location 标头。 若要执行此操作，添加要评估是否在响应中的 location 标头包含 azurewebsites.net 的条件：

   - 单击 **+ 添加条件**，并单击的部分**如果**说明进行操作以展开它。![添加规则名称](media/rewrite-http-headers-portal/add-condition.png)

   - 选择**HTTP 标头**从**类型的变量以检查**下拉列表。 

   - 选择**标头类型**作为**响应**。

   - 由于在此示例中，我们正在评估的 location 标头，它恰巧是通用标头，选择**常见标头**单选按钮**标头名称**。

   - 选择**位置**从**常见标头**下拉列表。

   - 选择**否**作为**区分大小写**设置。

   - 选择**等号 （=）** 从**运算符**下拉列表。

   - 输入正则表达式模式。 在此示例中，我们将使用的模式`(https?):\/\/.*azurewebsites\.net(.*)$`。

   - 单击“确定”。

     ![修改位置标头](media/rewrite-http-headers-portal/condition.png)

7. 添加要重写的 location 标头的操作：

   - 选择**设置**作为**操作类型**。

   - 选择**响应**作为**标头类型**。

   - 选择**常见标头**作为**标头名称**。

   - 选择**位置**从**常见标头**下拉列表。

   - 输入的标头值。 在此示例中，我们将使用`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值。 这将替换*azurewebsites.net*与*contoso.com* location 标头中。

   - 单击“确定”。

     ![修改位置标头](media/rewrite-http-headers-portal/action.png)

8. 单击**创建**若要创建重写设置。

   ![修改位置标头](media/rewrite-http-headers-portal/create.png)

9. 你将导航至重写集视图。 验证上面创建的重写集存在中重写集的列表。

   ![修改位置标头](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>后续步骤

若要了解有关所需完成一些常见的配置的详细使用情况下，请参阅[常见标头重写方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。

   
