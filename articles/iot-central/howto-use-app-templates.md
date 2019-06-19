---
title: 在 Azure IoT Central 中使用应用程序模板 |Microsoft Docs
description: 如何以操作员的身份在 Azure IoT Central 应用程序中使用设备集。
author: dominicbetts
ms.author: dobett
ms.date: 05/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: a26f70c5a61f3855a3de991072a7e84103e87b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66499214"
---
# <a name="use-application-templates"></a>使用应用程序模板

本指南介绍了如何为解决方案管理器，创建和使用应用程序模板。

创建 Azure IoT Central 应用程序时，可以选择的内置示例模板。 此外可以从现有的 IoT Central 应用程序创建您自己的应用程序模板。 然后，创建新的应用程序时，可以使用自己的应用程序模板。

创建应用程序模板时，它包括现有的应用程序中的以下项目：

- 默认应用程序仪表板中，包括仪表板布局和已定义的所有磁贴。
- 设备模板，包括度量值、 设置、 属性、 命令和仪表板。
- 规则。 包含所有规则定义都。 但是，除了电子邮件操作的操作不包括在内。
- 设备设置，包括其条件和仪表板。

> [!WARNING]
> 如果仪表板包含显示有关特定设备的信息的磁贴，则这些磁贴显示**找不到请求的资源**在新的应用程序。 必须重新配置这些磁贴以在新的应用程序中显示有关设备的信息。

创建应用程序模板时，它不包括以下各项：

- 设备
- 用户
- 作业定义
- 连续数据导出定义

手动将这些项添加到应用程序模板创建的任何应用程序。

## <a name="create-an-application-template"></a>创建应用程序模板

若要从现有的 IoT Central 应用程序创建应用程序模板：

1. 转到**管理**在应用程序中的部分。
1. 选择**应用程序模板导出**。
1. 上**应用程序模板导出**页上，输入的名称和描述你的模板。
1. 选择**导出**按钮以创建应用程序模板。 现在，可以复制**可共享的链接**，使我们可以从模板创建新的应用程序：

![创建应用程序模板](media/howto-use-app-templates/create-template.png)

## <a name="use-an-application-template"></a>使用应用程序模板

若要使用的应用程序模板创建新的 IoT Central 应用程序，您需要以前创建**可共享的链接**。 粘贴**可共享的链接**到浏览器的地址栏。 **创建的应用程序**页显示您选择的自定义应用程序模板：

![从模板创建的应用程序](media/howto-use-app-templates/create-app.png)

选择付款计划，并填写窗体上的其他字段。 然后选择**创建**从应用程序模板创建新的 IoT Central 应用程序。

## <a name="manage-application-templates"></a>管理应用程序模板

上**应用程序模板导出**页上，可以删除或更新应用程序模板。

如果删除应用程序模板，你无法再使用以前生成可共享的链接来创建新的应用程序。

若要更新应用程序模板，请更改模板名称或描述上**应用程序模板导出**页。 然后选择**导出**按钮再次。 此操作将生成一个新**可共享的链接**并且使失效之前**可共享的链接**URL。

## <a name="next-steps"></a>后续步骤

现在，已了解如何使用应用程序模板，建议下一步是了解如何[管理 IoT 中心从 Azure 门户](howto-manage-iot-central-from-portal.md)
