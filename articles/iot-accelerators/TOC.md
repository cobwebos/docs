# [Azure IoT 基础](/azure/iot-fundamentals)
# [IoT 解决方案加速器文档](index.md)

# 概述
## [IoT 体系结构概念](iot-accelerators-what-is-azure-iot.md)
## [比较 Azure IoT 选项](iot-accelerators-options.md)
## [解决方案加速器概述](iot-accelerators-what-are-solution-accelerators.md)

# 入门
## 远程监视
### [部署解决方案加速器](iot-accelerators-remote-monitoring-deploy.md)
### [操作解决方案加速器](iot-accelerators-remote-monitoring-explore.md)

## 远程监视（旧版）
### [解决方案加速器是什么？](../iot-suite/iot-suite-v1-what-are-preconfigured-solutions.md)
### [常见问题](../iot-suite/iot-suite-v1-faq.md)

### 入门
#### [解决方案加速器入门](../iot-suite/iot-suite-v1-getstarted-preconfigured-solutions.md)
#### [azureiotsuite.com 权限](../iot-suite/iot-suite-v1-permissions.md)
#### [远程监视解决方案演练](../iot-suite/iot-suite-v1-remote-monitoring-sample-walkthrough.md)
#### [连接 Raspberry Pi](../iot-suite/iot-suite-v1-raspberry-pi-kit-get-started.md)
##### 使用 C
###### [模拟数据](../iot-suite/iot-suite-v1-raspberry-pi-kit-c-get-started-simulator.md)
###### [使用实际传感器](../iot-suite/iot-suite-v1-raspberry-pi-kit-c-get-started-basic.md)
###### [实施固件更新](../iot-suite/iot-suite-v1-raspberry-pi-kit-c-get-started-advanced.md)
##### 使用 Node.js
###### [模拟数据](../iot-suite/iot-suite-v1-raspberry-pi-kit-node-get-started-simulator.md)
###### [使用实际传感器](../iot-suite/iot-suite-v1-raspberry-pi-kit-node-get-started-basic.md)
###### [实施固件更新](../iot-suite/iot-suite-v1-raspberry-pi-kit-node-get-started-advanced.md)
#### [连接 MXChip IoT DevKit](../iot-hub/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring.md)

### 操作指南
#### 连接模拟设备
##### [Windows 上的 C](../iot-suite/iot-suite-v1-connecting-devices.md)
##### [Linux 上的 C](../iot-suite/iot-suite-v1-connecting-devices-linux.md)
##### [Node.js](../iot-suite/iot-suite-v1-connecting-devices-node.md)
#### [将逻辑应用连接到远程监视解决方案](../iot-suite/iot-suite-v1-logic-apps-tutorial.md)
#### [自定义解决方案加速器](../iot-suite/iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md)
#### [在远程监视解决方案中使用动态遥测](../iot-suite/iot-suite-v1-dynamic-telemetry.md)
#### [在远程监视解决方案中创建自定义规则](../iot-suite/iot-suite-v1-custom-rule.md)
#### [远程监视解决方案中的设备信息](../iot-suite/iot-suite-v1-remote-monitoring-device-info.md)


## 预测性维护
### [预测性维护解决方案加速器概述](iot-accelerators-predictive-overview.md)
### [预测性维护解决方案加速器演练](iot-accelerators-predictive-walkthrough.md)

## 连接工厂
### [什么是连接工厂？](iot-accelerators-connected-factory-features.md)
### [连接工厂解决方案概述](iot-accelerators-connected-factory-overview.md)
### [连接工厂解决方案演练](iot-accelerators-connected-factory-sample-walkthrough.md)

## 设备模拟
### [部署设备模拟](iot-accelerators-device-simulation-deploy.md)
### [浏览设备模拟](iot-accelerators-device-simulation-explore.md)

