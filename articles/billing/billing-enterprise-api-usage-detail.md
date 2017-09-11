---
title: "Azure 计费企业 API - 使用情况详细信息 | Microsoft Docs"
description: "了解 Azure 帐单用量和 RateCard API，洞察 Azure 资源消耗量和趋势。"
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: zh-cn
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>适用于企业客户的报告 API - 使用情况详细信息

使用情况详细信息 API 提供已耗用量和注册估计费用的日常明细。 结果还包括有关实例、计量和部门信息。 可以按照计费周期或指定的开始日期和结束日期查询 API。 
## <a name="consumption-apis"></a>消耗量 API


##<a name="request"></a>请求 
[此处](billing-enterprise-api.md)指定了需要添加的通用标头属性。 如果未指定计费周期，则返回当前计费周期的数据。 可以使用 yyyy-MM-dd 格式的开始日期和结束日期参数指定自定义时间范围。 支持的最大时间范围为 36 个月。  

|方法 | 请求 URI|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> 若要使用预览版 API，请在上述 URL 中将 v2 替换为 v1。
>

## <a name="response"></a>响应

> 由于数据量可能很大，因此会将结果集分页。 如果存在 nextLink 属性，该属性将指定下一页数据的链接。 如果链接为空，则表示这是最后一页。 
<br/>

    {
        "id": "string",
        "data": [
            {                       
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "Cost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ],
        "nextLink": "string"
    }

<br/>
响应属性定义

|属性名称| 类型| 说明
|-|-|-|
|id| 字符串| API 调用的唯一 ID。 |
|数据| JSON 数组| 每个 instance\meter 的日常使用情况详细信息数组。|
|nextLink| 字符串| 存在多页数据时，nextLink 将指向返回下一页数据的 URL。 |
|accountId| int| 已过时字段。 用于确保后向兼容性。 |
|productId| int| 已过时字段。 用于确保后向兼容性。 |
|resourceLocationId| int| 已过时字段。 用于确保后向兼容性。 |
|consumedServiceID| int| 已过时字段。 用于确保后向兼容性。 |
|departmentId| int| 已过时字段。 用于确保后向兼容性。 |
|accountOwnerEmail| 字符串| 帐户所有者的电子邮件帐户。 |
|accountName| 字符串| 客户输入的帐户名称。 |
|serviceAdministratorId| 字符串| 服务管理员的电子邮件地址。 |
|subscriptionId| int| 已过时字段。 用于确保后向兼容性。 |
|subscriptionGuid| 字符串| 订阅的全局唯一标识符。 |
|subscriptionName| 字符串| 订阅的名称。 |
|日期| 字符串| 产生消耗的日期。 |
|product| 字符串| 有关计量的其他详细信息。 示例： A1 (VM) Windows - 亚太东部|
|meterId| 字符串| 发出使用的计量的标识符。 |
|meterCategory| 字符串| 所用的 Azure 平台服务。 |
|meterSubCategory| 字符串| 定义 Azure 服务类型，该类型可能会影响费率。 示例：A1 VM（非 Windows）|
|meterRegion| 字符串| 指明某些服务的数据中心的位置，这些服务根据数据中心位置进行定价。 |
|meterName| 字符串| 计量的名称。 |
|consumedQuantity| double| 已耗用的计量量。 |
|resourceRate| double| 每个计费单位适用的费率。 |
|cost| double| 计量产生的费用。 |
|resourceLocation| 字符串| 标识计量正在其中运行的数据中心。 |
|consumedService| 字符串| 所用的 Azure 平台服务。 |
|instanceId| 字符串| 此标识符是资源名称或完全限定的资源 ID。 有关详细信息，请参阅 [Azure 资源管理器 API](https://docs.microsoft.com/rest/api/resources/resources) |
|serviceInfo1| 字符串| 内部 Azure 服务元数据。 |
|serviceInfo2| 字符串| 例如，虚拟机的映像类型和 ExpressRoute 的 ISP 名称。 |
|additionalInfo| 字符串| 服务特定的元数据。 例如，虚拟机的映像类型。 |
|标记| 字符串| 客户添加的标记。 有关详细信息，请参阅[使用标记来组织 Azure 资源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags)。 |
|storeServiceIdentifier| 字符串| 不使用此列。 用于确保后向兼容性。 |
|departmentName| 字符串| 部门名称。 |
|costCenter| 字符串| 使用情况关联的成本中心。 |
|unitOfMeasure| 字符串| 指明服务的计价单位。 例如，GB、小时、10,000 秒。 |
|resourceGroup| 字符串| 部署的测定仪正在其中运行的资源组。 有关详细信息，请参阅 [Azure Resource Manager 概述](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)。 |
<br/>
## <a name="see-also"></a>另请参阅

* [计费周期 API](billing-enterprise-api-billing-periods.md)

* [余额和摘要 API](billing-enterprise-api-balance-summary.md)

* [Marketplace 费用 API](billing-enterprise-api-marketplace-storecharge.md) 

* [价目表 API](billing-enterprise-api-pricesheet.md)

