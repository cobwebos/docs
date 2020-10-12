---
title: 用于下游设备的网关 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure IoT Edge 创建一个透明、不透明或代理网关设备，以将数据从多个下游设备发送到云或在本地对其进行处理。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/21/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0589779de2ddb0bc75dde3b57d6444634b879f86
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89017016"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>如何将 IoT Edge 设备用作网关

IoT Edge 解决方案中的网关为 IoT 设备提供了设备连接和边缘分析，否则这些设备将不具备这些功能。 Azure IoT Edge 可用于满足对 IoT 网关的任何需求，无论该需求是否与连接、标识或边缘分析有关。 本文中的网关模式仅指下游设备连接和设备标识的特征，而不是指在网关上处理设备数据的方式。

## <a name="patterns"></a>模式

有三种将 IoT Edge 设备用作网关的模式：透明、协议转换和标识转换。

模式之间的主要区别在于，透明网关无需任何其他处理即可在下游设备与 IoT 中心之间传递消息。 但协议转换和身份转换需要在网关上进行处理才能实现通信。

在将消息从下游设备传递到 IoT 中心之前，任何网关都可以使用 IoT Edge 模块在边缘执行分析或预处理。

![关系图 - 透明、协议和标识网关模式](./media/iot-edge-as-gateway/edge-as-gateway.png)

### <a name="transparent-pattern"></a>透明模式

在 *透明* 网关模式下，理论上可以连接到 IoT 中心的设备可以改为连接到网关设备。 下游设备具有其自己的 IoT 中心标识，并使用 MQTT、AMQP 或 HTTP 协议中的任何一种协议。 网关只是在设备与 IoT 中心之间传递通信。 设备和通过 IoT 中心与其交互的用户都不知道网关正在协调它们的通信。 缺乏意识意味着网关被认为是 *透明*的。

IoT Edge 运行时包括透明的网关功能。 有关详细信息，请参阅[将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)。

### <a name="protocol-translation-pattern"></a>协议转换模式

*协议转换*网关也称为不*透明*网关，与透明网关模式相反。 在此模式下，不支持 MQTT、AMQP 或 HTTP 的设备可以使用网关设备代表它们将数据发送到 IoT 中心。 网关了解下游设备使用的协议，并且是 IoT 中心中唯一具有标识的设备。 所有信息好像都来自一台设备，即网关。 如果云应用程序想要以设备位单位分析数据，则下游设备就必须在其消息中嵌入额外的标识信息。 此外，IoT 中心基元（如孪生和方法）仅适用于网关设备，而不适用于下游设备。

IoT Edge 运行时不包括协议转换功能。 此模式需要自定义或第三方模块，这些模块通常特定于所使用的硬件和协议。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) 包含多个可供选择的协议转换模块。

### <a name="identity-translation-pattern"></a>标识转换模式

在 *身份转换* 网关模式下，无法连接到 IoT 中心的设备可以改为连接到网关设备。 网关以下游设备身份提供 IoT 中心标识和协议转换。 网关非常智能，它能够理解下游设备使用的协议，为其提供标识，并转换 IoT 中心基元。 下游设备作为一流设备出现在 IoT 中心，随附克隆和方法。 用户可以与 IoT 中心中的设备进行交互，而同时不了解中间网关设备。

IoT Edge 运行时不包括标识转换功能。 此模式需要自定义或第三方模块，这些模块通常特定于所使用的硬件和协议。 有关使用标识转换模式的示例，请参阅 [Azure IoT Edge LoRaWAN 初学者工具包](https://github.com/Azure/iotedge-lorawan-starterkit)。

## <a name="use-cases"></a>用例

所有网关模式提供以下优势：

* **边缘分析** – 在本地使用 AI 服务处理来自下游设备的数据，而无需向云发送完全保真的遥测数据。 本地查找和响应见解，并仅将一部分数据发送到 IoT 中心。
* 下游设备隔离  – 网关设备可以屏蔽所有下游设备，而不对 Internet 公开。 它可以位于无连接的 OT 网络和提供 Web 访问权限的 IT 网络之间。
* **连接多路复用** - 通过 IoT Edge 网关连接到 IoT 中心的所有设备使用同一个基础连接。
* 流量平滑  - 在本地保存消息的同时，如果 IoT 中心对流量进行限制，IoT Edge 设备将自动执行指数回退。 此优点使解决方案能灵活应对流量高峰。
* **脱机支持** - 网关设备存储不能传递到 IoT 中心的消息和孪生更新。

执行协议转换的网关可以支持现有设备和受资源约束的新设备。 许多现有设备将生成能够为企业提供见解的数据；然而，它们的设计并未考虑云连接。 不透明的网关允许解锁这些数据，并在 IoT 解决方案中使用这些数据。

实现标识转换的网关提供了协议转换的好处，并且还允许从云完全管理下游设备。 IoT 解决方案中的所有设备都显示在 IoT 中心内，不管它们使用的是什么协议。

## <a name="cheat-sheet"></a>备忘单

下面一个速查表，用于在使用透明、不透明（协议）和代理网关时比较 loT 中心基元。

| 基元 | 透明网关 | 协议转换 | 标识转换 |
|--------|-------------|--------|--------|
| 存储在 IoT 中心标识注册表中的标识 | 所有已连接的设备的标识 | 仅网关设备的标识 | 所有已连接的设备的标识 |
| 设备孪生 | 每个已连接的设备均有自己的设备孪生 | 仅网关具有设备和模块孪生 | 每个已连接的设备均有自己的设备孪生 |
| 直接方法和云到设备的消息 | 云可以对每个已连接的设备单独寻址 | 云只能对网关设备寻址 | 云可以对每个已连接的设备单独寻址 |
| [IoT 中心限制和配额](../iot-hub/iot-hub-devguide-quotas-throttling.md) | 适用于每个设备 | 适用于网关设备 | 适用于每个设备 |

使用不透明网关（协议转换）模式时，通过该网关连接的所有设备共享同一个可包含最多 50 条消息的云到设备的队列。 它遵循的原则是，仅当很少设备通过各字段网关进行连接以及云到设备的流量较低时，才使用不透明网关模式。

## <a name="next-steps"></a>后续步骤

了解设置透明网关的三个步骤：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
