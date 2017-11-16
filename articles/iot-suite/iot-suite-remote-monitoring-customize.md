---
title: "自定义远程监视解决方案 - Azure | Microsoft Docs"
description: "本文提供有关如何访问远程监视预配置解决方案的源代码的信息。"
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 11/10/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 886a4412ac348869563a03d697f4363cb3dea8f9
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2017
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>自定义远程监视预配置解决方案

本文提供有关如何访问远程监视预配置解决方案的源代码以及对其进行自定义的信息。 本文介绍：

* GitHub 存储库，其中包含构成预配置解决方案的微服务的源代码和资源。
* 常见的自定义方案，例如添加新设备类型。

## <a name="project-overview"></a>项目概述

### <a name="implementations"></a>实现形式

远程监视解决方案具有 .NET 和 Java 实现形式。 这两种实现形式提供类似的功能，它们依赖于相同的底层 Azure 服务。 可在以下网页中找到顶层 GitHub 存储库：

* [.NET 解决方案](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java 解决方案](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>微服务

如果对解决方案的特定功能感兴趣，可以访问每个微服务的 GitHub 存储库。 每个微服务实现解决方案功能的不同部分。 若要详细了解总体体系结构，请参阅[远程监视预配置解决方案体系结构](iot-suite-remote-monitoring-sample-walkthrough.md)。

下表汇总了每种语言的每个微服务的当前可用性：

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| 微服务      | 说明 | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Web UI            | 远程监视解决方案的 Web 应用。 使用 React.js 框架实现 UI。 | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/A(React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| IoT 中心管理器   | 处理与 IoT 中心之间的通信。        | [可用](https://github.com/Azure/iothub-manager-java) | [可用](https://github.com/Azure/iothub-manager-dotnet)   |
| 身份验证    |  管理 Azure Active Directory 集成。  | 目前不可用 | [可用](https://github.com/Azure/pcs-auth-dotnet)   |
| 设备模拟 | 管理模拟设备池。 | 目前不可用 | [可用](https://github.com/Azure/device-simulation-dotnet)   |
| 遥测         | 在 UI 中提供设备遥测数据。 | [可用](https://github.com/Azure/device-telemetry-java) | [可用](https://github.com/Azure/device-telemetry-dotnet)   |
| 遥测代理   | 分析遥测流、存储来自 Azure IoT 中心的消息，并根据定义的规则生成警报。  | [可用](https://github.com/Azure/telemetry-agent-java) | [可用](https://github.com/Azure/telemetry-agent-dotnet)   |
| UI 配置         | 管理来自 UI 的配置数据。 | [可用](https://github.com/azure/pcs-ui-config-java) | [可用](https://github.com/azure/pcs-ui-config-dotnet)   |
| 存储适配器   |  管理与存储服务之间的交互。   | [可用](https://github.com/azure/pcs-storage-adapter-java) | [可用](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| 反向代理     | 通过唯一的终结点以托管方式公开专用资源。 | 目前不可用 | [可用](https://github.com/Azure/reverse-proxy-dotnet)   |

Java 解决方案目前使用 .NET 身份验证、模拟和反向代理微服务。 在 Java 版本推出后，这些版本即会取代这些微服务。

## <a name="presentation-and-visualization"></a>呈现和可视化

以下部分介绍用于自定义远程监视解决方案中的呈现和可视化层的选项：

### <a name="change-the-logo-in-the-ui"></a>更改 UI 中的徽标

默认部署在 UI 中使用 Contoso 公司的名称和徽标。 若要更改这些 UI 元素以显示自己公司的名称和徽标，请执行以下操作：

1. 使用以下命令克隆 Web UI 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. 若要更改公司名称，请在文本编辑器中打开 `src/common/lang.js` 文件。

1. 在该文件中找到以下行：

    ```js
    CONTOSO: 'Contoso',
    ```

1. 将 `Contoso` 替换为自己公司的名称。 例如：

    ```js
    CONTOSO: 'YourCo',
    ```

1. 保存文件。

1. 若要更新徽标，请在 `assets/icons` 文件夹中添加一个新的 SVG 文件。 现有的徽标为 `assets/icons/Contoso.svg` 文件。

1. 在文本编辑器中打开 `src/components/layout/leftNav/leftNav.js` 文件。

1. 在该文件中找到以下行：

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. 将 `Contoso.svg` 替换为徽标文件的名称。 例如：

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. 在该文件中找到以下行：

    ```js
    alt="ContosoIcon"
    ```

1. 将 `ContosoIcon` 替换为 `alt` 文本。 例如：

    ```js
    alt="YourCoIcon"
    ```

1. 保存文件。

1. 若要测试更改，可在本地计算机上运行更新的 `webui`。 若要了解如何在本地生成和运行 `webui` 解决方案，请参阅 `webui` GitHub 存储库自述文件中的[在本地生成、运行和测试](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally)。

1. 若要部署更改，请参阅[开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)。

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>自定义映射

请参阅 GitHub 中的[自定义映射](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map)页，了解解决方案中映射组件的详细信息。

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>其他自定义选项

若要进一步修改远程监视解决方案中的呈现和可视化层，可以编辑代码。 相关的 GitHub 存储库包括：

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure PCS 远程监视 WebUI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>设备连接和流式处理

以下部分介绍用于自定义远程监视解决方案中的设备连接和流式处理层的选项。 [设备模型](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models)描述解决方案中的设备类型和遥测。 可对模拟和物理设备使用设备模型。

有关物理设备实现形式的示例，请参阅[将设备连接到远程监视预配置解决方案](iot-suite-connecting-devices-node.md)。

如果使用物理设备，必须提供一个带有设备模型的客户端应用程序，该模型包含设备元数据和遥测规范。

以下部分介绍如何对模拟设备使用设备模型：

### <a name="add-a-telemetry-type"></a>添加遥测类型

Contoso 演示解决方案中的设备类型指定每个设备类型发送的遥测数据。 若要指定其他遥测类型，设备可将遥测定义作为元数据发送到解决方案。 如果使用此格式，仪表板会动态使用设备遥测和可用方法，且无需修改 UI。 或者，可以修改解决方案中的设备类型定义。

若要了解如何在设备模拟器微服务中添加自定义遥测，请参阅[使用模拟设备测试解决方案](iot-suite-remote-monitoring-test.md)。

### <a name="add-a-device-type"></a>添加设备类型

Contoso 演示解决方案定义了一些示例设备类型。 在该解决方案中，可以根据具体的应用程序要求自定义设备类型。 例如，公司可将工业网关用作与解决方案连接的主要设备。

若要创建设备的准确表示形式，需要修改设备上运行的应用程序，使之与设备要求相符。

若要了解如何在设备模拟器微服务中添加新的设备类型，请参阅[使用模拟设备测试解决方案](iot-suite-remote-monitoring-test.md)。

### <a name="define-custom-methods-for-simulated-devices"></a>定义模拟设备的自定义方法

若要了解如何在远程监视解决方案中定义模拟设备的自定义方法，请参阅 GitHub 存储库中的[设备模型](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models)。

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>使用物理设备

若要在物理设备上实现方法和作业，请参阅以下 IoT 中心文章：

* [了解以及从 IoT 中心调用直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。
* [在多台设备上计划作业](../iot-hub/iot-hub-devguide-jobs.md)。

### <a name="other-customization-options"></a>其他自定义选项

若要进一步修改远程监视解决方案中的设备连接和流式处理层，可以编辑代码。 相关的 GitHub 存储库包括：

* [设备遥测 (.NET)](https://github.com/Azure/device-telemetry-dotnet)
* [设备遥测 (Java)](https://github.com/Azure/device-telemetry-java)
* [遥测代理 (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [遥测代理 (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="data-processing-and-analytics"></a>数据处理和分析

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

若要修改远程监视解决方案中的数据处理和分析层，可以编辑代码。 相关的 GitHub 存储库包括：

* [遥测代理 (.NET)](https://github.com/Azure/telemetry-agent-dotnet)
* [遥测代理 (Java)](https://github.com/Azure/telemetry-agent-java)

## <a name="infrastructure"></a>基础结构

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

若要修改远程监视解决方案中的基础结构，可以编辑代码。 相关的 GitHub 存储库包括：

* [IoT 中心管理器 (.NET)](https://github.com/Azure/iothub-manager-dotnet)
* [IoT 中心管理器 (Java)](https://github.com/Azure/iothub-manager-java)
* [存储适配器 (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet)
* [存储适配器 (Java)](https://github.com/Azure/pcs-storage-adapter-java)

## <a name="next-steps"></a>后续步骤

本文已介绍可用来帮助自定义预配置解决方案的资源。

有关远程监视预配置解决方案的其他概念性信息，请参阅[远程监视体系结构](iot-suite-remote-monitoring-sample-walkthrough.md)

有关自定义远程监视解决方案的详细信息，请参阅：

* [开发人员参考指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [开发人员故障排除指南](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->