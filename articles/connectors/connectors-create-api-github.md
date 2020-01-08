---
title: 访问、监视和管理你的 GitHub 存储库
description: 使用 Azure 逻辑应用创建自动化工作流，监视 GitHub 事件并管理 GitHub 存储库
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378443"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用来监视和管理 GitHub 存储库

GitHub 是基于 Web 的 Git 存储库托管服务，它提供 Git 的所有分布式修订控制和源代码管理 (SCM) 功能以及其他功能。

若要开始使用 GitHub 连接器，[首先创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

## <a name="create-a-connection-to-github"></a>创建到 GitHub 的连接

若要在逻辑应用中使用 GitHub 连接器，必须先创建*连接*，然后提供以下属性的详细信息： 

| 属性 | 需要 | Description | 
| -------- | -------- | ----------- | 
| 令牌 | 是 | 提供 GitHub 凭据。 |

创建连接后，可执行操作并侦听触发器，如本文所述。

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>连接器参考

有关由连接器的 OpenAPI （以前为 Swagger）说明描述的触发器、操作和限制的技术详细信息，请查看[连接器的参考页](/connectors/github/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)