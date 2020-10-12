---
title: 创建或修改产品/服务-Azure Marketplace
description: 用于创建新的或更新现有产品/服务的 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87420220"
---
# <a name="create-or-modify-an-offer"></a>创建或修改产品/服务

> [!NOTE]
> 云合作伙伴门户 Api 与集成，并将在合作伙伴中心继续工作。 转换引入了少量更改。 查看 [云合作伙伴门户 API 参考](./cloud-partner-portal-api-overview.md) 中列出的更改，确保你的代码在转换到合作伙伴中心后继续工作。 CPP Api 仅适用于过渡到合作伙伴中心之前已集成的现有产品;新产品应使用合作伙伴中心提交 Api。

此调用更新发布者命名空间中的特定产品/服务，或创建新的产品/服务。

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数

|  **名称**         |  **描述**                      |  **Data type**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  发布者标识符，例如 `contoso` |   字符串 |
| offerId           |  产品/服务标识符                     |   字符串        |
| api-version       |  API 最新版本            |   Date           |
|  |  |  |

## <a name="header"></a>Header

|  **Name**        |  **值**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| 授权    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>正文示例

以下示例创建 offerID 为 `contosovirtualmachine` 的产品/服务。

### <a name="request"></a>请求

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
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
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>响应

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
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
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> 若要修改此产品/服务，请在上述请求中添加设置为“*”的 **If-Match** 标头。 使用与上面相同的请求正文，但根据需要修改值。 

### <a name="response-status-codes"></a>响应状态代码

| **代码**  |  **描述**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. 请求已成功处理，并且已成功修改产品/服务。           |
|  201      | `Created`. 请求已成功处理，并且已成功创建产品/服务。   |
|  400      | `Bad/Malformed request`. 错误响应正文可以提供更多信息。            |
|  403      | `Forbidden`. 客户端无法访问请求的命名空间。                     |
|  404      | `Not found`. 客户端引用的实体不存在。                           |
|  412      | 服务器不满足请求者在请求中指定的某一前提条件。 客户端应检查随请求发送的 ETAG。 |
|  |  |

## <a name="uploading-artifacts"></a>上传项目

应该通过将项目（例如图像和徽标）上传到 Web 上的可访问位置来共享项目，然后将每个项目作为 URI 包含在 PUT 请求中，如上例所示。 系统将检测到这些文件不存在于 Azure Marketplace 存储中，并将这些文件下载到存储中。  因此，你会发现将来的 GET 请求将返回这些文件的 Azure Marketplace 服务 URL。

## <a name="categories-and-industries"></a>类别和行业

创建新产品/服务时，必须在 marketplace 中为产品/服务指定类别。 （可选）对于某些产品/服务类型，还可以指定 "行业"。 根据产品/服务类型，使用下表中的特定密钥值提供适用于该产品/服务的类别/行业。

### <a name="azure-marketplace-categories"></a>Azure Marketplace 类别

这些类别及其各自的密钥适用于 Azure 应用、虚拟机、核心虚拟机、容器、容器应用、IoT Edge 模块和 SaaS 产品/服务类型。 以粗体倾斜 (例如 ***分析***) 中的项是类别和标准文本项 (如数据见解) 是其下的子类别。 使用正确的键值，而不更改间距或大小写。

