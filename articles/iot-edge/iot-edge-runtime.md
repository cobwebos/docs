---
title: 了解 Azure IoT Edge 运行时 | Microsoft Docs
description: 了解 Azure IoT Edge 运行时以及它如何支持边缘设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: aa371ef2ebad01fba379675e8438f56dca9ce356
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096959"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>了解 Azure IoT Edge 运行时及其体系结构

IoT Edge 运行时是一系列程序，设备安装了这些程序才可视为 IoT Edge 设备。 IoT Edge 运行时中的组件共同协作，使 IoT Edge 设备能够接收代码，从而在 Edge 运行和传达结果。 

IoT Edge 运行时在 IoT Edge 设备上执行以下功能：

* 安装和更新设备上的工作负荷。
* 维护设备上的 Azure IoT Edge 安全标准。
* 确保 [IoT Edge 模块][lnk-modules] 保持运行状态。
* 将模块运行状况报告给云以进行远程监控。
* 促进下游叶设备与 IoT Edge 设备之间的通信。
* 促进 IoT Edge 设备上的模块间的通信。
* 促进 IoT Edge 设备和云之间的通信。

![IoT Edge 运行时向 IoT 中心传达见解和模块运行状况][1]

IoT Edge 运行时的职责分为两类：模块管理和通信。 这两种角色是由组成 IoT Edge 运行时的两个组件执行的。 IoT Edge 中心负责通信，IoT Edge 代理管理部署和监视模块。 

Edge 代理和 Edge 中心都是模块，就像 IoT Edge 设备上运行的其他任何模块一样。 有关模块如何工作的详细信息，请参阅 [lnk-modules]。 

## <a name="iot-edge-hub"></a>IoT Edge 中心

Edge 中心是组成 Azure IoT Edge 运行时的两个模块之一。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端（无论是设备还是模块）可以连接到 IoT Edge 运行时，就像连接到 IoT 中心一样。 

>[!NOTE]
>Edge 中心支持使用 MQTT 或 AMQP 进行连接的客户端， 它不支持使用 HTTP 的客户端。 

Edge 中心不是在本地运行的完整版本的 IoT 中心。 有一些功能是 Edge 中心以静默方式委托给 IoT 中心的。 例如，设备首次尝试连接时，Edge 中心会将身份验证请求转发给 IoT 中心。 建立第一个连接之后，Edge 中心会在本地缓存安全信息。 无需在云中进行身份验证即可允许该设备的后续连接。 

>[!NOTE]
>每次尝试对设备进行身份验证时，必须连接运行时。

为减少 IoT Edge 解决方案使用的带宽，Edge 中心优化了对云的实际连接数量。 Edge 中心采用来自客户端（如模块或叶设备）的逻辑连接，并将它们组合为连接到云的单个物理连接。 此过程的详细信息对解决方案的其他部分透明。 即使客户端都通过相同连接进行发送，它们也会认为具有自己的云连接。 

![Edge 中心充当多个物理设备和云之间的网关][2]

Edge 中心可以确定其是否连接到了 IoT 中心。 如果连接丢失，Edge 中心将在本地保存消息或克隆更新。 一旦重新建立连接，将同步所有数据。 此临时缓存的位置由 Edge 中心的模块孪生的属性决定。 只要设备具有存储容量，缓存的大小就没有限制并且会增加。 

>[!NOTE]
>对其他缓存参数添加的控件将在其正式发布之前添加到产品中。

### <a name="module-communication"></a>模块通信

Edge 中心促进模块间通信。 使用 Edge 中心作为消息中转站可以保持模块之间相互独立。 模块只需指定它们接受消息的输入和写入消息的输出。 然后解决方案开发者将这些输入和输出拼结在一起，以便模块按特定于该解决方案的顺序处理数据。 

![Edge 中心促进模块间通信。][3]

为了将数据发送到 Edge 中心，模块会调用 SendEventAsync 方法。 第一个参数指定要发送消息的输出。 下面的伪代码发送 output1 上的消息：

   ```csharp
   DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

若要接收消息，请注册一个回叫，用于处理在特定输入上传入的消息。 下面的伪代码注册用于处理在 input1 上接收到的所有消息的函数 messageProcessor。

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

解决方案开发者负责指定用于确定 Edge 中心如何在模块间传递消息的规则。 路由规则在云中定义，并向下推送到其设备孪生中的 Edge 中心。 使用 IoT 中心路由的同一语法定义在 Azure IoT Edge 中的模块之间的路由。 

<!--- For more info on how to declare routes between modules, see []. --->   

![模块之间的路由][4]

## <a name="iot-edge-agent"></a>IoT Edge 代理

IoT Edge 代理是构成 Azure IoT Edge 运行时的其他模块。 它负责实例化模块、确保它们继续运行以及报告返回到 IoT 中心的模块的状态。 就和其他任何模块一样，Edge 代理使用其模块孪生来存储此配置数据。 

若要开始执行 Edge 代理，请运行 azure-iot-edge-runtime-ctl.py 开始命令。 该代理从 IoT 中心检索其模块孪生并检查模块字典。 模块字典是需要启动的模块的集合。 

模块字典中的每一项包含有关模块的特定信息，并由 Edge 代理用于控制模块的生命周期。 下面是一些更有趣的属性： 

* settings.image - Edge 代理用来启动模块的容器映像。 如果该映像受密码保护，则必须为 Edge 代理配置容器注册表的凭据。 若要配置 Edge 代理，请更新 `config.yaml` 文件。 在 Linux 中使用以下命令：`sudo nano /etc/iotedge/config.yaml`
* settings.createOptions - 启动模块的容器时直接传递到 Docker 守护程序的字符串。 在此属性中添加 Docker 选项允许高级选项，如端口转接或将卷装载到模块的容器中。  
* 状态 - Edge 代理放置模块的状态。 此值通常设置为“正在运行”，因为大多数人希望 Edge 代理立即启动设备上的所有模块。 但是，可以将模块的初始状态指定为“已停止”，等待一定时间后告知 Edge 代理启动模块。 Edge 代理会向报告属性中的云报告每个模块的状态。 所需属性和报告的属性之间存在差异指示了设备运行状况不正常。 支持的状态为：
   * 正在下载
   * 正在运行
   * 不正常
   * 已失败
   * 已停止
* restartPolicy - Edge 代理如何重启模块。 可能的值包括：
   * 从不 – Edge 代理永远不会重启模块。
   * 故障时 - 如果模块发生故障，Edge 代理会重启它。 如果该模块完全关闭，Edge 代理不会重启它。
   * 不正常 - 如果模块发生故障或被视为不正常，Edge 代理会重启它。
   * 始终 - 如果模块发生故障、被视为不正常或者以任何方式关闭，Edge 代理会重启它。 

IoT Edge 代理会将运行时响应发送到 IoT 中心。 下面是可能的响应的列表：
  * 200 - 正常
  * 400 - 部署配置格式不正确或无效。
  * 417 - 设备没有部署配置集。
  * 412 - 部署配置中的架构版本无效。
  * 406 - edge 设备脱机或不发送状态报告。
  * 500 - edge 运行时中出错。

### <a name="security"></a>“安全”

IoT Edge 代理在 IoT Edge 设备的安全性中起着关键作用。 例如，它会执行某些操作，如在启动之前验证模块的映像。 正式发布时将添加这些功能。 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>后续步骤

- [了解 Azure IoT Edge 模块][lnk-modules]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-modules]: iot-edge-modules.md
