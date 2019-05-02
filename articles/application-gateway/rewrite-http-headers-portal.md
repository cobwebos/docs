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
ms.openlocfilehash: e144214a58f9fe383cf4edd878554792d9d6a6f9
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947168"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-portal"></a>重写 HTTP 请求和响应标头与 Azure 应用程序网关-Azure 门户

本文介绍如何使用 Azure 门户来配置[应用程序网关 v2 SKU](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>)实例重写中请求和响应的 HTTP 标头。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="before-you-begin"></a>开始之前

需要有一个应用程序网关 v2 SKU 实例，以便完成这篇文章中的步骤。 在 v1 SKU 中不支持重写标头。 如果您没有 v2 SKU，创建[应用程序网关 v2 SKU](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps)实例在开始之前。

## <a name="create-required-objects"></a>创建所需的对象

若要配置 HTTP 标头重写时，需要完成以下步骤。

1. 创建所需的 HTTP 标头重写的对象：

   - **重写操作**:用于指定请求和你想要重写的请求标头字段和标头的新值。 您可以将一个相关联或详细信息重写与重写操作的条件。

   - **重写条件**:一项可选配置。 重写条件评估结果的 HTTP (S) 请求和响应的内容。 如果 HTTP (S) 请求或响应与重写条件匹配时，将发生的重写操作。

     如果将多个条件与操作相关联，仅当满足所有条件时，则会发生操作。 换而言之，该操作是逻辑 AND 运算。

   - **重写规则**:包含多个重写操作 / 重写条件组合。

   - **规则序列**:有助于确定重写规则的执行的顺序。 重写集中有多个重写规则时，此配置非常有用。 首先运行具有较低的规则顺序值的重写规则。 如果将相同的规则序列值分配到两个重写规则，请执行顺序是不确定的。

   - **重写集**:包含将请求路由规则与相关联的多个重写规则。

2. 附加重写设置为路由规则。 重写配置附加到源侦听器通过路由规则。 当您使用基本的路由规则时，标头重写配置与源侦听器相关联，并全局标头重写。 当使用基于路径的路由规则时，URL 路径映射定义标头重写配置。 在这种情况下，它仅适用于站点的特定路径区域。

您可以创建多个 HTTP 标头重写集和应用设置多个侦听器为每个重写。 但您可以将应用仅其中一个重写设置为特定的侦听程序。

## <a name="sign-in-to-azure"></a>登录 Azure

使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="configure-header-rewrite"></a>配置标头重写

在此示例中，我们将重写由后端应用程序发送的 HTTP 响应中的 location 标头来修改重定向 URL。

1. 选择**的所有资源**，然后选择应用程序网关。

2. 选择**重写**的左窗格中。

3. 选择**重写集**:

   ![添加重写集](media/rewrite-http-headers-portal/add-rewrite-set.png)

4. 提供重写集的名称，并将其关联的路由规则：

   - 输入重写中设置的名称**名称**框。
   - 选择一个或多个规则中列出**关联的路由规则**列表。 你可以选择仅一直与其他重写集相关联的规则。 已与其他重写集相关联的规则会变暗。
   - 选择“**下一步**”。
   
     ![添加名称和关联](media/rewrite-http-headers-portal/name-and-association.png)

5. 创建一个重写规则：

   - 选择**添加重写规则**。

     ![添加重写规则](media/rewrite-http-headers-portal/add-rewrite-rule.png)

   - 输入中重写规则的名称**重写规则名称**框。 输入的数字**规则序列**框。

     ![添加重写规则名称](media/rewrite-http-headers-portal/rule-name.png)

6. 此示例中，在仅包含对 azurewebsites.net 的引用时我们将重写的 location 标头。 若要执行此操作，添加要评估是否在响应中的 location 标头包含 azurewebsites.net 的条件：

   - 选择**添加条件**，然后选择框包含**如果**说明进行操作以将其展开。

     ![添加条件](media/rewrite-http-headers-portal/add-condition.png)

   - 在中**若要检查的变量的类型**列表中，选择**HTTP 标头**。

   - 在中**标头类型**列表中，选择**响应**。

   - 因为在此示例中，我们将评估 location 标头，这是常见标头，则选择**常见标头**下**标头名称**。

   - 在中**常见标头**列表中，选择**位置**。

   - 下**区分大小写**，选择**否**。

   - 在中**运算符**列表中，选择**等号 （=）**。

   - 输入正则表达式模式。 在此示例中，我们将使用该模式`(https?):\/\/.*azurewebsites\.net(.*)$`。

   - 选择“确定”。

     ![配置 If 条件](media/rewrite-http-headers-portal/condition.png)

7. 添加要重写的 location 标头的操作：

   - 在中**操作类型**列表中，选择**设置**。

   - 在中**标头类型**列表中，选择**响应**。

   - 下**标头名称**，选择**常见标头**。

   - 在中**常见标头**列表中，选择**位置**。

   - 输入的标头值。 在此示例中，我们将使用`{http_resp_Location_1}://contoso.com{http_resp_Location_2}`作为标头值。 此值将替换*azurewebsites.net*与*contoso.com* location 标头中。

   - 选择“确定”。

     ![添加操作](media/rewrite-http-headers-portal/action.png)

8. 选择**创建**若要创建重写设置：

   ![选择“创建”](media/rewrite-http-headers-portal/create.png)

9. 重写集视图将打开。 验证你创建的重写集为重写集的列表：

   ![重写集视图](media/rewrite-http-headers-portal/rewrite-set-list.png)

## <a name="next-steps"></a>后续步骤

若要了解有关如何设置一些常见用例的详细信息，请参阅[常见标头重写方案](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)。