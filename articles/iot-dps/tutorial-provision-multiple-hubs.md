---
title: "使用 Azure IoT 中心设备预配服务跨负载均衡的 IoT 中心预配设备 | Microsoft Docs"
description: "在 Azure 门户中跨负载均衡的 IoT 中心执行 DPS 自动设备预配"
services: iot-dps
keywords: 
author: sethmanheim
ms.author: sethm
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 4842944cd0d980fb7e817165da23b9c3c4037e94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="provision-devices-across-load-balanced-iot-hubs"></a>跨负载均衡的 IoT 中心预配设备

本教程演示如何使用设备预配服务 (DPS) 为多个负载均衡的 IoT 中心预配设备。 本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 使用 Azure 门户将另一台设备预配到另一个 IoT 中心 
> * 添加第二台设备的注册列表条目
> * 将 DPS 分配策略设置为“均匀分发”
> * 将新 IoT 中心链接到 DPS

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

本教程是在前面的[将设备预配到中心](tutorial-provision-device-to-hub.md)教程的基础上制作的。

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>使用 Azure 门户将另一台设备预配到另一个 IoT 中心

按照[将设备预配到中心](tutorial-provision-device-to-hub.md)教程中的步骤，将另一台设备预配到另一个 IoT 中心。

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>添加第二台设备的注册列表条目

注册列表会告知 DPS 该设备当前使用的证明方法（即确认设备标识的方法）。 下一步，添加第二台设备的注册列表条目。 

1. 在 DPS 页中，单击“管理注册”。 随即出现“添加注册列表条目”页。 
2. 在页面顶部，单击“添加”。
2. 填写字段，然后单击“保存”。

## <a name="set-the-dps-allocation-policy"></a>设置 DPS 分配策略

分配策略是一项 DPS 设置，用于确定将设备分配到 IoT 中心的方式。 支持三种分配策略： 

1. **最低延迟**：将设备预配到具有最低延迟的 IoT 中心。
2. **均匀加权分发**（默认）：链接的 IoT 中心等可能地获得预配到它们的设备。 此设置为默认设置。 如果只将设备预配到一个 IoT 中心，则可以保留此设置。 
3. **通过注册列表进行静态配置**：注册列表中所需 IoT 中心的规范优先于 DPS 级别的分配策略。

请按照以下步骤设置分配策略：

1. 若要设置分配策略，请在 DPS 页中单击“管理分配策略”。
2. 将分配策略设置为“均匀加权分发”。
3. 单击“保存” 。

## <a name="link-the-new-iot-hub-to-dps"></a>将新 IoT 中心链接到 DPS

链接 DPS 和 IoT 中心，以便 DPS 可将设备注册到该中心。

1. 在“所有资源”页上，单击之前创建的 DPS。
2. 在 DPS 页中，单击“链接 IoT 中心”。
3. 单击“添加”。
4. 在“将链接添加到 IoT 中心”页上，使用单选按钮指定链接的 IoT 中心位于当前订阅中还是其他订阅中。 然后，从“IoT 中心”框中选择该 IoT 中心的名称。
5. 单击“保存” 。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用 Azure 门户将另一台设备预配到另一个 IoT 中心 
> * 添加第二台设备的注册列表条目
> * 将 DPS 分配策略设置为“均匀分发”
> * 将新 IoT 中心链接到 DPS

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)
-->
