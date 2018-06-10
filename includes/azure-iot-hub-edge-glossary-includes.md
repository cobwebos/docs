## <a name="automatic-device-management"></a>自动设备管理
Azure IoT 中心内的自动设备管理功能可将许多复杂且重复性的任务自动化，包括在大型设备阵列的整个生命周期内对其进行管理。 使用自动设备管理，可以根据设备的属性将一组设备指定为目标、定义所需的配置，并在设备进入管理范畴时让 IoT 中心更新这些设备。  包括[自动设备配置](/articles/iot-hub/iot-hub-auto-device-config.md)和 [IoT Edge 自动部署](/articles/iot-edge/how-to-deploy-monitor.md)。

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge 支持在本地设备进行 Azure 服务和解决方案特定代码的云驱动部署。 IoT Edge 设备可以聚合其他设备的数据，以在数据发送到云之前执行计算和分析。 有关详细信息，请参阅 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)。

## <a name="iot-edge-agent"></a>IoT Edge 代理
IoT Edge 运行时部件负责部署和监视模块。

## <a name="iot-edge-device"></a>IoT Edge 设备
IoT Edge 设备已安装 IoT Edge 运行时，并且在设备详细信息中标记为“IoT Edge 设备”。 了解如何[在 Linux 的模拟设备上部署 Azure IoT Edge - 预览](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux)。

## <a name="iot-edge-automatic-deployment"></a>IoT Edge 自动部署
IoT Edge 自动部署配置 IoT Edge 设备的目标集以运行 IoT Edge 模块集。 每次部署持续确保符合其目标条件的所有设备运行指定的模块集，即使是在创建新设备或者为满足目标条件修改设备时。 每个 IoT Edge 设备仅接收满足其目标条件的最高优先级部署。 了解有关 [IoT Edge 自动部署](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)的详细信息。

## <a name="iot-edge-deployment-manifest"></a>IoT Edge 部署清单
Json 文档包含要在一个或多个 IoT Edge 设备的模块孪生中复制的信息，以部署一组模块、路由和关联模块所需属性。

## <a name="iot-edge-gateway-device"></a>IoT Edge 网关设备
有下游设备的 IoT Edge 设备。 下游设备可以是 IoT Edge 设备，也可以不是 IoT Edge 设备。

## <a name="iot-edge-hub"></a>IoT Edge 中心
IoT Edge 运行时部件负责模块间通信、上游（流向 IoT 中心）和下游（离开 IoT Edge）通信。 

## <a name="iot-edge-leaf-device"></a>IoT Edge 叶设备
没有下游设备的 IoT Edge 设备。 

## <a name="iot-edge-module"></a>IoT Edge 模块
IoT Edge 模块是可部署到 IoT Edge 设备的 Docker 容器。 它执行特定的任务，例如从设备引入消息、转换消息，或者将消息发送到 IoT 中心。 它与其他模块进行通信并将数据发送到 IoT Edge 运行时。 [了解开发 IoT Edge 模块的要求和工具](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="iot-edge-module-identity"></a>IoT Edge 模块标识
IoT 中心模块标识注册表中的记录详细说明，模块用来向 Edge 中心或 IoT 中心进行身份验证的存在和安全凭证。

## <a name="iot-edge-module-image"></a>IoT Edge 模块映像
IoT Edge 运行时用来实例化模块实例的 docker 映像。

## <a name="iot-edge-module-twin"></a>IoT Edge 模块孪生
保存在存储模块实例状态信息的 IoT 中心内的 Json 文档。

## <a name="iot-edge-priority"></a>IoT Edge 优先级
当两个 IoT Edge 部署以同一设备为目标时，应用优先级更高的部署。 如果两个部署优先级相同，则应用创建日期更晚的部署。 了解有关[优先级](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority)的详细信息。

## <a name="iot-edge-runtime"></a>IoT Edge 运行时
IoT Edge 运行时包括 Microsoft 分发用于安装在 IoT Edge 设备上的所有内容。 它包括 Edge 代理、Edge 中心和 Edge CTL 工具。

## <a name="iot-edge-set-modules-to-a-single-device"></a>IoT Edge 将模块设置为单个设备
在一个设备的模块孪生上复制 IoT Edge 清单内容的操作。 基础 API 是泛型“应用配置”，仅将 IoT Edge 清单作为输入。

## <a name="iot-edge-target-condition"></a>IoT Edge 目标条件
在 IoT Edge 部署中，目标条件是设备孪生标记上的任意布尔条件，以选择部署的目标设备（例如“tag.environment = prod”）。 持续评估目标条件，以包括满足需求的任何新设备或者删除不再满足要求的设备。 了解有关[目标条件](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)的详细信息