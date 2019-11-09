---
title: 导出 Azure IoT Central 应用程序 |Microsoft Docs
description: 作为解决方案经理，我想要导出应用程序模板以便能够重用它。
author: dominicbetts
ms.author: dobett
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 81e24712de186e2d5ec03e33ecabe485cc0d9f31
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73894755"
---
# <a name="export-your-application-preview-features"></a>导出应用程序（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

本文介绍如何使用解决方案管理器导出 IoT Central 的应用程序，使其能够重复使用。

可以使用两个选项：

- 如果只需创建应用程序的副本，则可以创建应用程序的副本。
- 如果计划创建多个副本，则可以从你的应用程序创建应用程序模板。

## <a name="copy-your-application"></a>复制应用程序

可以创建任一应用程序的副本，但其中不会包括任何设备实例、设备数据历史记录和用户数据。 该副本是将向你收费的即用即付应用程序。 不能通过复制应用程序来创建试用应用程序。

选择 "**复制**"。 在对话框中，输入新的即用即付应用程序的详细信息。 然后选择 "**复制**"，确认是否要继续。 若要了解有关窗体中的字段的详细信息，请参阅[创建应用程序](quick-deploy-iot-central.md)快速入门。

![“应用程序设置”页](media/howto-use-app-templates/appcopy2.png)

应用复制操作成功后，可以使用链接导航到新应用程序。

![“应用程序设置”页](media/howto-use-app-templates/appcopy3a.png)

复制应用程序还会复制规则和电子邮件操作的定义。 某些操作（例如 Flow 和逻辑应用）通过规则 ID 与特定规则关联。 将规则复制到其他应用程序时，它将获取其自己的规则 ID。 在这种情况下，用户必须创建一个新的操作，然后将新规则与其关联。 通常情况下，最好检查规则和操作，以确保它们在新应用中处于最新状态。

> [!WARNING]
> 如果仪表板包括显示特定设备相关信息的磁贴，则那些磁贴会显示在新应用程序中**找不到请求的资源**。 您必须重新配置这些磁贴，以显示有关新应用程序中设备的信息。

## <a name="create-an-application-template"></a>创建应用程序模板

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

从现有 IoT Central 应用程序创建应用程序模板：

1. 请在应用程序中中转到 "**管理**" 部分。
1. 选择**应用程序模板导出**。
1. 在 "**应用程序模板导出**" 页上，输入模板的名称和描述。
1. 选择 "**导出**" 按钮，创建应用程序模板。 你现在可以复制可**共享的链接**，使某人可以从模板创建新应用程序：

![创建应用程序模板](media/howto-use-app-templates/create-template.png)

### <a name="use-an-application-template"></a>使用应用程序模板

若要使用应用程序模板创建新的 IoT Central 应用程序，你需要一个以前创建的可**共享的链接**。 将可**共享的链接**粘贴到浏览器的地址栏中。 将显示 "**创建应用程序**" 页，其中选择了自定义应用程序模板：

![从模板创建应用程序](media/howto-use-app-templates/create-app.png)

选择付款计划，并填写窗体上的其他字段。 然后从 "应用程序" 模板中选择 "**创建**" 来创建新的 IoT Central 应用程序。

### <a name="manage-application-templates"></a>管理应用程序模板

在 "**应用程序模板导出**" 页上，可以删除或更新应用程序模板。

如果删除应用程序模板，则不能再使用以前生成的可共享的链接来创建新的应用程序。

若要更新应用程序模板，请在**应用程序模板导出**页上更改模板名称或说明。 然后再次选择 "**导出**" 按钮。 此操作将生成一个新的可**共享链接**，并使任何以前可**共享的链接**URL 失效。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何使用应用程序模板，接下来的建议是了解如何[从 Azure 门户管理 IoT Central](../core/howto-manage-iot-central-from-portal.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)
