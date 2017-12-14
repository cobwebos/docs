---
title: "Azure API 管理页面控件 | Microsoft 文档"
description: "了解适用于 Azure API 管理中的开发人员门户模板的页面控件。"
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: 4fd91ae079ff054932f4572874001dd69dd848e7
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2017
---
# <a name="azure-api-management-page-controls"></a>Azure API 管理页面控件
Azure API 管理提供以下控件，适用于开发人员门户模板。  
  
要使用控件，请将其放在开发人员门户模板中的所需位置。 某些控件（例如 [app-actions](#app-actions) 控件）有参数，如以下示例所示：  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 参数的值作为模板的数据模型的一部分传入。 大多数情况下，只需粘贴提供的每个控件的示例，即可正常使用该控件。 有关参数值的详细信息，可参阅每个模板的数据模型部分，其中可能使用了控件。  
  
 如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)。  
  
## <a name="developer-portal-template-page-controls"></a>开发人员门户模板页面控件  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a> app-actions  
 `app-actions` 控件提供的用户界面适用于与开发人员门户中用户配置文件页上的应用程序交互。  
  
 ![app&#45;actions 控件](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-actions 控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>parameters  
  
|参数|说明|  
|---------------|-----------------|  
|appId|应用程序的 ID。|  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `app-actions` 控件可用于以下开发人员门户模板：  
  
-   [应用程序](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> basic-signin  
 `basic-signin` 控件提供了一个控件，用于收集开发人员门户中登录页上的用户登录信息。  
  
 ![basic&#45;signin 控件](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-signin 控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>parameters  
 无。  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `basic-signin` 控件可用于以下开发人员门户模板：  
  
-   [登录](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> paging-control  
 `paging-control` 在开发人员门户页上提供分页功能，该页面会显示项目列表。  
  
 ![分页控件](./media/api-management-page-controls/APIM-paging-control.png "APIM 分页控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>parameters  
 无。  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `paging-control` 控件可用于以下开发人员门户模板：  
  
-   [API 列表](api-management-api-templates.md#APIList)  
  
-   [问题列表](api-management-issue-templates.md#IssueList)  
  
-   [产品列表](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> 提供程序  
 `providers` 控件提供了一个控件，用于在开发人员门户的登录页中选择身份验证提供程序。  
  
 ![提供程序控件](./media/api-management-page-controls/APIM-providers-control.png "APIM 提供程序控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>parameters  
 无。  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `providers` 控件可用于以下开发人员门户模板：  
  
-   [登录](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> search-control  
 `search-control` 在开发人员门户页上提供搜索功能，该页面会显示项目列表。  
  
 ![搜索控件](./media/api-management-page-controls/APIM-search-control.png "APIM 搜索控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>parameters  
 无。  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `search-control` 控件可用于以下开发人员门户模板：  
  
-   [API 列表](api-management-api-templates.md#APIList)  
  
-   [产品列表](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> 注册  
 `sign-up` 控件提供了一个控件，用于收集开发人员门户中注册页上的用户配置文件信息。  
  
 ![注册控件](./media/api-management-page-controls/APIM-sign-up-control.png "APIM 注册控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>parameters  
 无。  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `sign-up` 控件可用于以下开发人员门户模板：  
  
-   [注册](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> subscribe-button  
 `subscribe-button` 提供了一个控件，可供用户订阅产品。  
  
 ![subscribe&#45;button 控件](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM subscribe-button 控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>parameters  
 无。  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `subscribe-button` 控件可用于以下开发人员门户模板：  
  
-   [产品](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> subscription-cancel  
 `subscription-cancel` 控件提供了一个控件，用于在开发人员门户的用户配置文件页中取消产品订阅。  
  
 ![subscription&#45;cancel 控件](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM subscription-cancel 控件")  
  
### <a name="usage"></a>使用情况  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>parameters  
  
|参数|说明|  
|---------------|-----------------|  
|subscriptionId|要取消的订阅的 ID。|  
|cancelUrl|订阅取消 URL。|  
  
### <a name="developer-portal-templates"></a>开发人员门户模板  
 `subscription-cancel` 控件可用于以下开发人员门户模板：  
  
-   [产品](api-management-product-templates.md#Product)

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。