---
title: 使用 REST API 查看 Azure 企业合约计费数据 | Microsoft Docs
description: 了解如何使用 Azure REST API 来查看企业合约计费信息。
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064290"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>使用 Azure REST API 查看企业合约计费信息

Azure 报告 API 可帮助查看和管理 Azure 费用。

这里介绍了如何检索与企业帐户合约相关联的当前帐单。

若要检索当前帐单，请执行以下操作：
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>生成请求  

`{enrollmentID}` 参数是必需的，并且应包含企业帐户 (EA) 的合约 ID。

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type：|必需。 设置为 `application/json`。|  
|Authorization：|必需。 设置为有效的 `Bearer` [API 密钥](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)。 |  

此示例演示返回当前计费周期的详细信息的同步调用。 出于性能原因，同步调用返回上一个月的信息。  还可以[采用异步方式调用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 来返回 36 个月的数据。


## <a name="response"></a>响应  

系统会返回状态代码 200（正常）以指示一个成功响应，该响应中包含你的帐户的详细费用列表。

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

**data** 中的每个项都表示一种费用：

|响应属性|说明|
|----------------|----------|
|**cost** | 收取的金额，以适用于数据中心位置的货币为单位。 |
|**subscriptionGuid** | 订阅的的全局唯一 ID。 | 
|**departmentId** | 部门（如果有）的 ID。 |
|**date** | 计费的日期。 |
|**tags** | 包含与订阅关联的标记的 JSON 字符串。 |
|**resourceGroup**|包含产生费用的对象的资源组名称。 |
|**nextLink**| 设置时，指定一个 URL 作为详细信息的下一“页”。 当页面为最后一个页面时为空。 |  
||
  
部门 ID、资源组、标记和相关字段由 EA 管理员定义。  

此示例已经过缩略；请参阅[获取使用情况详细信息](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)以了解每个响应字段的完整说明。 

其他状态代码指示错误条件。 在这些情况下，响应对象解释请求失败的原因。

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>后续步骤 
- 查看 [Enterprise Reporting 概述](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- 详细研究[企业计费 REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Azure REST API 入门](https://docs.microsoft.com/rest/api/azure/)   
