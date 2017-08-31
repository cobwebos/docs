---
title: "Azure API 管理中的应用程序模板 | Microsoft Docs"
description: "了解如何在 Azure API 管理中自定义开发人员门户中的应用程序页面的内容。"
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 77fd7b5b339a8ede8a297bec96f91f0a243cc18d
ms.openlocfilehash: df71d6d36949f457ad95576e37ae3da2fef7d4d7
ms.contentlocale: zh-cn
ms.lasthandoff: 01/12/2017

---
# <a name="application-templates-in-azure-api-management"></a>Azure API 管理中的应用程序模板
通过 Azure API 管理，用户能够使用一组用于配置内容的模板自定义开发人员门户页面的内容。 使用 [DotLiquid](http://dotliquidmarkup.org/) 语法和所选编辑器（例如 [DotLiquid for Designers](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)），以及提供的一组本地化[字符串资源](api-management-template-resources.md#strings)、[字形资源](api-management-template-resources.md#glyphs)和[页面控件](api-management-page-controls.md)，即可根据这些模板的使用需要非常灵活地配置页面内容。  
  
 本部分中的模板可用于自定义开发人员门户中的应用程序页面的内容。  
  
-   [应用程序列表](#ProductList)  
  
-   [应用程序](#Application)  
  
> [!NOTE]
>  示例默认模板包含在后续文档中，但是会因持续改进而有所更改。 通过导航到所需单个模板，可在开发人员门户中查看实时的默认模板。 如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)。  
  
##  <a name="ProductList"></a>应用程序列表  
 **应用程序列表**模板可用于自定义开发人员门户中应用程序列表页的正文。  
  
 ![应用程序列表页开发人员门户模板](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "APIM 应用程序列表页开发人员门户模板")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
            </li>     
        {% endfor %}  
        </ul>  
        <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    </div>  
</div>  
```  
  
### <a name="controls"></a>控制  
 `Product list` 模板可使用以下[页面控件](api-management-page-controls.md)。  
  
-   [paging-control](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>数据模型  
  
|属性|类型|说明|  
|--------------|----------|-----------------|  
|分页|[分页](api-management-template-data-model-reference.md#Paging)实体。|应用程序集合的分页信息。|  
|应用程序|[应用程序](api-management-template-data-model-reference.md#Application)实体的集合。|对当前用户可见的应用程序。|  
|CategoryName|字符串|应用程序的类别。|  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="Application"></a>应用程序  
 **应用程序**模板可用于自定义开发人员门户中应用程序页的正文。  
  
 ![应用程序页开发人员门户模板](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "APIM 应用程序页开发人员门户模板")  
  
### <a name="default-template"></a>默认模板  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>控制  
 `Application` 模板不允许使用任何[页面控件](api-management-page-controls.md)。  
  
### <a name="data-model"></a>数据模型  
 [应用程序](api-management-template-data-model-reference.md#Application)实体。  
  
### <a name="sample-template-data"></a>示例模板数据  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>后续步骤
如需详细了解如何使用模板，请参阅[如何使用模板自定义 API 管理开发人员门户](api-management-developer-portal-templates.md)。
