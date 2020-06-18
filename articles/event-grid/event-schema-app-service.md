---
title: 将 Azure 应用服务用作事件网格源
description: 本文介绍了如何将 Azure 应用服务用作事件网格事件源。 其中提供了架构，以及教程和操作指南文章的链接。
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 0a24e8ba84739dbc1b5de5e0546a8fe0d2e826f1
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650707"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>将 Azure 应用服务用作事件网格源

本文提供了 Azure 应用服务事件的属性和架构。 有关事件架构的简介，请参阅 [Azure 事件网格事件架构](event-schema.md)。 本文还提供了有关将 Azure 应用服务用作事件源的快速入门和教程列表。

## <a name="event-grid-event-schema"></a>事件网格事件架构

### <a name="available-event-types"></a>可用事件类型

Azure 应用服务发出以下事件类型

|    事件类型                                             |    说明                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    备份开始时触发                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    备份完成时触发                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    备份失败时触发                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    从备份还原开始时触发        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    从备份还原完成时触发      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    从备份还原失败时触发         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    槽交换开始时触发                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    槽交换完成时触发                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    槽交换失败时触发                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    启动带预览的槽交换时触发           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    取消带预览的槽交换时触发    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    站点重启时触发                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    站点停止时触发                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    站点的应用设置更改时触发             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    应用服务计划更新时触发                 |

### <a name="the-contents-of-an-event-response"></a>事件响应的内容

触发某个事件后，事件网格服务会将有关该事件的数据发送到订阅终结点。
本部分包含每个事件的数据外观示例。 每个事件具有以下顶级数据：

|     properties          |     类型     |     说明                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    主题              |    字符串    |    事件源的完整资源路径。 此字段不可写入。 事件网格提供此值。                                      |
|    subject            |    字符串    |    事件主题的发布者定义路径。                                                                                              |
|    eventType          |    字符串    |    此事件源的已注册事件类型之一。                                                                                  |
|    EventTime          |    字符串    |    事件的生成时间，基于提供程序的 UTC 时间。                                                                         |
|    id                 |    字符串    |    事件的唯一标识符。                                                                                                            |
|    data               |    对象 (object)    |    Blob 存储事件数据。                                                                                                                    |
|    dataVersion        |    字符串    |    数据对象的架构版本。 发布者定义架构版本。                                                          |
|    metadataVersion    |    字符串    |    事件元数据的架构版本。 事件网格定义顶级属性的架构。 事件网格提供此值。    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted、BackupOperationCompleted、BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

数据对象包含以下属性：

|    properties                |    类型      |    说明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    对象 (object)    |    对应用执行的操作的详细信息                                                                                       |
|    action                  |    字符串    |    操作的操作类型                                                                                   |
|    name                    |    字符串    |    发生此事件的网站的名称                                                                          |
|    ClientRequestId         |    字符串    |    应用服务为触发此事件的站点 API 操作生成的客户端请求 ID         |
|    correlationRequestId    |    字符串    |    应用服务为触发此事件的站点 API 操作生成的关联请求 ID    |
|    requestId               |    字符串    |    应用服务为触发此事件的站点 API 操作生成的请求 ID                |
|    address                 |    字符串    |    此操作的 HTTP 请求 URL                                                                                |
|    谓词                    |    字符串    |    此操作的 HTTP 谓词                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted、RestoreOperationCompleted、RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

数据对象包含以下属性：

|    properties                |    类型      |    说明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    对象 (object)    |    对应用执行的操作的详细信息                                                                                       |
|    action                  |    字符串    |    操作的操作类型                                                                                   |
|    name                    |    字符串    |    发生此事件的网站的名称                                                                          |
|    ClientRequestId         |    字符串    |    应用服务为触发此事件的站点 API 操作生成的客户端请求 ID         |
|    correlationRequestId    |    字符串    |    应用服务为触发此事件的站点 API 操作生成的关联请求 ID    |
|    requestId               |    字符串    |    应用服务为触发此事件的站点 API 操作生成的请求 ID                |
|    address                 |    字符串    |    此操作的 HTTP 请求 URL                                                                                |
|    谓词                    |    字符串    |    此操作的 HTTP 谓词                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted、SlotSwapCompleted、SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

