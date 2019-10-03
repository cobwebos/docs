---
title: 安装和使用 Azure IoT 浏览器 |Microsoft Docs
description: 安装 Azure IoT 资源管理器工具，并使用它与连接到 IoT 中心的 IoT 即插即用 Preview 设备进行交互。
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4e23a440f46b52633a88d0212e08c7b584f61a38
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70932479"
---
# <a name="install-and-use-azure-iot-explorer"></a>安装和使用 Azure IoT 浏览器

Azure IoT 浏览器是一种图形工具，用于与 IoT 即插即用预览版设备进行交互和测试。 在本地计算机上安装该工具后，可以使用它来连接到设备。 您可以使用该工具查看设备正在发送的遥测数据，使用设备属性和调用命令。

本文介绍以下操作：

- 安装和配置 Azure IoT 资源管理器工具。
- 使用该工具与设备交互并对其进行测试。

## <a name="prerequisites"></a>先决条件

若要使用 Azure IoT 资源管理器工具，你需要：

- Azure IoT 中心。 可以通过多种方式将 IoT 中心添加到 Azure 订阅，如[使用 Azure CLI 创建 iot 中心](../iot-hub/iot-hub-create-using-cli.md)。 需要 IoT 中心连接字符串才能运行 Azure IoT 资源管理器工具。 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 注册到 IoT 中心的设备。 你可以使用以下 Azure CLI 命令来注册设备。 请确保将`{YourIoTHubName}`和`{YourDeviceID}`占位符替换为值：

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

请访问[Azure IoT 资源管理器版本](https://github.com/Azure/azure-iot-explorer/releases)，并展开最新版本的资产列表。 下载并安装最新版本的应用程序。

## <a name="use-azure-iot-explorer"></a>使用 Azure IoT 浏览器

对于设备，可以连接自己的设备，也可以使用我们的模拟设备之一。 按照[以下说明](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples)运行模拟设备示例。

### <a name="connect-to-your-hub"></a>连接到中心

首次运行 Azure IoT 资源管理器时，系统将提示你提供 IoT 中心的连接字符串。 添加连接字符串后，选择 "**连接**"。 通过更新连接字符串，可以使用该工具的设置来切换到另一个 IoT 中心。

IoT 即插即用设备的模型定义存储在公共存储库、公司存储库或连接的设备中。 默认情况下，该工具在公共模型存储库和连接的设备中查找模型定义。 可以在 "**设置**" 中添加和删除源，或配置源的优先级：

添加源：

1. 转到“设置”。
1. 选择 "**新建**"，然后选择你的源。
1. 如果要添加公司模型存储库，请提供连接字符串。

删除源：

1. 转到“设置”。
1. 查找要删除的源。
1. 选择 " **X** " 将其删除。 不能删除公共模型存储库，因为公共接口定义来自此存储库。

更改源优先级：

您可以将模型定义源之一拖放到列表中的其他排名。 如果存在冲突，具有较高排名的定义源将覆盖排名更低的源。

### <a name="overview-page"></a>概述页

#### <a name="device-overview"></a>设备概述

该工具连接到 IoT 中心后，会显示一个概述页面，其中列出了注册到 Azure IoT 中心的所有设备标识。 选择设备以查看更多详细信息。

#### <a name="device-management"></a>设备管理

- 若要向中心注册新设备，请选择 "**添加**"。 输入设备 ID。 使用默认设置来自动生成身份验证密钥，并启用与中心的连接。
- 若要删除设备标识，请选择 "**删除**"。 请在完成此操作之前查看设备详细信息，以确保删除正确的设备标识。
- 该工具支持按和`capabilityID` `interfaceID`进行查询。 `capabilityID`添加或`interfaceID`作为参数以查询设备。

## <a name="interact-with-a-device"></a>与设备交互

双击 "概述" 页上的设备，查看下一级别的详细信息。 有两个部分：**设备**和**数字**设备。

### <a name="device"></a>设备

本部分包括**设备标识**、**遥测**和**设备**克隆选项卡。

- 可以在 "**设备标识**" 选项卡上查看和更新设备标识信息。
- 如果设备已连接并主动发送数据，则可以在 "**遥测**" 选项卡上查看遥测数据。
- 可以在 "**设备**克隆" 选项卡上访问设备克隆信息。

### <a name="digital-twin"></a>数字输出

您可以使用该工具来查看设备的数字克隆实例。 对于 IoT 即插即用设备，此文章中显示了与设备功能模型关联的所有接口。 选择接口以扩展其相应的[IoT 即插即用基元](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL)。

#### <a name="properties"></a>属性

您可以在 "**属性**" 页上查看接口中定义的只读属性。 可以在 "可**写属性**" 页上更新接口中定义的可写属性。

1. 请参阅**可写属性**页。
1. 单击要更新的属性。
1. 为属性输入新值。
1. 预览要发送到设备的有效负载。
1. 提交更改。

提交更改后，可以跟踪更新状态：**同步**、**成功**或**错误**。 完成同步后，会在**报告的属性**列中看到属性的新值。 如果在完成同步之前导航到其他页，则该工具在更新完成后仍会通知你。 你还可以使用该工具的通知中心来查看通知历史记录。

#### <a name="commands"></a>命令

若要将命令发送到设备，请参阅**命令**页：

1. 在命令列表中，展开要触发的命令。
1. 输入命令所需的任何值。
1. 预览要发送到设备的有效负载。
1. 提交命令。

#### <a name="telemetry"></a>遥测

若要查看所选接口的遥测数据，请切换到其**遥测**页。

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何安装和使用 Azure IoT 浏览器与 IoT 即插即用设备进行交互。 建议的下一步是了解如何[安装和使用 Azure CLI 扩展](./howto-install-pnp-cli.md)。
