# 概述
## [Azure 和 IoT](iot-hub-what-is-azure-iot.md)
## [Azure IoT 中心是什么？](iot-hub-what-is-iot-hub.md)
## [设备管理概述](iot-hub-device-management-overview.md)

# [入门](iot-hub-get-started.md)

## 设置设备
### [模拟 PC 上的设备](iot-hub-get-started-simulated.md)
#### [.NET](iot-hub-csharp-csharp-getstarted.md)
#### [Java](iot-hub-java-java-getstarted.md)
#### [Node.js](iot-hub-node-node-getstarted.md)
#### [Python](iot-hub-python-getstarted.md)

### [使用联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)

### [使用物理设备](iot-hub-get-started-physical.md)
#### [将 Raspberry Pi 与 Node.js 配合使用](iot-hub-raspberry-pi-kit-node-get-started.md)
#### [将 Raspberry Pi 与 C 配合使用](iot-hub-raspberry-pi-kit-c-get-started.md)

#### [将 Intel Edison 与 Node.js 配合使用](iot-hub-intel-edison-kit-node-get-started.md)
#### [将 Intel Edison 与 C 配合使用](iot-hub-intel-edison-kit-c-get-started.md)

#### [将 Adafruit Feather HUZZAH ESP8266 与 Arduino IDE 配合使用](iot-hub-arduino-huzzah-esp8266-get-started.md)
#### [将 Sparkfun ESP8266 Thing Dev 与 Arduino IDE 配合使用](iot-hub-sparkfun-esp8266-thing-dev-get-started.md)
#### [将 Adafruit Feather M0 与 Arduino IDE 配合使用](iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md)

#### 使用 IoT 网关初学者工具包
##### [将 Intel NUC 设置为网关](iot-hub-gateway-kit-c-lesson1-set-up-nuc.md)
##### [将网关连接到 IoT 中心](iot-hub-gateway-kit-c-iot-gateway-connect-device-to-cloud.md)
##### [使用网关进行数据转换](iot-hub-gateway-kit-c-use-iot-gateway-for-data-conversion.md)

## 扩展的 IoT 方案
### [使用 iothub-explorer 管理云设备消息传送](iot-hub-explorer-cloud-device-messaging.md)
### [将 IoT 中心消息保存到 Azure 数据存储](iot-hub-store-data-in-azure-table-storage.md)
### [Power BI 中的数据可视化](iot-hub-live-data-visualization-in-power-bi.md)
### [使用 Web 应用进行数据可视化](iot-hub-live-data-visualization-in-web-apps.md)
### [使用 Azure 机器学习预测天气](iot-hub-weather-forecast-machine-learning.md)
### [使用 iothub-explorer 进行设备管理](iot-hub-device-management-iothub-explorer.md)
### [使用逻辑应用执行远程监视和发送通知](iot-hub-monitoring-notifications-with-azure-logic-apps.md)

