---
title: 连接到 GitHub
description: 使用 GitHub REST API 和 Azure 逻辑应用监视 GitHub 事件
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: 7dc865f520b6f4667ace720e656a210e0252d1a1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789756"
---
# <a name="connect-to-github-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到 GitHub

GitHub 是基于 Web 的 Git 存储库托管服务，它提供 Git 的所有分布式修订控制和源代码管理 (SCM) 功能以及其他功能。

若要开始使用 GitHub 连接器，[首先创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-github"></a>创建到 GitHub 的连接

若要在逻辑应用中使用 GitHub 连接器，必须先创建*连接*，然后提供以下属性的详细信息： 

| properties | 需要 | 描述 | 
| -------- | -------- | ----------- | 
| 令牌 | 是 | 提供 GitHub 凭据。 |

创建连接后，可执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>连接器参考

有关由连接器的 OpenAPI （以前为 Swagger）说明描述的触发器、操作和限制的技术详细信息，请查看[连接器的参考页](/connectors/github/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)