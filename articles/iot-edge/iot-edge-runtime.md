---
title: 了解运行时如何管理设备 - Azure IoT Edge |Microsoft Docs
description: Learn how the IoT Edge runtime manages modules, security, communication, and reporting on your devices
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6382159c2a9d73b9db21dd0467be0e68cf4b4c2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456595"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 运行时及其体系结构

The IoT Edge runtime is a collection of programs that turn a device into an IoT Edge device. Collectively, the IoT Edge runtime components enable IoT Edge devices to receive code to run at the edge and communicate the results. 

The IoT Edge runtime is responsible for the following functions on IoT Edge devices:

* 在设备上安装和更新工作负荷。
* 维护设备上的 Azure IoT Edge 安全标准。
* Ensure that [IoT Edge modules](iot-edge-modules.md) are always running.
* 将模块运行状况报告给云以进行远程监视。
* Manage communication between downstream devices and IoT Edge devices.
* Manage communication between modules on the IoT Edge device.
* Manage communication between the IoT Edge device and the cloud.

![运行时向 IoT 中心传达见解和模块运行状况](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 运行时的职责分为两类：通信和模块管理。 These two roles are performed by two components that are part of the IoT Edge runtime. The *IoT Edge hub* is responsible for communication, while the *IoT Edge agent* deploys and monitors the modules. 

IoT Edge 中心和 IoT Edge 代理都是模块，就像 IoT Edge 设备上运行的其他任何模块一样。 They're sometimes referred to as the *runtime modules*. 

## <a name="iot-edge-hub"></a>IoT Edge 中心

IoT Edge 中心是组成 Azure IoT Edge 运行时的两个模块之一。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端（无论是设备还是模块）可以连接到 IoT Edge 运行时，就像连接到 IoT 中心一样。 

>[!NOTE]
> IoT Edge hub supports clients that connect using MQTT or AMQP. 它不支持使用 HTTP 的客户端。 

IoT Edge 中心不是在本地运行的完整版本的 IoT 中心。 有一些功能是 IoT Edge 中心以静默方式委托给 IoT 中心的。 例如，设备首次尝试连接时，IoT Edge 中心会将身份验证请求转发给 IoT 中心。 建立第一个连接之后，IoT Edge 中心会在本地缓存安全信息。 无需在云中进行身份验证即可允许该设备的后续连接。 

为减少 IoT Edge 解决方案使用的带宽，IoT Edge 中心优化了对云的实际连接数量。 IoT Edge hub takes logical connections from clients like modules or downstream devices and combines them for a single physical connection to the cloud. 此过程的详细信息对解决方案的其他部分透明。 即使客户端都通过相同连接进行发送，它们也会认为具有自己的云连接。 

![IoT Edge 中心是物理设备和 IoT 中心之间的网关](./media/iot-edge-runtime/Gateway.png)

IoT Edge 中心可以确定其是否连接到了 IoT 中心。 如果连接丢失，IoT Edge 中心将在本地保存消息或克隆更新。 一旦重新建立连接，将同步所有数据。 此临时缓存的位置由 IoT Edge 中心的模块孪生的属性决定。 只要设备具有存储容量，缓存的大小就没有限制并且会增加。 For more information, see [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>模块通信

IoT Edge 中心促进模块间通信。 使用 IoT Edge 中心作为消息中转站可以保持模块之间相互独立。 模块只需指定它们接受消息的输入和写入消息的输出。 A solution developer can stitch these inputs and outputs together so that the modules process data in the order specific to that solution. 

![IoT Edge 中心促进模块间通信](./media/iot-edge-runtime/module-endpoints.png)

为了将数据发送到 IoT Edge 中心，模块会调用 SendEventAsync 方法。 第一个参数指定要发送消息的输出。 The following pseudocode sends a message on **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync("output1", message); 
   ```

若要接收消息，请注册一个回叫，用于处理在特定输入上传入的消息。 The following pseudocode registers the function messageProcessor to be used for processing all messages received on **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

解决方案开发者负责指定用于确定 IoT Edge 中心如何在模块间传递消息的规则。 Routing rules are defined in the cloud and pushed down to IoT Edge hub in its module twin. 使用 IoT 中心路由的同一语法定义在 Azure IoT Edge 中的模块之间的路由。 For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

![模块之间的路由通过 IoT Edge 中心](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 代理

IoT Edge 代理是构成 Azure IoT Edge 运行时的其他模块。 它负责实例化模块、确保它们继续运行以及报告返回到 IoT 中心的模块的状态。 This configuration data is written as a property of the IoT Edge agent module twin. 

[IoT Edge 安全守护程序](iot-edge-security-manager.md)在设备启动时启动 IoT Edge 代理。 该代理从 IoT 中心检索其模块孪生并检查部署清单。 部署清单是一个 JSON 文件，它声明了需要启动的模块。 

部署清单中的每个项都包含有关模块的特定信息，并由 IoT Edge 代理用于控制模块的生命周期。 下面是一些更有趣的属性： 

* settings.image - IoT Edge 代理用来启动模块的容器映像。 如果该映像受密码保护，则必须为 IoT Edge 代理配置容器注册表的凭据。 可以使用部署清单远程配置容器注册表的凭据，也可以在 IoT Edge 设备本身上通过更新 IoT Edge 程序文件夹中的 `config.yaml` 文件进行配置。
* **settings.createOptions** – A string that is passed directly to the Moby container daemon when starting a module’s container. Adding options in this property allows for advanced configurations like port forwarding or mounting volumes into a module’s container.  
* 状态 - IoT Edge 代理放置模块的状态。 Usually, this value is set to *running* as most people want the IoT Edge agent to immediately start all modules on the device. 但是，可以将模块的初始状态指定为“已停止”，等待一定时间后告知 IoT Edge 代理启动模块。 The IoT Edge agent reports the status of each module back to the cloud in the reported properties. 所需属性和报告的属性之间存在差异指示了设备运行状况不正常。 支持的状态为：
   * 正在下载
   * 正在运行
   * 不正常
   * 已失败
   * 已停止
* restartPolicy - IoT Edge 代理如何重启模块。 可能值包括：
   * `never` – The IoT Edge agent never restarts the module.
   * `on-failure` - If the module crashes, the IoT Edge agent restarts it. 如果该模块完全关闭，IoT Edge 代理不会重启它。
   * `on-unhealthy` - If the module crashes or is considered unhealthy, the IoT Edge agent restarts it.
   * `always` - If the module crashes, is considered unhealthy, or shuts down in any way, the IoT Edge agent restarts it. 
* **imagePullPolicy** - Whether the IoT Edge agent attempts to pull the latest image for a module automatically or not. If you don't specify a value, the default is *onCreate*. 可能值包括： 
   * `on-create` - When starting a module or updating a module based on a new deployment manifest, the IoT Edge agent will attempt to pull the module image from the container registry.
   * `never` - The IoT Edge agent will never attempt to pull the module image from the container registry. The expectation is that the module image is cached on the device, and any module image updates are made manually or managed by a third-party solution. 

IoT Edge 代理会将运行时响应发送到 IoT 中心。 下面是可能的响应的列表：
  * 200 - 正常
  * 400 - 部署配置格式不正确或无效。
  * 417 - The device doesn't have a deployment configuration set.
  * 412 - 部署配置中的架构版本无效。
  * 406 - IoT Edge 设备脱机或不发送状态报告。
  * 500 - IoT Edge 运行时中出现了一个错误。

For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).   

### <a name="security"></a>“安全”

IoT Edge 代理在 IoT Edge 设备的安全性中起着关键作用。 例如，它会执行某些操作，如在启动之前验证模块的映像。 

For more information about the Azure IoT Edge security framework, read about the [IoT Edge security manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>后续步骤

[了解 Azure IoT Edge 模块](iot-edge-modules.md)