# 如何
## 计划
### [IoT 中心与事件中心的比较](iot-hub-compare-event-hubs.md)
### [缩放解决方案](iot-hub-scaling.md)
### [高可用性和灾难恢复](iot-hub-ha-dr.md)
### [支持其他协议](iot-hub-protocol-gateway.md)
## [开发](iot-hub-how-to.md)
### [开发人员指南](iot-hub-devguide.md)
#### [设备到云的功能指南](iot-hub-devguide-d2c-guidance.md)
#### [云到设备的功能指南](iot-hub-devguide-c2d-guidance.md)
#### [发送和接收消息](iot-hub-devguide-messaging.md)
##### [将设备到云的消息发送到 IoT 中心](iot-hub-devguide-messages-d2c.md)
##### [从内置终结点读取设备到云的消息](iot-hub-devguide-messages-read-builtin.md)
##### [对设备到云的消息使用自定义终结点和路由规则](iot-hub-devguide-messages-read-custom.md)
##### [从 IoT 中心发送云到设备的消息](iot-hub-devguide-messages-c2d.md)
##### [创建和读取 IoT 中心消息](iot-hub-devguide-messages-construct.md)
##### [选择通信协议](iot-hub-devguide-protocols.md)
#### [从设备上传文件](iot-hub-devguide-file-upload.md)
#### [管理设备标识](iot-hub-devguide-identity-registry.md)
#### [控制对 IoT 中心的访问](iot-hub-devguide-security.md)
#### [了解设备孪生](iot-hub-devguide-device-twins.md)
#### [对设备调用直接方法](iot-hub-devguide-direct-methods.md)
#### [在多个设备上计划作业](iot-hub-devguide-jobs.md)
#### [IoT 中心终结点](iot-hub-devguide-endpoints.md)
#### [查询语言](iot-hub-devguide-query-language.md)
#### [配额和限制](iot-hub-devguide-quotas-throttling.md)
#### [定价示例](iot-hub-devguide-pricing.md)
#### [设备和服务 SDK](iot-hub-devguide-sdks.md)
#### [MQTT 支持](iot-hub-mqtt-support.md)
#### [术语表](iot-hub-devguide-glossary.md)
### [使用适用于 C 的 IoT 设备 SDK](iot-hub-device-sdk-c-intro.md)
#### [使用 IoTHubClient](iot-hub-device-sdk-c-iothubclient.md)
#### [使用序列化程序](iot-hub-device-sdk-c-serializer.md)
### 处理设备到云的消息
#### [.NET](iot-hub-csharp-csharp-process-d2c.md)
#### [Java](iot-hub-java-java-process-d2c.md)
### 发送“云到设备”消息
#### [.NET](iot-hub-csharp-csharp-c2d.md)
#### [Java](iot-hub-java-java-c2d.md)
#### [Node.js](iot-hub-node-node-c2d.md)
### [从设备上传文件](iot-hub-csharp-csharp-file-upload.md)
### 设备孪生入门
#### [Node.js 后端/Node.js 设备](iot-hub-node-node-twin-getstarted.md)
#### [.NET 后端/Node.js 设备](iot-hub-csharp-node-twin-getstarted.md)
#### [.NET 后端/.NET 设备](iot-hub-csharp-csharp-twin-getstarted.md)
### 使用直接方法
#### [Node.js 后端/Node.js 设备](iot-hub-node-node-direct-methods.md)
#### [.NET 后端/Node.js 设备](iot-hub-csharp-node-direct-methods.md)
#### [.NET 后端/.NET 设备](iot-hub-csharp-csharp-direct-methods.md)
#### [Java 后端/Java 设备](iot-hub-java-java-direct-methods.md)
### 设备管理入门
#### [Node.js 后端/Node.js 设备](iot-hub-node-node-device-management-get-started.md)
#### [.NET 后端/Node.js 设备](iot-hub-csharp-node-device-management-get-started.md)
#### [Java 后端/Java 设备](iot-hub-java-java-device-management-getstarted.md)
### 如何使用克隆属性
#### [Node.js 后端/Node.js 设备](iot-hub-node-node-twin-how-to-configure.md)
#### [.NET 后端/Node.js 设备](iot-hub-csharp-node-twin-how-to-configure.md)
#### [.NET 后端/.NET 设备](iot-hub-csharp-csharp-twin-how-to-configure.md)
### 使用设备作业更新设备固件
#### [Node 后端/Node 设备](iot-hub-node-node-firmware-update.md)
#### [.NET 后端/Node.js 设备](iot-hub-csharp-node-firmware-update.md)
### 计划和广播作业
#### [Node.js 后端/Node.js 设备](iot-hub-node-node-schedule-jobs.md)
#### [.NET 后端/Node.js 设备](iot-hub-csharp-node-schedule-jobs.md)
## 管理
### 创建 IoT 中心 
#### [使用门户](iot-hub-create-through-portal.md)
#### [使用 PowerShell](iot-hub-create-using-powershell.md)
#### [使用 CLI 2.0](iot-hub-create-using-cli.md)
#### [使用 CLI](iot-hub-create-using-cli-nodejs.md)
#### [使用 REST API](iot-hub-rm-rest.md)
#### [通过 PowerShell 使用模板](iot-hub-rm-template-powershell.md)
#### [通过 .NET 使用模板](iot-hub-rm-template.md)
### 配置文件上传
#### [使用门户](iot-hub-configure-file-upload.md)
#### [使用 PowerShell](iot-hub-configure-file-upload-powershell.md)
#### [使用 CLI 2.0](iot-hub-configure-file-upload-cli.md)
### [批量管理 IoT 设备](iot-hub-bulk-identity-mgmt.md)
### [使用指标](iot-hub-metrics.md)
### [操作监视](iot-hub-operations-monitoring.md)
### [配置 IP 筛选](iot-hub-ip-filtering.md)
## 安全
### [安全基础知识](iot-hub-security-ground-up.md)
### [安全最佳实践](iot-hub-security-best-practices.md)
### [安全体系结构](iot-hub-security-architecture.md)
### [保护 IoT 部署](iot-hub-security-deployment.md)
## Azure IoT Edge
### [概述](iot-hub-iot-edge-overview.md)
### 入门
#### [Linux](iot-hub-linux-iot-edge-get-started.md)
#### [Windows](iot-hub-windows-iot-edge-get-started.md)
### 模拟设备
#### [Linux](iot-hub-linux-iot-edge-simulated-device.md)
#### [Windows](iot-hub-windows-iot-edge-simulated-device.md)
### [使用真实设备](iot-hub-iot-edge-physical-device.md)
### 创建模块
#### [Java](iot-hub-iot-edge-create-module-java.md)
#### [.NET Framework](https://github.com/Azure-Samples/iot-edge-samples#how-to-run-the-net-module-sample-windows-10)
#### [.NET 标准](iot-hub-iot-edge-create-module-dotnet-core.md)
#### [Node.js](iot-hub-iot-edge-create-module-js.md)
### 构建
#### [.NET Framework](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_binding_sample)
#### [.NET Core 模块](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_module_sample)
#### [.NET Core 托管网关](https://github.com/Azure/iot-edge/tree/master/samples/dotnet_core_managed_gateway)
#### [Java](https://github.com/Azure/iot-edge/tree/master/samples/java_sample)
#### [Node.js](https://github.com/Azure/iot-edge/tree/master/samples/nodejs_simple_sample)
#### [动态添加模块](https://github.com/Azure/iot-edge/tree/master/samples/dynamically_add_module_sample)
#### [进程外代理模块](https://github.com/Azure/iot-edge/tree/master/samples/proxy_sample)
#### [本机模块主机](https://github.com/Azure/iot-edge/tree/master/samples/native_module_host_sample)