| 类别 | SaaS 密钥 | Azure 应用密钥 | 虚拟机、容器、容器应用、IoT Edge 模块、核心虚拟机密钥 |
| --- | --- | --- | --- |
| ***分析*** | ***学*** | ***分析-azure-应用*** | ***analytics-amp*** |
| 数据见解 | 数据见解 | 数据见解 | 数据见解 |
| 数据分析 | 数据分析 | 数据分析 | 数据分析 |
| 大数据 | 大数据 | bigData | 大数据 |
| 预测分析 | 预测-分析 | 预测-分析 | 预测-分析 |
| 实时/流分析 | 实时流分析 | 实时流分析 | 实时流分析 |
| 其他 | 其他 | 其他-分析 | 其他 |
| ***AI + 机器学习*** | ***ArtificialIntelligence*** | ***ai-加上机器学习*** | ***ai-加上机器学习*** |
| 机器人服务 | 机器人-服务 | 机器人-服务 | 机器人-服务 |
| 认知服务 | 认知-服务 | 认知-服务 | 认知-服务 |
| ML 服务 | ml-服务 | ml-服务 | ml-服务 |
| 自动化机器学习 | 自动-ml | 自动-ml | 自动-ml |
| 业务/机器人流程自动化 | 业务自动-进程-自动化 | 业务自动-进程-自动化 | 业务自动-进程-自动化 |
| 数据标签 | 数据标签 | 数据标签 | 数据标签 |
| 数据准备 | 数据准备 | 数据准备 | 数据准备 |
| 知识挖掘 | 知识-挖掘 | 知识-挖掘 | 知识-挖掘 |
| ML 操作 | ml-操作 | ml-操作 | ml-操作 |
| 其他 | 其他-AI-+ 机器学习 | 其他 | 其他 |
| 区块链 | ***blockchain*** | ***blockchain*** | ***blockchain*** |
| 应用加速器 | 应用加速器 | 应用加速器 | 应用加速器 |
| 单节点分类帐 | 单节点-分类帐 | 单节点-分类帐 | 单节点-分类帐 |
| 多节点分类帐 | 多节点-分类帐 | 多节点-分类帐 | 多节点-分类帐 |
| 工具 | 工具 | 工具 | 工具 |
| 其他 | 其他 | 其他 | 其他 |
| ***计算*** | ***计算-saas*** | ***计算-azure-应用*** | ***计算*** |
| 应用程序基础结构 | appInfra | appInfrastructure | 应用程序-基础结构 |
| 操作系统 | clientOS | clientOS | 操作系统 |
| 缓存 | cache | cache | cache |
| 其他 | 其他-计算 | 其他-计算 | 其他 |
| ***容器*** | ***存放*** | ***存放*** | ***存放*** |
| 容器应用 | 容器-应用 | 容器-应用 | 容器-应用 |
| 容器映像 | 容器-映像 | 容器-映像 | 容器-映像 |
| 容器入门 | 入门-with-容器 | 入门-with-容器 | 入门-with-容器 |
| 其他 | 其他 | 其他 | 其他 |
| ***数据库*** | ***数据库-saas*** | ***database*** | ***数据库*** |
| NoSQL 数据库 | nosql-数据库 | nosql-数据库 | nosql-数据库 |
| 关系数据库 | 关系-数据库 | 关系-数据库 | 关系-数据库 |
| 分类帐/区块链数据库 | 区块链-数据库 | 区块链-数据库 | 区块链-数据库 |
| 数据 Lake | 数据-lake | 数据-lake | 数据-lake |
| 数据仓库 | 数据仓库 | 数据仓库 | 数据仓库 |
| 其他 | 其他-数据库 | 其他-数据库 | 其他 |
| ***开发人员工具*** | ***开发人员-工具-saas*** | ***开发人员-工具-azure-应用*** | ***开发人员-工具*** |
| 工具 | 工具-开发人员-工具 | 工具-开发人员-工具 | 工具-开发人员-工具 |
| 脚本 | 脚本 | 脚本 | 脚本 |
| 开发人员服务 | devService | devService | 开发人员-服务 |
| 其他 | 其他-开发人员工具 | 其他-开发人员工具 | 其他 |
| ***DevOps*** | ***devops*** | ***devops*** | ***devops*** |
| 其他 | 其他 | 其他 | 其他 |
| ***标识*** | ***identity*** | ***identity*** | ***identity*** |
| 访问管理 | 访问管理 | 访问管理 | 访问管理 |
| 其他 | 其他 | 其他 | 其他 |
| ***集成*** | ***集成度*** | ***集成度*** | ***集成度*** |
| 消息传送 | 消息传递 | 消息传递 | 消息传递 |
| 其他 | 其他 | 其他 | 其他 |
| ***物联网*** | ***IoT*** | ***物联网-azure-应用*** | ***物联网*** |
| IoT 核心服务 | 不适用 | iot-核心-服务 | iot-核心-服务 |
| IoT Edge 模块 | 不适用 | iot edge 模块 | iot edge 模块 |
| IoT 解决方案 | iot-解决方案 | iot-解决方案 | iot-解决方案 |
| 数据分析 & 可视化 | 数据分析和可视化 | 数据分析和可视化 | 数据分析和可视化 |
| IoT 连接 | iot-连接 | iot-连接 | iot-连接 |
| 其他 | 其他-物联网 | 其他-物联网 | 其他 |
| ***IT & 管理工具*** | ***ITandAdministration*** | ***it 和管理-工具-azure-应用*** | ***it 和管理工具*** |
| 管理解决方案 | 管理-解决方案 | 管理-解决方案 | 管理-解决方案 |
| 业务应用程序 | businessApplication | businessApplication | 业务应用程序 |
| 其他 | 其他 it 管理工具 | 其他 it 管理工具 | 其他 |
| ***监视 & 诊断*** | ***监视和诊断*** | ***监视和诊断*** | ***监视和诊断*** |
| 其他 | 其他 | 其他 | 其他 |
| ***介质*** | ***media*** | ***media*** | ***media*** |
| 媒体服务 | media-services | media-services | media-services |
| 内容保护 | content-protection | content-protection | content-protection |
| 实时 & 点播流式处理 | 实时和按需流式处理 | 实时和按需流式处理 | 实时和按需流式处理 |
| 其他 | 其他 | 其他 | 其他 |
| ***迁移*** | ***迁移*** | ***迁移*** | ***迁移*** |
| 数据迁移 | 数据迁移 | 数据迁移 | 数据迁移 |
| 其他 | 其他 | 其他 | 其他 |
| ***混合现实*** | ***mixed-reality*** | ***mixed-reality*** | ***mixed-reality*** |
| 其他 | 其他 | 其他 | 其他 |
| ***网络*** | ***上网*** | ***上网*** | ***上网*** |
| 设备管理器 | 设备管理器 | 设备管理器 | 设备管理器 |
| 连接性 | 连接 | 连接 | 连接 |
| 防火墙 | 防火墙 | 防火墙 | 防火墙 |
| 负载均衡器 | 负载平衡器 | 负载平衡器 | 负载平衡器 |
| 其他 | 其他 | 其他 | 其他 |
| ***安全性*** | ***安全*** | ***安全*** | ***安全*** |
| 标识和访问管理 | 标识和访问管理 | 标识和访问管理 | 标识和访问管理 |
| 威胁防护 | 威胁保护 | 威胁保护 | 威胁保护 |
| 信息保护 | 信息保护 | 信息保护 | 信息保护 |
| 其他 | 其他 | 其他 | 其他 |
| ***存储*** | ***存储-saas*** | ***存储-azure-应用*** | ***storage*** |
| 备份 & 恢复 | 备份 (backup) | 备份 (backup) | 备份和恢复 |
| 企业混合存储 | 企业混合存储 | 企业混合存储 | 企业混合存储 |
| 文件共享 | 文件共享 | 文件共享 | 文件共享 |
| 数据生命周期管理 | 数据生命周期-管理 | 数据生命周期-管理 | 数据生命周期-管理 |
| 其他 | 其他-存储 | 其他-存储 | 其他 |
| ***Web*** | ***web*** | ***web*** | ***web*** |
| Cms & 博客 | 博客和-cms | 博客和-cms | 博客和-cms |
| 入门 Web 应用 | 初学者-web 应用 | 初学者-web 应用 | 初学者-web 应用 |
| 电子 | 电子 | 电子 | 电子 |
| Web 应用框架 | web 应用-框架 | web 应用-框架 | web 应用-框架 |
| Web 应用 | web 应用 | web 应用 | web 应用 |
| 其他 | 其他 | 其他 | 其他 |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource 类别

