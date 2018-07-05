---
title: 了解 Azure IoT Edge 模块 | Microsoft 文档
description: 了解有关 Azure IoT Edge 模块以及如何进行配置的信息
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c196ec92fc7997617fa464d676dc93ca9fe84f0
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029080"
---
# <a name="understand-azure-iot-edge-modules"></a>了解 Azure IoT Edge 模块

通过 Azure IoT Edge，可让你以模块的形式来部署和管理 Edge 上的业务逻辑。 Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码。 若要了解如何开发、部署和维护模块，很有必要先理解以下四个组成模块的概念：

* 模块映像是包含定义模块的程序包。
* 模块实例是在 IoT Edge 设备上运行模块映像的特定计算单位。 该模块实例由 IoT Edge 运行时启动。
* 模块标识是存储在 IoT 中心的一段信息（包括安全凭据），与每个模块实例相关联。
* 模块孪生是存储在 IoT 中心的 JSON 文档，包含模块实例的状态信息，其中包括元数据、配置和条件。 

## <a name="module-images-and-instances"></a>模块映像和实例

IoT Edge 模块映像包含能够充分利用 IoT Edge 运行时的管理、安全性和通信功能的应用程序。 你可以开发自己的模块映像，或从支持的 Azure 服务导出一个模块映像，如 Azure 流分析。
映像存在于云中，可以在不同解决方案中对其进行更新、更改和部署。 例如，使用机器学习来预测生产线输出的模块作为一个单独映像（而不是一个使用计算机视觉来控制无人机的模块）存在。 

每当将模块映像部署到设备上，并由 IoT Edge 运行时启动时，就会创建该模块的一个新实例。 世界不同地区的两台设备可以使用相同的模块映像；但是，当模块在设备上启动时，每个模块都有其自己的模块实例。 

![云中的模块映像 - 设备上模块实例][1]

在实现中，模块映像作为存储库中的容器映像存在，而模块实例则是设备上的容器。 随着 Azure IoT Edge 使用情况的不断增长，将会创建新类型的模块映像和实例。 例如，资源受限的设备不能运行容器，因此，可能需要作为动态链接库和可执行文件实例存在的模块映像。 

## <a name="module-identities"></a>模块标识

当 IoT Edge 运行时创建一个新的模块实例时，该实例与相应的模块标识相关联。 模块标识存储在 IoT 中心，用作该特定模块实例的所有本地和云通信的寻址和安全范围。
与模块实例相关联的标识将取决于在其上运行实例的设备的标识，以及在解决方案中为该模块提供的名称。 例如，如果调用使用 Azure 流分析的 `insight` 模块，并将其部署到一个名为 `Hannover01` 的设备上，那么 IoT Edge 运行时将创建一个相应的称为 `/devices/Hannover01/modules/insight` 的模块标识。

很显然，当需要在同一设备上多次部署一个模块映像时，可以使用不同的名称多次部署相同的映像。

![模块标识是唯一的][2]

## <a name="module-twins"></a>模块孪生

此外，每个模块实例都有一个对应的模块孪生，你可以使用它来配置模块实例。 实例和孪生通过模块标识进行相互关联。 

模块孪生是存储模块信息和配置属性的 JSON 文档。 此概念与 IoT 中心的[设备孪生][lnk-device-twin]概念类似。 模块孪生的结构与设备孪生完全相同。 用于与这两种类型的孪生进行交互的 API 也是相同的。 两者之间唯一的区别是用来实例化客户端 SDK 的标识。 

```csharp
// Create a DeviceClient object. This DeviceClient will act on behalf of a 
// module since it is created with a module’s connection string instead 
// of a device connection string. 
DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
await client.OpenAsync(); 
 
// Get the model twin 
Twin twin = await client.GetTwinAsync(); 
```

## <a name="offline-capabilities"></a>脱机功能

Azure IoT Edge 支持在 IoT Edge 设备上执行脱机操作。 目前，这些功能有限，我们正在开发更多的方案。 

只要满足以下要求，IoT Edge 模块可以长时间内处于脱机状态： 

* **消息生存期 (TTL) 未过**。 消息 TTL 的默认值是两个小时，但可以在 IoT Edge 中心设置中的存储和转发配置中将其更改为更大或更小的值。 
* **当处于脱机状态时，模块不需要通过 IoT Edge 中心重新进行身份验证**。 模块可以仅通过与 IoT 中心之间具有活动连接的 Edge 中心进行身份验证。 如果模块因任何原因而重启，则它们需要重新进行身份验证。 模块的 SAS 令牌过期后，模块仍然可以向 Edge 中心发送消息。 当连接恢复时，Edge 中心会向模块请求一个新令牌，并通过 IoT 中心验证该令牌。 如果成功，Edge 中心会转发它存储的模块消息，即使该消息在模块的令牌过期时已发送过。 
* **在脱机状态下发送消息的模块在连接恢复时仍然会工作**。 在重新连接到 IoT 中心时，Edge 中心需要对新的模块令牌进行验证（如果以前的令牌已过期），然后才能转发模块消息。 如果模块不可用来提供新令牌，则 Edge 中心无法对模块的已存储消息进行操作。 
* **Edge 中心利用磁盘空间来存储消息**。 默认情况下，消息存储在 Edge 中心容器的文件系统中。 有一个配置选项可用来指定改为使用装载的卷来存储消息。 在任一情况下，都需要有空间可用来存储延迟传递到 IoT 中心的消息。  

## <a name="next-steps"></a>后续步骤
 - [了解 Azure IoT Edge 运行时及其体系结构][lnk-runtime]

<!-- Images -->
[1]: ./media/iot-edge-modules/image_instance.png
[2]: ./media/iot-edge-modules/identity.png

<!-- Links -->
[lnk-device-identity]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-runtime]: iot-edge-runtime.md