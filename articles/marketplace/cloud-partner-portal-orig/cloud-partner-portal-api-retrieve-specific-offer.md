---
title: 检索特定的产品/服务 API |Azure Marketplace
description: API 可检索发布者命名空间中指定的产品/服务。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 030fb221b9227acf9c5dcda8797b106e51f56d64
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827349"
---
<a name="retrieve-a-specific-offer"></a>检索特定产品/服务
=========================

检索发布者命名空间中指定的产品/服务。  

还可以检索特定版本的产品/服务，或检索草稿、视图或生产槽中的产品/服务。 如果未指定槽，则默认值为 `draft`。 尝试检索尚未预览或发布的产品/服务会导致 `404 Not Found` 错误。

> [!WARNING]
> 此 API 不会检索机密类型字段的机密值。

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>URI 参数
--------------


| **名称**    | **说明**                                                                          | **数据类型** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId。 例如，Contoso                                                        | String        |
| offerId     | 唯一标识产品/服务的 Guid。                                                 | String        |
| version     | 所检索的产品/服务的版本。 默认情况下，将检索最新的产品/服务版本。 | Integer       |
| slotId      | 要从中检索产品/服务的槽，可以是以下值之一：      <br/>  - `Draft`（默认值）检索当前处于草稿状态的产品/服务版本。  <br/>  -  `Preview` 检索当前处于预览状态的产品/服务版本。     <br/>  -  `Production` 检索当前处于生产状态的产品/服务版本。          |      枚举 |
| api-version | API 的最新版本                                                                    | Date          |
|  |  |  |


<a name="header"></a>标头
------

|  **名称**          |   **值**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  授权     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>正文示例
------------

### <a name="response"></a>响应

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>响应正文属性

|  **名称**       |   **说明**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | 标识产品/服务的类型                                                                                                    |
|  publisherId    | 发布者的唯一标识符                                                                                              |
|  status         | 产品/服务的状态。 有关可能值的列表，请参阅下面的[产品/服务状态](#offer-status)。                                  |
|  ID             | 唯一标识产品/服务的 GUID                                                                                         |
|  version        | 该产品/服务的当前版本。 客户端无法修改版本属性。 它在每次发布后都会递增。    |
|  定义     | 工作负载的实际定义                                                                                               |
|  changedTime    | 上次修改该产品/服务时的 UTC 日期/时间                                                                                   |
|  |  |


### <a name="response-status-codes"></a>响应状态代码

| **代码**  | **说明**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - 已成功处理请求，发布者下的所有产品/服务都已返回给客户端。               |
|  400      | `Bad/Malformed request` - 错误响应正文可能包含更多信息。                                                 |
|  403      | `Forbidden` - 客户端无法访问指定的命名空间。                                                        |
|  404      | `Not found` - 指定的实体不存在。 客户端应检查 publisherId、offerId 和 version（如果指定）。      |
|  |  |


### <a name="offer-status"></a>产品/服务状态

|  **名称**                   |   **说明**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | 产品/服务从未发布过。               |
|  NotStarted                 | 产品/服务是新的，但未启动。              |
|  WaitingForPublisherReview  | 产品/服务正在等待发布者批准。      |
|  正在运行                    | 正在处理产品/服务提交。          |
|  已成功                  | 产品/服务提交已完成处理。    |
|  已取消                   | 产品/服务提交已取消。                |
|  已失败                     | 产品/服务提交失败。                      |
|  |  |
