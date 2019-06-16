---
title: 连接到 GitHub - Azure 逻辑应用 | Microsoft Docs
description: 使用 GitHub REST API 和 Azure 逻辑应用监视 GitHub 事件
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 03/02/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 0d2ff9368bc244a5afd6fafc40cf476b90a80a52
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61462515"
---
# <a name="connect-to-github"></a>连接到 GitHub

GitHub 是基于 Web 的 Git 存储库托管服务，它提供 Git 的所有分布式修订控制和源代码管理 (SCM) 功能以及其他功能。

若要开始使用 GitHub 连接器，[首先创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-github"></a>创建到 GitHub 的连接

若要在逻辑应用中使用 GitHub 连接器，必须先创建*连接*，然后提供以下属性的详细信息： 

| 属性 | 需要 | 描述 | 
| -------- | -------- | ----------- | 
| 令牌 | 是 | 提供 GitHub 凭据。 |

创建连接后，可执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

有关 Swagger 中定义的触发器和操作以及任何限制，请查看[连接器详细信息](/connectors/github/)。

## <a name="find-more-connectors"></a>查找更多连接器

* 查看[连接器列表](apis-list.md)。