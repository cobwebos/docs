---
title: include 文件
description: include 文件
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 400f12237ae8b8cbaf6d66bda1663ecb680136f3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162718"
---
[消息路由](../articles/iot-hub/iot-hub-devguide-messages-d2c.md)能够将遥测数据从 IoT 设备发送到内置的与事件中心兼容的终结点或自定义终结点，例如，Blob 存储、服务总线队列、服务总线主题和事件中心。 若要配置自定义消息路由，请创建[路由查询](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)来自定义与特定条件匹配的路由。 设置完成后，引入的数据将通过 IoT 中心自动路由到终结点。 如果某个消息不匹配定义的任何路由查询，它将路由到默认终结点。

本教程包括 2 个部分，介绍如何通过 IoT 中心设置和使用这些自定义路由查询。 将消息从 IoT 设备路由到多个终结点中的一个，包括 Blob 存储和服务总线队列。 路由到服务总线队列的消息将由逻辑应用拾取，并通过电子邮件发送。 未定义自定义消息路由的消息将发送到默认终结点，然后由 Azure 流分析拾取，可在 Power BI 中直观查看。

 若要完成本教程的第 1 和第 2 部分，请执行以下任务：

**第 I 部分：创建资源并设置消息路由**
> [!div class="checklist"]
> * 创建资源 - IoT 中心、存储帐户、服务总线队列和模拟设备。 可以使用门户、Azure CLI、Azure PowerShell 或 Azure 资源管理器模板完成此操作。
> * 在 IoT 中心为存储帐户和服务总线队列配置终结点和消息路由。

**第 II 部分：将消息发送到中心并查看路由的结果**
> [!div class="checklist"]
> * 创建一个逻辑应用，该应用将在消息添加到服务总线队列时触发，并发送电子邮件。
> * 下载并运行应用，该应用模拟 IoT 设备将消息发送到中心，以获得不同的路由选择。
> * 为发送至默认终结点的数据创建 Power BI 可视化。
> * 查看结果...
> * ...在服务总线队列和电子邮件中。
> * ...在存储帐户中。
> * ...在 Power BI 可视化中。

## <a name="prerequisites"></a>先决条件

* 对于本教程的第 1 部分：
  - 必须拥有 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 对于本教程的第 2 部分：
  - 必须事先完成本教程的第 1 部分，并保留一些可用的资源。
  - 安装 [Visual Studio](https://www.visualstudio.com/)。
  - 用于分析默认终结点的流分析的 Power BI 帐户。 （[免费试用 Power BI](https://app.powerbi.com/signupredirect?pbi_source=web)。）
  - 用于发送通知电子邮件的 Office 365 帐户。

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
