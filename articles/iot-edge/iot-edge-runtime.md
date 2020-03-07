---
title: 了解运行时如何管理设备 - Azure IoT Edge |Microsoft Docs
description: 了解 IoT Edge 运行时如何管理设备上的模块、安全性、通信和报告
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358678"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 运行时及其体系结构

IoT Edge 运行时是将设备转换为 IoT Edge 设备的程序的集合。 IoT Edge 运行时组件共同允许 IoT Edge 设备接收代码，使其在边缘运行并传达结果。

IoT Edge 运行时负责 IoT Edge 设备上的以下功能：

* 在设备上安装和更新工作负荷。
* 维护设备上的 Azure IoT Edge 安全标准。
* 确保[IoT Edge 模块](iot-edge-modules.md)始终运行。
* 将模块运行状况报告给云以进行远程监视。
* 管理下游设备与 IoT Edge 设备之间的通信。
* 管理 IoT Edge 设备上的模块之间的通信。
* 管理 IoT Edge 设备和云之间的通信。

![运行时向 IoT 中心传达见解和模块运行状况](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 运行时的职责分为两类：通信和模块管理。 这两个角色由 IoT Edge 运行时的一部分的两个组件执行。 *IoT Edge 中心*负责通信，而*IoT Edge 代理*部署和监视模块。

IoT Edge 中心和 IoT Edge 代理都是模块，就像 IoT Edge 设备上运行的其他任何模块一样。 它们有时称为*运行时模块*。

## <a name="iot-edge-hub"></a>IoT Edge 中心

IoT Edge 中心是组成 Azure IoT Edge 运行时的两个模块之一。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端（无论是设备还是模块）可以连接到 IoT Edge 运行时，就像连接到 IoT 中心一样。

>[!NOTE]
> IoT Edge 中心支持使用 MQTT 或 AMQP 进行连接的客户端。 它不支持使用 HTTP 的客户端。

IoT Edge 集线器不是本地运行的 IoT 中心的完整版本。 IoT Edge 中心将一些任务以无提示方式委托给 IoT 中心。 例如，设备首次尝试连接时，IoT Edge 中心会将身份验证请求转发给 IoT 中心。 建立第一个连接之后，IoT Edge 中心会在本地缓存安全信息。 允许来自该设备的未来连接，而无需再次向云进行身份验证。

为了降低 IoT Edge 解决方案所使用的带宽，IoT Edge 中心优化了与云建立的实际连接的数量。 IoT Edge 中心从模块或下游设备获得逻辑连接，并将它们与云的单个物理连接合并。 此过程的详细信息对解决方案的其他部分透明。 即使客户端都通过相同连接进行发送，它们也会认为具有自己的云连接。

![IoT Edge 中心是物理设备和 IoT 中心之间的网关](./media/iot-edge-runtime/Gateway.png)

IoT Edge 中心可以确定其是否连接到了 IoT 中心。 如果连接丢失，IoT Edge 中心将在本地保存消息或克隆更新。 一旦重新建立连接，将同步所有数据。 此临时缓存的位置由 IoT Edge 中心的模块孪生的属性决定。 只要设备具有存储容量，缓存的大小就没有限制并且会增加。 有关详细信息，请参阅[脱机功能](offline-capabilities.md)。

### <a name="module-communication"></a>模块通信

IoT Edge 中心促进模块间通信。 使用 IoT Edge 中心作为消息中转站可以保持模块之间相互独立。 模块只需指定它们接受消息的输入和写入消息的输出。 解决方案开发人员可以将这些输入和输出组合在一起，以便模块按特定于该解决方案的顺序处理数据。

![IoT Edge 中心促进模块间通信](./media/iot-edge-runtime/module-endpoints.png)

为了将数据发送到 IoT Edge 中心，模块会调用 SendEventAsync 方法。 第一个参数指定要发送消息的输出。 以下伪代码在**output1**上发送一条消息：

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

若要接收消息，请注册一个回叫，用于处理在特定输入上传入的消息。 下面的伪代码注册了用于处理**input1**上收到的所有消息的 messageProcessor 函数：

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

有关 ModuleClient 类及其通信方法的更多信息，请参阅首选 SDK 语言的 API 参考：[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、 [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、 [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python)、 [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)[或 node.js。](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

解决方案开发者负责指定用于确定 IoT Edge 中心如何在模块间传递消息的规则。 路由规则在云中定义，并向下推送到其模块克隆中 IoT Edge 中心。 使用 IoT 中心路由的同一语法定义在 Azure IoT Edge 中的模块之间的路由。 有关详细信息，请参阅[了解如何部署模块和在 IoT Edge 中建立路由](module-composition.md)。

![模块之间的路由通过 IoT Edge 中心](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 代理

IoT Edge 代理是构成 Azure IoT Edge 运行时的其他模块。 它负责实例化模块、确保它们继续运行以及报告返回到 IoT 中心的模块的状态。 此配置数据被编写为 IoT Edge 代理模块克隆的属性。

[IoT Edge 安全守护程序](iot-edge-security-manager.md)在设备启动时启动 IoT Edge 代理。 该代理从 IoT 中心检索其模块孪生并检查部署清单。 部署清单是一个 JSON 文件，它声明了需要启动的模块。

部署清单中的每个项都包含有关模块的特定信息，并由 IoT Edge 代理用于控制模块的生命周期。 下面是一些更有趣的属性：

* settings.image - IoT Edge 代理用来启动模块的容器映像。 如果该映像受密码保护，则必须为 IoT Edge 代理配置容器注册表的凭据。 可以使用部署清单远程配置容器注册表的凭据，也可以在 IoT Edge 设备本身上通过更新 IoT Edge 程序文件夹中的 `config.yaml` 文件进行配置。
* **createOptions** –启动模块的容器时，直接传递到小鲸鱼容器守护程序的字符串。 如果在此属性中添加选项，则可以进行高级配置，例如端口转发或将卷装入模块的容器。  
* 状态 - IoT Edge 代理放置模块的状态。 通常情况下，此值设置为 "*运行*"，因为大多数人希望 IoT Edge 代理立即启动设备上的所有模块。 但是，可以将模块的初始状态指定为“已停止”，等待一定时间后告知 IoT Edge 代理启动模块。 IoT Edge 代理会向报告属性中的云报告每个模块的状态。 所需属性和报告的属性之间存在差异指示了设备运行状况不正常。 支持的状态为：

  * 正在下载
  * 正在运行
  * 不正常
  * 已失败
  * 已停止

* restartPolicy - IoT Edge 代理如何重启模块。 可能值包括：
  
  * `never` – IoT Edge 代理从不重新启动模块。
  * `on-failure`-如果模块发生故障，则 IoT Edge 代理将其重新启动。 如果模块完全关闭，IoT Edge 代理将不会重新启动。
  * `on-unhealthy`-如果模块发生故障或被视为不正常，则 IoT Edge 代理将其重新启动。
  * `always`-如果模块崩溃、被视为不正常或以任何方式关闭，则 IoT Edge 代理将其重新启动。

* **imagePullPolicy** -IoT Edge 代理是否尝试自动提取模块的最新图像。 如果未指定值，则默认值为*onCreate*。 可能值包括：

  * `on-create`-启动模块或基于新的部署清单更新模块时，IoT Edge 代理将尝试从容器注册表中提取模块映像。
  * `never`-IoT Edge 代理从不尝试从容器注册表中提取模块映像。 通过此配置，你负责将模块映像放到设备上，并管理任何映像更新。

IoT Edge 代理会将运行时响应发送到 IoT 中心。 下面是可能的响应的列表：
  
* 200 - 正常
* 400 - 部署配置格式不正确或无效。
* 417-设备没有部署配置集。
* 412 - 部署配置中的架构版本无效。
* 406 - IoT Edge 设备脱机或不发送状态报告。
* 500 - IoT Edge 运行时中出现了一个错误。

有关详细信息，请参阅[了解如何部署模块和在 IoT Edge 中建立路由](module-composition.md)。

### <a name="security"></a>安全性

IoT Edge 代理在 IoT Edge 设备的安全性中起着关键作用。 例如，它会执行某些操作，如在启动之前验证模块的映像。

有关 Azure IoT Edge 安全框架的详细信息，请参阅[IoT Edge 安全管理器](iot-edge-security-manager.md)。

## <a name="next-steps"></a>后续步骤

[了解 Azure IoT Edge 模块](iot-edge-modules.md)
