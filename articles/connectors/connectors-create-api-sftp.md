---
title: 了解如何在逻辑应用中使用 SFTP 连接器 | Microsoft Docs
description: 使用 Azure 应用服务创建逻辑应用。 连接到 SFTP API 以发送和接收文件。 可以执行各种操作，如创建、更新、获取或删除文件。
services: logic-apps
documentationcenter: .net,nodejs,java
author: ecfan
manager: jeconnoc
editor: ''
tags: connectors
ms.assetid: 697eb8b0-4a66-40c7-be7b-6aa6b131c7ad
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 07/20/2016
ms.author: estfan; ladocs
ms.openlocfilehash: 28ea02082903f71f619a52672ba41ce65557b0c7
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295996"
---
# <a name="get-started-with-the-sftp-connector"></a>SFTP 连接器入门
使用 SFTP 连接器访问 SFTP 帐户以发送和接收文件。 可以执行各种操作，如创建、更新、获取或删除文件。  

若要使用[任何连接器](apis-list.md)，首先需要创建逻辑应用。 可通过 [立即创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md) 开始操作。

## <a name="connect-to-sftp"></a>连接到 SFTP
在逻辑应用访问任何服务之前，必须先创建到该服务的*连接*。 [连接](connectors-overview.md)提供逻辑应用和其他服务之间的连接性。  

### <a name="create-a-connection-to-sftp"></a>创建到 SFTP 的连接
> [!INCLUDE [Steps to create a connection to SFTP](../../includes/connectors-create-api-sftp.md)]
> 
> 

## <a name="use-an-sftp-trigger"></a>使用 SFTP 触发器
触发器是用于启动在逻辑应用中定义的工作流的事件。 [了解有关触发器的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。  

在此示例中，“SFTP - 添加或修改文件时”触发器用于在 SFTP 服务器上添加或修改文件时启动逻辑应用工作流。 还可添加用于检查新的或已修改文件内容的条件，并在其内容指示应在使用该内容前提取文件时作出提取文件的决策。 最后，添加提取文件内容并将提取的内容放置在 SFTP 服务器上某个文件夹中的操作。 

在企业示例中，可以使用此触发器监视 SFTP 文件夹中表示客户订单的新文件。  然后可以使用 SFTP 连接器操作（如“获取文件内容”）获取订单内容进行进一步处理并存储在订单数据库中。

> [!INCLUDE [Steps to create an SFTP trigger](../../includes/connectors-create-api-sftp-trigger.md)]
> 
> 

## <a name="add-a-condition"></a>添加条件
> [!INCLUDE [Steps to add a condition](../../includes/connectors-create-api-sftp-condition.md)]
> 
> 

## <a name="use-an-sftp-action"></a>使用 SFTP 操作
操作是指在逻辑应用中定义的工作流所执行的操作。 [了解有关操作的详细信息](../logic-apps/logic-apps-overview.md#logic-app-concepts)。  

> [!INCLUDE [Steps to create an SFTP action](../../includes/connectors-create-api-sftp-action.md)]
> 
> 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

在[连接器详细信息](/connectors/sftpconnector/)中查看在 Swagger 中定义的触发器和操作，并查看限制。

## <a name="more-connectors"></a>更多连接器
返回到 [API 列表](apis-list.md)。