# 引用
## [Azure CLI](/cli/azure/iot)
## [.NET（服务）](/dotnet/api/microsoft.azure.devices)
## [.NET（设备）](/dotnet/api/microsoft.azure.devices.client)
## [Java（服务）](/java/api/com.microsoft.azure.sdk.iot.service)
## [Java（设备）](/java/api/com.microsoft.azure.sdk.iot.device)
## [Node.js SDK](http://azure.github.io/azure-iot-sdk-node/)
## [C 设备 SDK](https://azure.github.io/azure-iot-sdk-c/index.html)
## [Azure IoT Edge](http://azure.github.io/iot-edge/)
## [REST（资源提供程序）](https://docs.microsoft.com/rest/api/iothub/iothubresource)
## [REST（设备标识）](https://docs.microsoft.com/rest/api/iothub/deviceapi)
## [REST（设备孪生）](https://docs.microsoft.com/rest/api/iothub/devicetwinapi)
## [REST（设备消息传送）](https://docs.microsoft.com/rest/api/iothub/httpruntime)
## [REST（作业）](https://docs.microsoft.com/rest/api/iothub/jobapi)

# 相关内容
## [Azure IoT 套件](https://azure.microsoft.com/documentation/suites/iot-suite/)
## [Azure 事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)
## [流分析](https://azure.microsoft.com/documentation/services/stream-analytics/)
## [机器学习](https://azure.microsoft.com/documentation/services/machine-learning/)

# 资源
## [Azure IoT 已认证设备目录](https://catalog.azureiotsuite.com/)
## [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot/)
## [Azure 路线图](https://azure.microsoft.com/roadmap/)
## [DeviceExplorer 工具](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
## [iothub-diagnostics 工具](https://github.com/Azure/iothub-diagnostics)
## [iothub-explorer 工具](https://github.com/Azure/iothub-explorer)
## [学习路径](https://azure.microsoft.com/documentation/learning-paths/iot-hub/)
## [MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azureiothub)
## [定价](https://azure.microsoft.com/pricing/details/iot-hub/)
## [服务更新](https://azure.microsoft.com/updates/?product=iot-hub)
## [堆栈溢出](http://stackoverflow.com/questions/tagged/azure-iot-hub)
## [技术案例研究](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)
## [视频](https://azure.microsoft.com/documentation/videos/index/?services=iot-hub)
