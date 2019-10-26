---
title: 使用 Azure IoT Central 中的应用程序模板 |Microsoft Docs
description: 如何以操作员的身份在 Azure IoT Central 应用程序中使用设备集。
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: d682c49aa833b9e11dbbddc5e9f6afd52cbb6e84
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952870"
---
# <a name="use-application-templates"></a>使用应用程序模板

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

本文介绍如何使用解决方案管理器创建和使用应用程序模板。

创建 Azure IoT Central 应用程序时，可以选择内置的示例模板。 你还可以从现有的 IoT Central 应用程序创建自己的应用程序模板。 然后，你可以在创建新应用程序时使用自己的应用程序模板。

当你创建应用程序模板时，它将包括你的现有应用程序中的以下项：

- 默认应用程序仪表板，包括仪表板布局和你定义的所有磁贴。
- 设备模板，包括度量、设置、属性、命令和仪表板。
- 原则. 包括所有规则定义。 但不包括电子邮件操作以外的操作。
- 设备集，包括其条件和仪表板。

> [!WARNING]
> 如果仪表板包括显示特定设备相关信息的磁贴，则那些磁贴会显示在新应用程序中**找不到请求的资源**。 您必须重新配置这些磁贴，以显示有关新应用程序中设备的信息。

当你创建应用程序模板时，它不包含以下项：

- 设备
- 用户
- 作业定义
- 连续数据导出定义

请手动将这些项添加到从应用程序模板创建的任何应用程序。

## <a name="create-an-application-template"></a>创建应用程序模板

从现有 IoT Central 应用程序创建应用程序模板：

1. 请在应用程序中中转到 "**管理**" 部分。
1. 选择**应用程序模板导出**。
1. 在 "**应用程序模板导出**" 页上，输入模板的名称和描述。
1. 选择 "**导出**" 按钮，创建应用程序模板。 你现在可以复制可**共享的链接**，使某人可以从模板创建新应用程序：

![创建应用程序模板](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>使用应用程序模板

若要使用应用程序模板创建新的 IoT Central 应用程序，你需要一个以前创建的可**共享的链接**。 将可**共享的链接**粘贴到浏览器的地址栏中。 将显示 "**创建应用程序**" 页，其中选择了自定义应用程序模板：

![从模板创建应用程序](media/howto-use-app-templates/create-app.png)

选择付款计划，并填写窗体上的其他字段。 然后从 "应用程序" 模板中选择 "**创建**" 来创建新的 IoT Central 应用程序。

## <a name="manage-application-templates"></a>管理应用程序模板

在 "**应用程序模板导出**" 页上，可以删除或更新应用程序模板。

如果删除应用程序模板，则不能再使用以前生成的可共享的链接来创建新的应用程序。

若要更新应用程序模板，请在**应用程序模板导出**页上更改模板名称或说明。 然后再次选择 "**导出**" 按钮。 此操作将生成一个新的可**共享链接**，并使任何以前可**共享的链接**URL 失效。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用应用程序模板，接下来的建议是了解如何[从 Azure 门户管理 IoT Central](howto-manage-iot-central-from-portal.md)
