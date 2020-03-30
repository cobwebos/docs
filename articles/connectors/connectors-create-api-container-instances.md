---
title: 部署&管理 Azure 容器实例
description: 使用 Azure 逻辑应用自动执行在 Azure 容器实例中创建和管理容器部署的任务和工作流
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76046287"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用部署和管理 Azure 容器实例

使用 Azure 逻辑应用和 Azure 容器实例连接器，可以设置部署和管理[容器组的](../container-instances/container-instances-container-groups.md)自动任务和工作流。 容器实例连接器支持以下操作：

* 创建或删除容器组
* 获取容器组的属性
* 获取容器组的列表
* 获取容器实例的日志

在逻辑应用中对响应逻辑应用触发器运行容器工作负载等任务使用这些操作。 您还可以让其他操作使用容器实例操作中的输出。 

此连接器仅提供操作，因此要启动逻辑应用，请使用单独的触发器，例如**定期**触发器以按常规计划运行容器工作负载。 或者，您可能需要在 Outlook 电子邮件到达等事件后触发容器组部署。 

如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 有关如何[创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)以及如何[创建和管理容器实例](../container-instances/container-instances-quickstart.md)的基本知识

* 要访问容器实例的逻辑应用。 要使用操作，请使用另一个触发器启动逻辑应用，例如 **"重复**"触发器。

## <a name="add-a-container-instance-action"></a>添加容器实例操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 选择一个路径： 

   * 在要添加操作的最后一个步骤下，选择“新建步骤”。**** 

     -或-

   * 在要在其中添加操作的两个步骤之间，将鼠标指针移到步骤之间的箭头上。 
   选择显示的加号**+**（），然后选择 **"添加操作**"。

1. 在搜索框中，输入"容器实例"作为筛选器。 在操作列表下，选择所需的 Azure 容器实例连接器操作。

1. 提供连接的名称。 

1. 为所选操作提供必要的详细信息，并继续构建逻辑应用的工作流。

  例如，选择 **"创建容器组"** 并输入容器组和组中的一个或多个容器实例的属性，如下图所示（部分详细信息）：

  ![创建容器组](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制的技术详细信息（由连接器的 OpenAPI（以前的斯瓦格）描述，请查看连接器的[参考页](/connectors/aci/)或容器组[YAML 引用](../container-instances/container-instances-reference-yaml.md)。

## <a name="next-steps"></a>后续步骤

* 查看在 Azure 容器实例中运行容器[的示例逻辑应用](https://github.com/Azure-Samples/aci-logicapps-integration)，以分析电子邮件或 Twitter 文本的情绪

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)