这些类别及其各自的密钥适用于 SaaS、PowerBI 应用、Dynamics 365 business central、Dynamics 365 for customer engagement 和 Dynamics 365 for operation 产品/服务类型。 以粗体倾斜 (例如 ***分析***) 中的项是类别和标准文本项 (如高级分析) 在其下的子类别。 使用正确的键值，而不更改间距或大小写。

| 类别 | SaaS 密钥 | Dynamics 365 business central，Dynamics 365 for customer engagement，Dynamics 365 for operation keys | PowerBI 应用密钥 |
| --- | --- | --- | --- |
| ***分析*** | ***学*** | ***分析*** | ***分析*** |
| 高级分析 | 高级-分析 | 高级-分析 | 高级-分析 |
| 可视化 & 报告 | 可视化效果-报告 | 可视化效果-报告 | 可视化效果-报告 |
| 其他 | 其他 | 其他-分析 | 其他-分析 |
| ***AI + 机器学习*** | ***ArtificialIntelligence*** | ***ai-加上机器学习-动态*** | ***ai-加法-appsource*** |
| AI for Business | ai-适用于企业 | ai-适用于企业 | ai-适用于企业 |
| 机器人应用 | 机器人-应用 | 机器人-应用 | 机器人-应用 |
| 其他 | 其他-ai-+ 机器学习 | 其他-ai-+ 机器学习 | 其他-ai-+ 机器学习 |
| ***协作*** | ***协作*** | ***协作*** | ***协作*** |
| 联系 & 人员 | 联系人-人员 | 联系人-人员 | 联系人和人员 |
| 会议管理 | 会议管理 | 会议管理 | 会议管理 |
| 站点设计 & 管理 | 站点-设计-管理 | 站点-设计-管理 | 站点-设计和管理 |
| 任务 & 项目管理 | 任务-项目-管理 | 任务-项目-管理 | 任务和项目管理 |
| 语音 & 视频会议 | 语音视频-会议 | 语音视频-会议 | 语音和视频会议 |
| 其他 | 其他-协作 | 其他-协作 | 其他 |
| ***符合性 & 法律*** | ***遵从*** | ***遵从*** | ***符合性和合法*** |
| 税 & 审核 | 税务审核 | 税务审核 | 税务审核 |
| Legal | Legal | Legal | 法律 |
| 数据，调控 & 隐私 | 数据管理-隐私 | 数据管理-隐私 | 数据管理和隐私 |
| 运行状况 & 安全 | 健康-安全 | 健康-安全 | 健康和安全 |
| 其他 | 其他-符合性-合法 | 其他-符合性-合法 | 其他 |
| ***Customer Service*** | ***CustomerService*** | ***CustomerService*** | ***客户-服务*** |
| 联系人中心 | 联系人中心 | 联系人中心 | 联系人中心 |
| 面对面服务 | 面对面服务 | 面对面服务 | 面对面服务 |
| 后勤办公室 & 员工服务 | 后勤-员工-服务 | 后勤-员工-服务 | 后端-与员工服务 |
| 知识 & 案例管理 | 知识案例管理 | 知识案例管理 | 知识和案例管理 |
| 社交媒体 & 对全渠道 Engagement | 社交-对全渠道 | 社交-对全渠道 | 社交--对全渠道 |
| 其他 | 其他-客户-服务 | 其他-客户-服务 | 其他 |
| ***金融*** | ***金融*** | ***金融*** | ***额*** |
| 计帐 | 会计 | 会计 | 会计 |
| 资产管理 | 资产管理 | 资产管理 | 资产管理 |
| 分析、整合 & 报告 | 分析-合并-报告 | 分析-合并-报告 | 分析-合并和报告 |
| 信用 & 集合 | 信用-集合 | 信用-集合 | 信用和集合 |
| 合规性 & 风险管理 | 合规性-风险管理 | 合规性-风险管理 | 合规性和风险管理 |
| 其他 | 其他财务 | 其他财务 | 其他 |
| ***Human Resources*** | ***HumanResources*** | ***HumanResources*** | ***人力资源*** |
| 人才购置 | 人才购置 | 人才购置 | 人才购置 |
| 人才管理 | 人才管理 | 人才管理 | 人才管理 |
| HR 操作 | hr-操作 | hr-操作 | hr-操作 |
| 员工计划 & 分析 | 员工-规划-分析 | 员工-规划-分析 | 员工-规划-分析 |
| 其他 | 其他人资源 | 其他人资源 | 其他 |
| ***物联网*** | ***IoT*** | ***物联网-动态*** | ***物联网-appsource*** |
| 资产管理 & 操作 | 资产管理-操作 | 资产管理-操作 | 资产管理-操作 |
| 连接的产品 | 已连接-产品 | 已连接-产品 | 已连接-产品 |
| 智能供应链 | 智能提供链 | 智能提供链 | 智能提供链 |
| 预测性维护 | 预测-维护 | 预测-维护 | 预测-维护 |
| 远程监视 | 远程监视 | 远程监视 | 远程监视 |
| 安全 & 安全性 | 安全性-安全性 | 安全性-安全性 | 安全和安全 |
| 智能基础结构 & 资源 | 智能基础结构-资源 | 智能基础结构-资源 | 智能基础结构和资源 |
| 车辆 & 移动性 | 车辆-移动性 | 车辆-移动性 | 车辆和移动性 |
| 其他 | 其他-物联网 | 其他-物联网 | 其他 |
| ***IT & 管理工具*** | ***ITandAdministration*** | ***ITandAdministration*** | ***it 和管理工具*** |
| 管理解决方案 | 管理-解决方案 | 管理-解决方案 | 管理-解决方案 |
| 业务应用程序 | businessApplication | businessApplication | 业务应用程序 |
| 其他 | 其他 it 管理工具 | 其他 it 管理工具 | 其他 |
| ***营销部*** | ***营销部*** | ***营销部*** | ***行销*** |
| 播发 | 播发 | 播发 | 播发 |
| Analytics | 分析-营销 | 分析-营销 | 分析-营销 |
| 市场活动管理 & 自动化 | 市场活动-管理-自动化 | 市场活动-管理-自动化 | 市场活动-管理和自动化 |
| 电子邮件营销 | 电子邮件-营销 | 电子邮件-营销 | 电子邮件-营销 |
| L2-事件 & 资源管理 | 事件-资源管理 | 事件-资源管理 | 事件和资源管理 |
| 研究 & 分析 | 研究-分析 | 研究-分析 | 研究与分析 |
| 社交媒体 | 社交媒体 | 社交媒体 | 社交媒体 |
| 其他 | 其他-营销 | 其他-营销 | 其他 |
| ***操作 & 提供链*** | ***OperationsSupplyChain*** | ***OperationsSupplyChain*** | ***操作和提供链*** |
| 资产 & 生产管理 | 资产-生产-管理 | 资产-生产-管理 | 资产和生产-管理 |
| 需求预测 | 需求预测 | 需求预测 | 需求预测 |
| 信息管理 & 连接 | 信息管理-连接 | 信息管理-连接 | 信息管理和连接 |
| 规划、采购 & 报告 | 规划-购买-报告 | 规划-购买-报告 | 规划-采购和报告 |
| 优质 & 服务管理 | 质量-服务管理 | 质量-服务管理 | 质量和服务管理 |
| 销售 & 订单管理 | 销售订单-管理 | 销售订单-管理 | 销售和订购管理 |
| 运输 & 仓库管理 | 运输-仓库管理 | 运输-仓库管理 | 运输和仓库管理 |
| 其他 | 其他操作-提供链 | 其他操作-提供链 | 其他 |
| 高效工作 | 高效工作 | 高效工作 | ***提高*** |
| 内容创建 & 管理 | 内容创建-管理 | 内容创建-管理 | 内容-创建和管理 |
| 语言 & 翻译 | 语言翻译 | 语言翻译 | 语言和转换 |
| 文档管理 | 文档-管理 | 文档-管理 | 文档-管理 |
| 电子邮件管理 | 电子邮件管理 | 电子邮件管理 | 电子邮件管理 |
| 搜索 & 引用 | 搜索-引用 | 搜索-引用 | 搜索和引用 |
| 其他 | 其他-生产力 | 其他-生产力 | 其他 |
| 游戏化 | 游戏化 | 游戏化 | 游戏化 |
| ***Sales*** | ***Sales*** | ***Sales*** | ***Sales*** |
| 销售 | 销售 | 销售 | 销售 |
| 配置、定价、报价 (CPQ)  | 配置-价格-报价 | 配置-价格-报价 | 配置-价格-报价 |
| 合同管理 | 合同管理 | 合同管理 | 合同管理 |
| CRM | crm | crm | crm |
| 电子商务 | 电子商务 | 电子商务 | 电子商务 |
| 业务数据扩充 | 业务数据-扩充 | 业务数据-扩充 | 业务数据-扩充 |
| 销售支持 | 销售-支持 | 销售-支持 | 销售-支持 |
| 其他 | 其他-销售 | 其他-销售 | 其他-销售 |
| ***地理位置*** | ***地理*** | ***地理*** | ***地理*** |
| 地图 | maps | maps | maps |
| 新闻 & 天气 | 新闻和天气 | 新闻和天气 | 新闻和天气 |
| 其他 | 其他-地理位置 | 其他-地理位置 | 其他-地理位置 |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource 行业

