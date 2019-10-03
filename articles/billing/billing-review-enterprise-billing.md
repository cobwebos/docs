---
title: 使用 REST API 查看 Azure 企业合约计费数据 | Microsoft Docs
description: 了解如何使用 Azure REST API 来查看企业合约计费信息。
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 3ff1a119ed87c8bffff227679bec41bc653c0c42
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718771"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>使用 Azure REST API 查看企业合约计费信息

Azure 报告 API 可帮助查看和管理 Azure 费用。

在本文中，你将学习如何使用 Azure REST API 检索与计费帐户、部门或企业协议 (EA) 合约帐户关联的账单信息。 

## <a name="individual-account-billing"></a>个人帐户计费

若要为部门中的各个帐户获取使用情况详细信息，请使用以下命令：

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{billingAccountId}` 参数是必需的，并且应当包含帐户的 ID。

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type： |必需。 设置为 `application/json`。|  
|Authorization： |必需。 设置为有效的 `Bearer` [API 密钥](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)。 |  

此示例演示返回当前计费周期的详细信息的同步调用。 出于性能原因，同步调用返回上一个月的信息。  还可以[采用异步方式调用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 来返回 36 个月的数据。


## <a name="response"></a>响应  

对于成功的响应，系统会返回状态代码 200（正常），该响应中包含你的帐户的详细费用列表。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

此示例已经过缩略；有关每个响应字段和错误处理的完整说明，请参阅[为计费帐户获取使用情况详细信息](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist)。

## <a name="department-billing"></a>部门计费 

获取针对部门中的所有帐户聚合的使用情况详细信息。 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{departmentId}` 参数是必需的，并且应当包含合约帐户中的部门的 ID。

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type： |必需。 设置为 `application/json`。|  
|Authorization： |必需。 设置为有效的 `Bearer` [API 密钥](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)。 |  

此示例演示返回当前计费周期的详细信息的同步调用。 出于性能原因，同步调用返回上一个月的信息。  还可以[采用异步方式调用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 来返回 36 个月的数据。

### <a name="response"></a>响应  

对于成功的响应，系统会返回状态代码 200（正常）， 该响应中包含部门在给定计费周期和发票 ID 内的详细使用情况信息和费用的列表。


下面的示例显示了用于部门 `1234` 的 REST API 的输出。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

此示例已经过缩略；有关每个响应字段和错误处理的完整说明，请参阅[为部门获取使用情况详细信息](/rest/api/consumption/usagedetails/list#departmentusagedetailslist)。

## <a name="enrollment-account-billing"></a>合约帐户计费

获取针对合约帐户聚合的使用情况详细信息。

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

`{enrollmentAccountId}` 参数是必需的，并且应当包含合约帐户的 ID。

以下标头是必需的： 

|请求标头|说明|  
|--------------------|-----------------|  
|Content-Type： |必需。 设置为 `application/json`。|  
|Authorization： |必需。 设置为有效的 `Bearer` [API 密钥](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based)。 |  

此示例演示返回当前计费周期的详细信息的同步调用。 出于性能原因，同步调用返回上一个月的信息。  还可以[采用异步方式调用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) 来返回 36 个月的数据。

### <a name="response"></a>响应  

对于成功的响应，系统会返回状态代码 200（正常）， 该响应中包含部门在给定计费周期和发票 ID 内的详细使用情况信息和费用的列表。

下面的示例显示了用于企业合约 `1234` 的 REST API 的输出。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

此示例已经过缩略；有关每个响应字段和错误处理的完整说明，请参阅[为合约帐户获取使用情况详细信息](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist)。

## <a name="next-steps"></a>后续步骤 
- 查看 [Enterprise Reporting 概述](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- 详细研究[企业计费 REST API](https://docs.microsoft.com/rest/api/billing/)   
- [Azure REST API 入门](https://docs.microsoft.com/rest/api/azure/)   
