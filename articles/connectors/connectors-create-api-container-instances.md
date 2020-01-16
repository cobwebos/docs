---
title: 部署 & 管理 Azure 容器实例
description: 使用 Azure 逻辑应用自动创建和管理 Azure 容器实例中的容器部署的任务和工作流
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
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2020
ms.locfileid: "76046287"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>使用 Azure 逻辑应用部署和管理 Azure 容器实例

通过 Azure 逻辑应用和 Azure 容器实例连接器，你可以设置自动化任务和工作流来部署和管理[容器组](../container-instances/container-instances-container-groups.md)。 容器实例连接器支持以下操作：

* 创建或删除容器组
* 获取容器组的属性
* 获取容器组的列表
* 获取容器实例的日志

在逻辑应用中使用这些操作来执行任务，例如运行容器工作负荷，以响应逻辑应用触发器。 还可以让其他操作使用容器实例操作的输出。 

此连接器只提供操作，因此，若要启动逻辑应用，请使用单独的触发器，如**定期**触发器按定期运行容器工作负荷。 或者，可能需要在事件（例如 Outlook 电子邮件到达）之后触发容器组部署。 

如果你不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。 

* 基本了解[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)，以及[如何创建和管理容器实例](../container-instances/container-instances-quickstart.md)

* 要在其中访问容器实例的逻辑应用。 若要使用操作，请使用另一个触发器启动逻辑应用，例如，**重复**执行触发器。

## <a name="add-a-container-instance-action"></a>添加容器实例操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 选择一个路径： 

   * 在要添加操作的最后一个步骤下，选择“新建步骤”。 

     -或-

   * 在要在其中添加操作的两个步骤之间，将鼠标指针移到步骤之间的箭头上。 
   选择出现的加号 ( **+** )，然后选择“添加操作”。

1. 在搜索框中，输入 "容器实例" 作为筛选器。 在 "操作" 列表下，选择所需的 Azure 容器实例连接器操作。

1. 提供连接的名称。 

1. 为所选操作提供必要的详细信息，并继续构建逻辑应用的工作流。

  例如，选择 "**创建容器组**"，并在组中输入容器组和一个或多个容器实例的属性，如下图所示（部分详细信息）：

  ![创建容器组](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>连接器参考

有关由连接器的 OpenAPI （以前的 Swagger）说明描述的触发器、操作和限制的技术详细信息，请查看连接器的[参考页](/connectors/aci/)或容器组[YAML 引用](../container-instances/container-instances-reference-yaml.md)。

## <a name="next-steps"></a>后续步骤

* 请参阅在 Azure 容器实例中运行容器的[示例逻辑应用](https://github.com/Azure-Samples/aci-logicapps-integration)，用于分析电子邮件或 Twitter 文本的情绪

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)