这些行业及其各自的密钥适用于 SaaS、PowerBI 应用、Dynamics 365 business central、Dynamics 365 for customer engagement 和 Dynamics 365 for operation 产品/服务类型。 以粗体倾斜 (如 ***汽车***) 中的项是类别和标准文本项 (如 AutomotiveL2) 是它们下面的子类别。 使用正确的键值，而不更改间距或大小写。

| 行业 | SaaS，Dynamics 365 business central，Dynamics 365 for customer engagement，操作密钥的 dynamics 365 | PowerBI 应用密钥 |
| --- | --- | --- |
| ***汽车*** | ***汽车*** | ***汽车*** |
| 汽车 | AutomotiveL2 | AutomotiveL2 |
| ***农业*** | ***农业*** | ***农业*** |
| 其他-Unsegmented | 农业 \_ OtherUnsegmented | 其他-unsegmented |
| ***Distribution*** | ***Distribution*** | ***分布*** |
| 家 | 家 | 家 |
| 包裹 & 包裹寄送 | ParcelAndPackageShipping | 包裹包和包裹寄送 |
| ***教育水平*** | ***教育水平*** | ***接受*** |
| 高等教育 | HigherEducation | 高等教育 |
| 主要 & 辅助教育/K-12 | PrimaryAndSecondaryEducationK12 | 主要教育和辅助教育 |
| 博物馆 & 库 | LibrariesAndMuseums | 库-和-博物馆 |
| ***金融服务*** | ***FinancialServices*** | ***金融服务*** |
| 银行 & 资本市场 | BankingAndCapitalMarkets | 银行和资本市场 |
| 保险 | 保险 | 保险 |
| 政府 | 政府 | ***政府*** |
| 防御 & 情报 | DefenseAndIntelligence | 防御和智能 |
| 公共安全 & 司法 | PublicSafetyAndJustice | 公共-安全 |
| 民用政府 | CivilianGovernment | 民用-政府 |
| ***医疗保健*** | ***HealthCareandLifeSciences*** | ***卫生*** |
| 运行状况 Payor | HealthPayor | 运行状况-payor |
| 运行状况提供程序 | HealthProvider | 运行状况-提供程序 |
| 医药业 | 医药业 | 药品 |
| ***制造 & 资源*** | ***制造*** | ***制造和资源*** |
| 化学 & Agrochemical | ChemicalAndAgrochemical | 化学 agrochemical |
| 离散制造业 | DiscreteManufacturing | 离散-制造 |
| 能源 | 能源 | energy |
| ***零售 & 消费品*** | ***RetailandConsumerGoods*** | ***零售和消费品*** |
| 消费品 | ConsumerGoods | 使用者-货物 |
| 经销商 | 经销商 | 经销商 |
| ***媒体 & 通信*** | ***MediaAndCommunications*** | ***媒体和通信*** |
| 媒体 & 娱乐 | MediaandEntertainment | 媒体和娱乐 |
| 电信 | 电信 | 通讯 |
| ***专业服务*** | ***ProfessionalServices*** | ***专业-服务*** |
| Legal | Legal | 法律 |
| 合作伙伴专业服务 | PartnerProfessionalServices | 合作伙伴-专业-服务 |
| ***结构 & 构造*** | ***ArchitectureAndConstruction*** | ***体系结构和构造*** |
| 其他-Unsegmented | ArchitectureAndConstruction \_ OtherUnsegmented | 其他-unsegmented |
| ***宾馆 & 旅行*** | ***HospitalityandTravel*** | ***酒店和旅行*** |
|    宾馆 & 休闲 | HotelsAndLeisure | 饭店和休闲 |
| 旅游和运输业 | TravelAndTransportation | 旅行和运输 |
| 餐厅 & 食物服务 | RestaurantsAndFoodServices | 餐厅和食物服务 |
| ***其他公共部门行业*** | ***OtherPublicSectorIndustries*** | ***其他公共扇区业*** |
| 林 & 钓鱼 | ForestryAndFishing | 林 |
| 非营利组织 | 非营利组织 | 非营利组织 |
| ***房地产*** | ***RealEstate*** | ***房地产*** |
| 其他-Unsegmented | RealEstate \_ OtherUnsegmented | 其他-unsegmented |
|||
