---
title: "Azure API 管理模板数据模型参考 | Microsoft 文档"
description: "了解数据模型中常用项目的实体和类型表示形式，这些数据模型适用于 Azure API 管理中的开发人员门户模板。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 07e5e15f4f4c4281a93c8c3267c0225b1d79af45
ms.openlocfilehash: 72936a4d38f809934ddea74e5ae4a6029450a97c
ms.contentlocale: zh-cn
ms.lasthandoff: 08/31/2017

---
# <a name="azure-api-management-template-data-model-reference"></a>Azure API 管理模板数据模型参考
本主题介绍数据模型中常用项目的实体和类型表示形式，这些数据模型适用于 Azure API 管理中的开发人员门户模板。  
  
 如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)。  
  
-   [API](#API)  
-   [API 摘要](#APISummary)  
-   [应用程序](#Application)  
-   [附件](#Attachment)  
-   [代码示例](#Sample)  
-   [评论](#Comment)  
-   [筛选](#Filtering)  
-   [标头](#Header)  
-   [HTTP 请求](#HTTPRequest)  
-   [HTTP 响应](#HTTPResponse)  
-   [问题](#Issue)  
-   [操作](#Operation)  
-   [操作菜单](#Menu)  
-   [操作菜单项](#MenuItem)  
-   [分页](#Paging)  
-   [Parameter](#Parameter)  
-   [产品](#Product)  
-   [提供程序](#Provider)  
-   [表示形式](#Representation)  
-   [订阅](#Subscription)  
-   [订阅摘要](#SubscriptionSummary)  
-   [用户帐户信息](#UserAccountInfo)  
-   [用户登录](#UseSignIn)  
-   [用户注册](#UserSignUp)  
  
##  <a name="API"></a> API  
 `API` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|id|字符串|资源标识符。 唯一标识当前 API 管理服务实例中的 API。 值是有效的相对 URL，采用 `apis/{id}` 格式，其中 `{id}` 是 API 标识符。 此属性为只读。|  
|name|字符串|API 的名称。 不得为空。 最大长度为 100 个字符。|  
|description|字符串|API 的说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|serviceUrl|字符串|实现此 API 的后端服务的绝对 URL。|  
|路径|字符串|相对 URL，用于唯一标识此 API 及其在 API 管理服务实例中的所有资源路径。 可将其追加到在服务实例创建过程中指定的 API 终结点基 URL，构成此 API 的公共 URL。|  
|protocols|数字数组|说明可在哪些协议上调用此 API 中的操作。 允许的值为 `1 - http` 和/或 `2 - https`。|  
|authenticationSettings|[授权服务器身份验证设置](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|此 API 中包含的身份验证设置的集合。|  
|subscriptionKeyParameterNames|对象|可选属性，可用于指定包含订阅密钥的查询参数和/或标头参数的自定义名称。 如果存在此属性，则其必须包含以下两个属性中的至少一个。<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API 摘要  
 `API summary` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|id|字符串|资源标识符。 唯一标识当前 API 管理服务实例中的 API。 值是有效的相对 URL，采用 `apis/{id}` 格式，其中 `{id}` 是 API 标识符。 此属性为只读。|  
|name|字符串|API 的名称。 不得为空。 最大长度为 100 个字符。|  
|description|字符串|API 的说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
  
##  <a name="Application"></a> 应用程序  
 `application` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|字符串|应用程序的唯一标识符。|  
|标题|字符串|应用程序的标题。|  
|说明|字符串|应用程序的说明。|  
|URL|URI|应用程序的 URI。|  
|版本|字符串|应用程序的版本信息。|  
|要求|字符串|应用程序的要求的说明。|  
|状态|数字|应用程序的当前状态。<br /><br /> - 0 - 已注册<br /><br /> - 1 - 已提交<br /><br /> - 2 - 已发布<br /><br /> - 3 - 已拒绝<br /><br /> - 4 - 未发布|  
|RegistrationDate|DateTime|注册应用程序的日期和时间。|  
|CategoryId|数字|应用程序的类别（财务、娱乐等）|  
|DeveloperId|字符串|提交应用程序的开发人员的唯一标识符。|  
|附件|[附件](#Attachment)实体的集合。|应用程序的任何附件，例如屏幕截图或图标。|  
|图标|[附件](#Attachment)|应用程序的图标。|  
  
##  <a name="Attachment"></a> 附件  
 `attachment` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|UniqueId|字符串|附件的唯一标识符。|  
|URL|字符串|资源的 URL。|  
|类型|字符串|附件的类型。|  
|ContentType|字符串|附件的媒体类型。|  
  
##  <a name="Sample"></a> 代码示例  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|title|字符串|操作的名称。|  
|snippet|字符串|此属性已弃用，不应使用。|  
|brush|字符串|显示代码示例时要使用的代码语法着色模板。 允许的值为 `plain`、`php`、`java`、`xml`、`objc`、`python`、`ruby`、`csharp`。|  
|template|字符串|此代码示例模板的名称。|  
|body|字符串|占位符，代表代码片段的代码示例部分。|  
|方法|字符串|此操作的 HTTP 方法。|  
|scheme|字符串|将用于操作请求的协议。|  
|路径|字符串|操作的路径。|  
|查询|字符串|包含已定义参数的查询字符串示例。|  
|主机|字符串|API 管理服务网关（针对包含此操作的 API）的 URL。|  
|headers|[标头](#Header)实体的集合。|此操作的标头。|  
|参数|[参数](#Parameter)实体的集合。|为此操作定义的参数。|  
  
##  <a name="Comment"></a> 注释  
 `API` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|数字|注释的 ID。|  
|CommentText|字符串|注释的正文。 可以包含 HTML。|  
|DeveloperCompany|字符串|开发人员所在公司的名称。|  
|PostedOn|DateTime|发布注释的日期和时间。|  
  
##  <a name="Issue"></a> 问题  
 `issue` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|字符串|问题的唯一标识符。|  
|ApiID|字符串|报告此问题时所针对的 API 的 ID。|  
|标题|字符串|问题的标题。|  
|说明|字符串|问题的说明。|  
|SubscriptionDeveloperName|字符串|报告此问题的开发人员的名字。|  
|IssueState|字符串|问题的当前状态。 可能的值为“已提议”、“已打开”、“已关闭”。|  
|ReportedOn|DateTime|报告此问题的日期和时间。|  
|注释|[注释](#Comment)实体的集合。|此问题的注释。|  
|附件|[附件](api-management-template-data-model-reference.md#Attachment)实体的集合。|此问题的任何附件。|  
|服务|[API](#API) 实体的集合。|报告此问题的用户所订阅的 API。|  
  
##  <a name="Filtering"></a> 筛选  
 `filtering` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|模式|字符串|当前的搜索词；如果没有搜索词，则为 `null`。|  
|占位符|字符串|没有指定搜索词时，在搜索框中显示的文本。|  
  
##  <a name="Header"></a> 标头  
 本部分描述 `parameter` 表示形式。  
  
|属性|说明|类型|  
|--------------|-----------------|----------|  
|name|字符串|参数名称。|  
|description|字符串|参数说明。|  
|value|字符串|标头值。|  
|typeName|字符串|标头值的数据类型。|  
|options|字符串|选项。|  
|必填|布尔值|标头是否为必需。|  
|readOnly|布尔值|标头是否为只读。|  
  
##  <a name="HTTPRequest"></a> HTTP 请求  
 本部分描述 `request` 表示形式。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|description|字符串|操作请求说明。|  
|headers|[标头](#Header)实体数组。|请求标头。|  
|参数|[参数](#Parameter)数组|操作请求参数的集合。|  
|representations|[表示形式](#Representation)数组|操作请求表示形式的集合。|  
  
##  <a name="HTTPResponse"></a> HTTP 响应  
 本部分描述 `response` 表示形式。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|statusCode|正整数|操作响应状态代码。|  
|description|字符串|操作响应说明。|  
|representations|[表示形式](#Representation)数组|操作响应表示形式的集合。|  
  
##  <a name="Operation"></a> 操作  
 `operation` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|id|字符串|资源标识符。 唯一标识当前 API 管理服务实例中的操作。 值是有效的相对 URL，采用 `apis/{aid}/operations/{id}` 格式，其中 `{aid}` 是 API 标识符，`{id}` 是操作标识符。 此属性为只读。|  
|name|字符串|操作的名称。 不得为空。 最大长度为 100 个字符。|  
|description|字符串|操作说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|scheme|字符串|说明可在哪些协议上调用此 API 中的操作。 允许的值为 `http`、`https` 或者 `http` 和 `https`。|  
|uriTemplate|字符串|相对 URL 模板，标识此操作的目标资源。 可以包括参数。 示例：`customers/{cid}/orders/{oid}/?date={date}`|  
|主机|字符串|托管 API 的 API 管理网关 URL。|  
|httpMethod|字符串|操作 HTTP 方法。|  
|请求|[HTTP 请求](#HTTPRequest)|一个实体，包含请求详细信息。|  
|responses|[HTTP 响应](#HTTPResponse)数组|操作 [HTTP 响应](#HTTPResponse)实体数组。|  
  
##  <a name="Menu"></a> 操作菜单  
 `operation menu` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ApiId|字符串|当前 API 的 ID。|  
|CurrentOperationId|字符串|当前操作的 ID。|  
|操作|字符串|菜单类型。|  
|MenuItems|[操作菜单项](#MenuItem)实体的集合。|当前 API 的操作。|  
  
##  <a name="MenuItem"></a> 操作菜单项  
 `operation menu item` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|字符串|操作的 ID。|  
|标题|字符串|操作说明。|  
|HttpMethod|字符串|此操作的 Http 方法。|  
  
##  <a name="Paging"></a> 分页  
 `paging` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|Page|数字|当前页码。|  
|PageSize|数字|要显示在单个页面上的最大结果数。|  
|TotalItemCount|数字|要显示的项数。|  
|ShowAll|布尔值|是否在单页上显示所有结果。|  
|PageCount|数字|结果的页数。|  
  
##  <a name="Parameter"></a> 参数  
 本部分描述 `parameter` 表示形式。  
  
|属性|说明|类型|  
|--------------|-----------------|----------|  
|name|字符串|参数名称。|  
|description|字符串|参数说明。|  
|value|字符串|参数值。|  
|options|字符串数组|为查询参数值定义的值。|  
|必填|布尔值|指定参数是否为必需。|  
|kind|数字|此参数是路径参数 (1)，还是查询字符串参数 (2)。|  
|typeName|字符串|参数类型。|  
  
##  <a name="Product"></a> 产品  
 `product` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|字符串|资源标识符。 唯一标识当前 API 管理服务实例中的产品。 值是有效的相对 URL，采用 `products/{pid}` 格式，其中 `{pid}` 是产品标识符。 此属性为只读。|  
|标题|字符串|产品的名称。 不得为空。 最大长度为 100 个字符。|  
|说明|字符串|产品说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|术语|字符串|产品使用条款。 当开发人员尝试订阅此产品时，系统会显示这些条款，开发人员需接受这些条款才能完成订阅过程。|  
|ProductState|数字|指定产品是否已发布。 开发人员可以在开发人员门户中发现已发布的产品。 尚未发布的产品只对管理员可见。<br /><br /> 允许用于产品状态的值包括：<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|布尔值|指定用户是否可以同时拥有此产品的多个订阅。|  
|MultipleSubscriptionsCount|数字|当前用户订阅此产品时的订阅数目。|  
  
##  <a name="Provider"></a> 提供程序  
 `provider` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|属性|字符串字典|此身份验证提供程序的属性。|  
|AuthenticationType|字符串|提供程序类型。 （Azure Active Directory、Facebook 登录、Google 帐户、Microsoft 帐户、Twitter）。|  
|Caption|字符串|提供程序的显示名称。|  
  
##  <a name="Representation"></a> 表示形式  
 本部分描述 `representation`。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|contentType|字符串|指定此表示形式的已注册内容类型或自定义内容类型，例如 `application/xml`。|  
|示例|字符串|表示形式的示例。|  
  
##  <a name="Subscription"></a> 订阅  
 `subscription` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|字符串|资源标识符。 唯一标识当前 API 管理服务实例中的订阅。 值是有效的相对 URL，采用 `subscriptions/{sid}` 格式，其中 `{sid}` 是订阅标识符。 此属性为只读。|  
|ProductId|字符串|已订阅产品的产品资源标识符。 值是有效的相对 URL，采用 `products/{pid}` 格式，其中 `{pid}` 是产品标识符。|  
|ProductTitle|字符串|产品的名称。 不得为空。 最大长度为 100 个字符。|  
|ProductDescription|字符串|产品说明。 不得为空。 可以包含 HTML 格式标记。 最大长度为 1000 个字符。|  
|ProductDetailsUrl|字符串|指向产品详细信息的相对 URL。|  
|state|字符串|订阅的状态。 可能的状态包括：<br /><br /> - `0 - suspended` – 订阅被阻止，订阅服务器无法调用产品的任何 API。<br /><br /> - `1 - active` – 订阅处于活动状态。<br /><br /> - `2 - expired` – 订阅已达到其到期日期，因此已停用。<br /><br /> - `3 - submitted` – 开发人员已提交订阅请求，但管理员尚未批准或拒绝该请求。<br /><br /> - `4 - rejected` – 管理员已拒绝订阅请求。<br /><br /> - `5 - cancelled` – 开发人员或管理员已取消订阅。|  
|DisplayName|字符串|订阅的显示名称。|  
|CreatedDate|dateTime|订阅的创建日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|CanBeCancelled|布尔值|当前用户是否可以取消订阅。|  
|IsAwaitingApproval|布尔值|订阅是否待批。|  
|StartDate|dateTime|订阅的开始日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|ExpirationDate|dateTime|订阅的到期日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|NotificationDate|dateTime|订阅的通知日期，采用 ISO 8601 格式：`2014-06-24T16:25:00Z`。|  
|primaryKey|字符串|主要订阅密钥。 最大长度为 256 个字符。|  
|secondaryKey|字符串|辅助订阅密钥。 最大长度为 256 个字符。|  
|CanBeRenewed|布尔值|当前用户是否可以续订订阅。|  
|HasExpired|布尔值|订阅是否已到期。|  
|IsRejected|布尔值|是否已拒绝订阅请求。|  
|CancelUrl|字符串|用于取消订阅的相对 URL。|  
|RenewUrl|字符串|用于续订订阅的相对 URL。|  
  
##  <a name="SubscriptionSummary"></a> 订阅摘要  
 `subscription summary` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|ID|字符串|资源标识符。 唯一标识当前 API 管理服务实例中的订阅。 值是有效的相对 URL，采用 `subscriptions/{sid}` 格式，其中 `{sid}` 是订阅标识符。 此属性为只读。|  
|DisplayName|字符串|订阅的显示名称|  
  
##  <a name="UserAccountInfo"></a> 用户帐户信息  
 `user account info` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|FirstName|字符串|名字。 不得为空。 最大长度为 100 个字符。|  
|LastName|字符串|姓氏。 不得为空。 最大长度为 100 个字符。|  
|电子邮件|字符串|电子邮件地址。 不得为空，且必须在服务实例中唯一。 最大长度为 254 个字符。|  
|密码|字符串|用户帐户密码。|  
|NameIdentifier|字符串|帐户标识符，与用户电子邮件相同。|  
|ProviderName|字符串|身份验证提供程序名称。|  
|IsBasicAccount|布尔值|如果此帐户使用电子邮件和密码注册，则为 true；如果此帐户使用提供程序注册，则为 false。|  
  
##  <a name="UseSignIn"></a> 用户登录  
 `user sign in` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|电子邮件|字符串|电子邮件地址。 不得为空，且必须在服务实例中唯一。 最大长度为 254 个字符。|  
|密码|字符串|用户帐户密码。|  
|ReturnUrl|字符串|用户单击“登录”时所在页面的 URL。|  
|RememberMe|布尔值|是否保存当前用户的信息。|  
|RegistrationEnabled|布尔值|是否已启用注册。|  
|DelegationEnabled|布尔值|是否已启用委派登录。|  
|DelegationUrl|字符串|委派登录 URL（如果已启用）。|  
|SsoSignUpUrl|字符串|用户的单一登录 URL（如果存在）。|  
|AuxServiceUrl|字符串|如果当前用户为管理员，则此项为指向 Azure 经典门户中服务实例的链接。|  
|提供程序|[提供程序](#Provider)实体的集合|此用户的身份验证提供程序。|  
|UserRegistrationTerms|字符串|用户必须在登录之前同意的条款。|  
|UserRegistrationTermsEnabled|布尔值|是否启用条款。|  
  
##  <a name="UserSignUp"></a> 用户注册  
 `user sign up` 实体具有以下属性。  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|PasswordConfirm|布尔值|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|密码|字符串|用户帐户密码。|  
|PasswordVerdictLevel|数字|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|UserRegistrationTerms|字符串|用户必须在登录之前同意的条款。|  
|UserRegistrationTermsOptions|数字|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|ConsentAccepted|布尔值|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|电子邮件|字符串|电子邮件地址。 不得为空，且必须在服务实例中唯一。 最大长度为 254 个字符。|  
|FirstName|字符串|名字。 不得为空。 最大长度为 100 个字符。|  
|LastName|字符串|姓氏。 不得为空。 最大长度为 100 个字符。|  
|UserData|字符串|[注册](api-management-page-controls.md#sign-up)控件使用的值。|  
|NameIdentifier|字符串|[注册](api-management-page-controls.md#sign-up)注册控件使用的值。|  
|ProviderName|字符串|身份验证提供程序名称。|

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。