# 操作指南
## 远程监视
### [执行高级监视](iot-accelerators-remote-monitoring-monitor.md)
### [使用规则检测问题](iot-accelerators-remote-monitoring-automate.md)
### [集成 SIM 数据](iot-accelerators-remote-monitoring-telefonica-sim.md)
### [管理设备](iot-accelerators-remote-monitoring-manage.md)
### [排查设备问题](iot-accelerators-remote-monitoring-maintain.md)
### [了解设备模型架构](iot-accelerators-remote-monitoring-device-schema.md)
### [了解设备模型行为](iot-accelerators-remote-monitoring-device-behavior.md)
### [创建新的模拟设备](iot-accelerators-remote-monitoring-test.md)
### [使用 PowerBI 进行可视化](iot-accelerators-integrate-data-powerbi.md)
### [自定义解决方案 UI](iot-accelerators-remote-monitoring-customize.md)
### [自定义和重新部署微服务](iot-accelerators-microservices-example.md)
### [使用 CLI 进行部署](iot-accelerators-remote-monitoring-deploy-cli.md)
### [本地部署](iot-accelerators-remote-monitoring-deploy-local.md)
### [使用时序见解可视化数据](iot-accelerators-time-series-insights.md)
### [与 Data Lake 集成](iot-accelerators-integrate-data-lake.md)
### 连接物理设备
#### [Windows 上的 C](iot-accelerators-connecting-devices.md)
#### [Linux 上的 C](iot-accelerators-connecting-devices-linux.md)
#### [Node.js（通用）](iot-accelerators-connecting-devices-node.md)
#### [Raspberry Pi 上的 Node.js](iot-accelerators-connecting-pi-node.md)
#### [Raspberry Pi 上的 C](iot-accelerators-connecting-pi-c.md)
#### [MX Chip AZ3166 DevKit](iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2.md)

### 概念
#### [远程监视实现详细信息](iot-accelerators-remote-monitoring-sample-walkthrough.md)
#### [远程监视体系结构选择](iot-accelerators-remote-monitoring-architectural-choices.md)

## 连接工厂
### [为连接工厂部署网关](iot-accelerators-connected-factory-gateway-deployment.md)
### [自定义连接工厂](iot-accelerators-connected-factory-customize.md)
### [将 OPC 发布服务器用于连接工厂](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md)
### [配置连接工厂](iot-accelerators-connected-factory-configure.md)
## [azureiotsuite.com 权限](iot-accelerators-permissions.md)

# 引用
## [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
## [开发人员故障排除指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)
## [安全基础知识](/azure/iot-fundamentals/iot-security-ground-up?context=azure/iot-accelerators/rc/rc)
## [安全最佳实践](/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-accelerators/rc/rc)
## [安全体系结构](/azure/iot-fundamentals/iot-security-architecture?context=azure/iot-accelerators/rc/rc)
## [保护 IoT 部署](/azure/iot-fundamentals/iot-security-deployment?context=azure/iot-accelerators/rc/rc)

# 相关内容
## 解决方案 
### [IoT 解决方案加速器](/azure/iot-suite) 
### [IoT Central](https://docs.microsoft.com/microsoft-iot-central/) 
## 平台服务 
### [IoT 中心](/azure/iot-hub) 
### [IoT 中心设备预配服务](/azure/iot-dps)
### [IoT 服务 SDK](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-service-sdks)
### [Maps](/azure/azure-maps) 
### [时序见解](/azure/time-series-insights) 
## Microsoft Edge 
### [IoT Edge](/azure/iot-edge) 
### [IoT 设备 SDK](/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks) 
## [流分析](/azure/stream-analytics/)
## [事件中心](/azure/event-hubs/)
## [机器学习](/azure/machine-learning/)

# 资源
## [Azure 路线图](https://azure.microsoft.com/roadmap/)
## [常见问题](iot-accelerators-faq.md)
## [远程监视常见问题解答](iot-accelerators-faq-rm-v2.md)
## [连接的工厂常见问题解答](iot-accelerators-faq-cf.md)
## [堆栈溢出](https://stackoverflow.com/questions/tagged/azure-iot-suite)
## [IoT 解决方案加速器学习路径](https://azure.microsoft.com/documentation/learning-paths/iot-suite/)
