---
title: "Azure API 管理中的策略 | Microsoft Docs"
description: "了解如何在 API 管理创建、编辑和配置策略。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 537e5caf-708b-430e-a83f-72b70af28aa9
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 30ec6f45da114b6c7bc081f8a2df46f037de61fd
ms.openlocfilehash: 3d78af41bbe13bcec9336452110a857d114ad006


---
# <a name="policies-in-azure-api-management"></a>Azure API 管理中的策略
在 Azure API 管理中，策略是一项强大的系统功能，允许发布者通过配置更改 API 的行为。 策略是一组语句，在请求或 API 的响应时按顺序执行。 流行的语句包括从 XML 到 JSON 的格式转换，并调用速率限制来限制从一名开发人员的传入调用。 许多策略开箱即用。

请参阅[策略参考][Policy Reference]了解政策说明完整列表及其设置。

策略在网关内部应用，该网关位于 API 使用者和托管 API 之间。 该网关接收所有请求，并通常将其原封不动地转发到基础 API。 但是策略可以将更改应用于入站的请求和出站响应。

在任何 API 管理策略中，策略表达式可以用作属性值或文本值，除非该策略另外指定。 某些策略（如[控制流][Control flow]和[设置变量][Set variable]策略）基于策略表达式。 有关详细信息，请参阅[高级策略][Advanced policies]和[策略表达式][Policy expressions]。

## <a name="scopes"> </a>如何配置策略
可在全局范围内配置策略或[产品][Product]的范围、[API][API] 或[操作][Operation]。 若要配置策略，请导航到发布服务器门户中的策略编辑器。

![策略菜单][policies-menu]

策略编辑器有三个主要部分：策略范围（上）、在其中编辑策略的策略定义（左）和语句列表（右）：

![策略编辑器][policies-editor]

要开始配置策略，必须首先选择该策略将应用的范围。 在下面的屏幕快照中，已选中“初学者”产品。 请注意，策略名称旁的正方形符号表示某一策略已应用于此级别。

![范围][policies-scope]

由于已应用策略，配置如定义视图中所示。

![配置][policies-configure]

第一个显示只读的策略。 为了编辑定义，请单击“配置策略”操作。

![编辑][policies-edit]

策略定义是一个简单的 XML 文档，用于描述一个入站和出站语句序列。 可以直接在定义窗口中编辑 XML。 右侧提供语句的列表，同时启用适用于当前范围的语句并突出显示；如上面的屏幕快照中的“限制调用速率”语句所示。

单击启用的语句将在定义视图中的光标位置添加相应的 XML。 

> [!NOTE]
> 如果无法启用要添加的策略，请确保为该策略设置的范围是正确的。 每个策略语句都设计有特定的使用范围，需在特定的策略部分使用。 若要查看某个策略的策略部分和范围，请参阅[策略参考][Policy Reference]中该策略的“用法”部分。
> 
> 

[策略参考][Policy Reference]提供策略语句的完整列表以及其设置。

例如，若要添加新的语句以限制到指定 IP 地址的入站请求，请将光标置于 `inbound` XML 元素的内容中，然后单击“限制调用方 IP”语句。

![限制策略][policies-restrict]

这会将 XML 代码片段添加到 `inbound` 元素，提供如何配置该语句的指导。

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

要限制入站请求并接受来自 IP 地址 1.2.3.4 的那些，请修改 XML，如下所示：

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

![保存][policies-save]

完成策略语句的配置后，单击“保存”，更改将立即传播到 API 管理网关。

## <a name="sections"> </a>了解策略配置
策略是一系列按请求和响应顺序执行的语句。 配置相应地划分为 `inbound`、`backend`、`outbound` 和 `on-error` 部分，如以下配置所示。

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

如果在处理请求的过程中出错，则会忽略 `inbound`、`backend` 或 `outbound` 部分的其余步骤，跳到 `on-error` 部分执行相关语句。 将策略语句置于 `on-error` 部分以后，即可使用 `context.LastError` 属性查看错误、使用 `set-body` 策略检查和自定义错误响应，以及配置发生错误时的应对措施。 错误代码可针对内置步骤，也可针对在处理策略语句的过程中会发生的错误。 有关详细信息，请参阅 [Error handling in API Management policies](https://msdn.microsoft.com/library/azure/mt629506.aspx)（API 管理策略中的错误处理）。

由于策略可以在不同级别（全局、产品、API 和操作）指定，因此可通过此配置指定一个顺序，根据父策略执行策略定义的语句。 

策略范围按以下顺序计算。

1. 全局范围
2. 产品范围
3. API 范围
4. 操作范围

范围内的语句按 `base` 元素（如果存在）的位置计算。

例如，如果在全局级别有一个策略并且为 API 配置了一个策略，则只要使用该特定 API，这两种策略都将被应用。 API 管理允许通过基础元素实现组合策略声明的确定性排序。 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

在上述示例策略定义中，`cross-domain` 语句将在执行任何更高版本的策略前执行，之后是 `find-and-replace` 策略。

如果同一策略在策略语句中出现两次，则会应用最近计算的策略。 可以使用此功能重写在层级较高的范围定义的策略。 若要在策略编辑器中查看当前范围的策略，请单击“重新计算所选范围的有效策略”。

请注意，全局策略没有父策略，因此在其中使用 `<base>` 元素无效。 

## <a name="next-steps"></a>后续步骤
观看以下有关策略表达式的视频。

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Policy Reference]: api-management-policy-reference.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operation]: api-management-howto-add-operations.md

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png



<!--HONumber=Dec16_HO3-->


