---
title: 通过对话学习器管理用户数据 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解如何使用对话学习器管理用户数据。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d42f903559a1e07b42ded33972be4b552f21b5e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366259"
---
# <a name="managing-user-data"></a>管理用户数据

本文介绍对话学习器云服务在与最终用户对话时所记录的内容。  同时，还介绍如何将对话学习器日志与用户 ID 相关联，便于检索或删除与特定用户相关的所有日志。

## <a name="overview-of-end-user-data-logging"></a>最终用户数据日志记录概述

默认情况下，对话学习器云服务会记录最终用户和机器人之间的交互内容。  这些日志对于改进机器人非常重要，可用于识别机器人提取错误实体或选择不正确操作的情况。  随后，可转到 UI 的“对话日志”页面更正这些错误，更正后，将正确的对话存储为新的训练对话。 有关详细信息，请参阅“对话日志”教程。

## <a name="how-to-disable-logging"></a>如何禁用日志记录

可控制是否在对话学习器应用程序的“设置”页面上显示与最终用户的对话。  有一个“记录对话”复选框。  取消选中此框即可不记录与最终用户的对话。

## <a name="what-is-logged"></a>记录的内容 

在“对话日志”中，对话学习器会存储每次对话的用户输入、实体值、所选操作和时间戳。  这些日志存储一段时间后即会被删除（有关详细信息，请参阅“默认值和界限”的帮助页面）。  

对话学习器会为记录的每个对话创建一个唯一的 ID。  对话学习器记录的对话中不会存储用户标识符。  

## <a name="associating-logged-dialogs-with-a-user-id"></a>将记录的对话与用户 ID 相关联

通常需要将记录的对话与用户 ID 相关联，这样便能检索或删除记录的特定用户的对话等。  由于对话学习器不存储用户标识符，因此需要由开发人员编写代码来实现此种关联。  

若要创建此映射，请在 `EntityDetectionCallback` 中获取记录的对话的 ID；然后在机器人存储中，存储用户 ID 和此对话间的关联。  

```
cl.EntityDetectionCallback(async (text: string, memoryManager: ClientMemoryManager): Promise<void> => {

    // Get user and session info
    var sessionData = memoryManager.SessionInfo();

    // sessionData.sessionId is the ID of this logged dialog.
    // In your bot-specific datastore, store an association
    // bewteen your user identifier and this session ID.
    console.log(sessionData.logDialogId)

    // sessionData.userId and sessionData.userName are the 
    // user ID and user name as defined by the channel the user
    // is on (eg, Skype, Teams, Facebook Messenger, etc.).
    // For some channels, this will be undefined.
    // This information is NOT stored with the logged dialog.
    console.log(sessionData.userId);
    console.log(sessionData.userName);

})
```

## <a name="headers-for-http-calls"></a>HTTP 调用的标头

在下方每个 HTTP 调用中，添加以下标头：

```
Ocp-Apim-Subscription-Key=<LUIS_AUTHORING_KEY>
```

其中 `<LUIS_AUTHORING_KEY>` 是用于访问对话学习器应用程序的 LUIS 创作密钥。

## <a name="how-to-obtain-raw-data-for-a-logged-dialog"></a>如何获取记录的对话的原始数据

若要获取记录的对话的原始数据，可使用此 HTTP 调用：

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

其中 `<appId>` 是此对话学习器应用程序的 GUID，`<logDialgoId>` 是要检索的对话的 ID。  

请注意，开发人员可编辑记录的对话，然后将其存储为训练对话。  完成后，对话学习器将“源”对话记录的 ID 存储到训练对话。  此外，训练对话可在 UI 中“存在分支”；如果训练对话与某个源对话记录 ID 关联，则会使用与该对话记录相同的 ID 标记该训练对话中的分支。

若要获取从记录的对话派生的所有训练对话，请按照下列步骤操作。

首先，检索所有训练对话：

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialogs
```

其中 `<appId>` 是此对话学习器应用程序的 GUID。  

此操作会返回所有训练对话。  在此列表中搜索关联的 `sourceLogDialogId`，并记下关联的 `trainDialogId`。 

通过 ID 转到某个训练对话：

```
GET https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

其中 `<appId>` 是此对话学习器应用程序的 GUID，`<trainDialogId>` 是要检索的训练对话的 ID。  

## <a name="how-to-delete-a-logged-dialog"></a>如何删除记录的对话

若要删除给定 ID 的对话记录，可使此 HTTP 调用：

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/logdialog/<logDialogId>
```

其中 `<appId>` 是此对话学习器应用程序的 GUID，`<logDialogId>` 是要删除的记录的对话的 ID。 

若要删除给定 ID 的训练对话，可使此 HTTP 调用：

```
DELETE https://westus.api.cognitive.microsoft.com/conversationlearner/v1.0/app/<appId>/traindialog/<trainDialogId>
```

其中 `<appId>` 是此对话学习器应用程序的 GUID，`<trainDialogId>` 是要删除的训练对话的 ID。 