数据对象包含以下属性：

|    properties                |    类型      |    说明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    对象 (object)    |    对应用执行的操作的详细信息                                                                                       |
|    action                  |    字符串    |    操作的操作类型                                                                                   |
|    name                    |    字符串    |    发生此事件的网站的名称                                                                          |
|    ClientRequestId         |    字符串    |    应用服务为触发此事件的站点 API 操作生成的客户端请求 ID         |
|    correlationRequestId    |    字符串    |    应用服务为触发此事件的站点 API 操作生成的关联请求 ID    |
|    requestId               |    字符串    |    应用服务为触发此事件的站点 API 操作生成的请求 ID                |
|    address                 |    字符串    |    此操作的 HTTP 请求 URL                                                                                |
|    谓词                    |    字符串    |    此操作的 HTTP 谓词                                                                                       |
|    sourceSlot              |    字符串    |    用于交换的源槽                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted、SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

数据对象包含以下属性：

|    properties                |    类型      |    说明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    对象 (object)    |    对应用执行的操作的详细信息                                                                                       |
|    action                  |    字符串    |    操作的操作类型                                                                                   |
|    name                    |    字符串    |    发生此事件的网站的名称                                                                          |
|    ClientRequestId         |    字符串    |    应用服务为触发此事件的站点 API 操作生成的客户端请求 ID         |
|    correlationRequestId    |    字符串    |    应用服务为触发此事件的站点 API 操作生成的关联请求 ID    |
|    requestId               |    字符串    |    应用服务为触发此事件的站点 API 操作生成的请求 ID                |
|    address                 |    字符串    |    此操作的 HTTP 请求 URL                                                                                |
|    谓词                    |    字符串    |    此操作的 HTTP 谓词                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted、AppUpdated.Stopped、AppUpdated.ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

数据对象具有以下属性：

|    properties                |    类型      |    说明                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    对象 (object)    |    对应用执行的操作的详细信息                                                                                       |
|    action                  |    字符串    |    操作的操作类型                                                                                   |
|    name                    |    字符串    |    发生此事件的网站的名称                                                                          |
|    ClientRequestId         |    字符串    |    应用服务为触发此事件的站点 API 操作生成的客户端请求 ID         |
|    correlationRequestId    |    字符串    |    应用服务为触发此事件的站点 API 操作生成的关联请求 ID    |
|    requestId               |    字符串    |    应用服务为触发此事件的站点 API 操作生成的请求 ID                |
|    address                 |    字符串    |    此操作的 HTTP 请求 URL                                                                                |
|    谓词                    |    字符串    |    此操作的 HTTP 谓词                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

数据对象具有以下属性：

|    properties                         |    类型      |    说明                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    对象 (object)    |    对应用服务计划执行的操作的详细信息                                                                          |
|    stampKind                        |    字符串    |    应用服务计划所在环境的种类                                                                     |
|    action                           |    字符串    |    对应用服务计划执行的操作的类型                                                                            |
|    status                           |    字符串    |    对应用服务计划执行的操作的状态                                                                   |
|    sku                              |    对象 (object)    |    应用服务计划的 SKU                                                                                       |
|    name                             |    字符串    |    应用服务计划的名称                                                                                      |
|    层                             |    字符串    |    应用服务计划的层                                                                                      |
|    大小                             |    字符串    |    应用服务计划的大小                                                                                      |
|    系列                           |    字符串    |    应用服务计划的系列                                                                                        |
|    容量                         |    字符串    |    应用服务计划的容量                                                                                      |
|    action                           |    字符串    |    操作的操作类型                                                                                   |
|    name                             |    字符串    |    发生此事件的网站的名称                                                                          |
|    ClientRequestId                  |    字符串    |    应用服务为触发此事件的站点 API 操作生成的客户端请求 ID         |
|    correlationRequestId             |    字符串    |    应用服务为触发此事件的站点 API 操作生成的关联请求 ID    |
|    requestId                        |    字符串    |    应用服务为触发此事件的站点 API 操作生成的请求 ID                |
|    address                          |    字符串    |    此操作的 HTTP 请求 URL                                                                                |
|    谓词                             |    字符串    |    此操作的 HTTP 谓词                                                                                       |
