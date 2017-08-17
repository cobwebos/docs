---
title: "使用 Java 创建 Azure IoT Edge 模块 | Microsoft Docs"
description: "本教程演示如何使用最新的 Azure IoT Edge Maven 包编写 BLE 数据转换器模块。"
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 682feb4d889ecd881abe1a70d36e0a5a4df3d910
ms.contentlocale: zh-cn
ms.lasthandoff: 07/04/2017

---
# <a name="create-an-azure-iot-edge-module-with-java"></a>使用 Java 创建 Azure IoT Edge 模块

本教程演示如何为 Java 中的 Azure IoT Edge 构建模块。

本教程将演练环境设置，以及如何使用最新的 Azure IoT Edge Maven 包编写 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 数据转换器模块。

## <a name="prerequisites"></a>先决条件

本部分将设置 IoT Edge 模块开发环境。 内容适用于 64 位 Windows 和 64 位 Linux (Ubuntu/Debian 8) 操作系统。

需要以下软件：

* [Git 客户端](https://git-scm.com/downloads)。
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。
* [Maven](https://maven.apache.org/install.html)。

打开命令行终端窗口并克隆以下存储库：

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`。
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>整体体系结构

Azure IoT Edge 平台在很大程度上采用 [Von Neumann 体系结构](https://en.wikipedia.org/wiki/Von_Neumann_architecture)。 这意味着，整个 Azure IoT Edge 体系结构就是一个处理输入并生成输出的系统；每个模块也是一个微小的输入-输出子系统。 本教程将介绍以下两个模块：

1. 一个接收 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 模拟信号并将其转换为 [JSON](https://en.wikipedia.org/wiki/JSON) 格式消息的模块。
2. 一个列显收到的 [JSON](https://en.wikipedia.org/wiki/JSON) 消息的模块。

下图显示了此项目的典型端到端数据流：

![三个模块之间的数据流](media/iot-hub-iot-edge-create-module/dataflow.png "输入：模拟的 BLE 模块；处理器：转换器模块；输出：列显程序模块")

## <a name="understanding-the-code"></a>了解数据

### <a name="maven-project-structure"></a>Maven 项目结构

由于 Azure IoT Edge 包基于 Maven，因此我们需要创建一个包含 `pom.xml` 文件的典型 Maven 项目结构。

POM 继承自 `com.microsoft.azure.gateway.gateway-module-base` 包，该包声明包含运行时二进制文件、网关配置文件路径和执行行为的模块项目所需的所有依赖项。 这样就可以节省大量的时间，并且无需反复编写和重写数百个代码行。

我们需要根据以下代码片段中所示，通过声明所需的依赖项/插件以及模块要使用的配置文件的名称，来更新 pom.xml 文件。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>基本了解 Azure IoT Edge 模块

可将 Azure IoT Edge 模块视为数据处理器，其作用是：接收输入、处理输入并生成输出。

输入可能是来自硬件（例如运动探测器）的数据、来自其他模块的消息，或其他任何信息（例如，计时器定期生成的随机数）。

输出类似于输入，它可能会触发硬件行为（例如，使 LED 闪烁）、向其他模块发送消息，或其他任何动作（例如，在控制台上列显信息）。

模块使用 `com.microsoft.azure.gateway.messaging.Message` 类互相通信。 `Message` 的**内容**是一个字节数组，可以代表所需的任何类型的数据。 **属性**也会出现在 `Message` 中，它们只是字符串到字符串的映射。 可将**属性**视为 HTTP 请求中的标头，或文件的元数据。

若要在 Java 中开发 Azure IoT Edge 模块，需要创建一个继承自 `com.microsoft.azure.gateway.core.GatewayModule` 的新模块类，并实现所需的抽象方法 `receive()` 和 `destroy()`。 此时，还可以选择实现可选的 `start()` 或 `create()` 方法。 以下代码片段演示如何开始创作 Azure IoT Edge 模块。

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>转换器模块

| 输入                    | 处理器                              | 输出                 | 源文件            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 温度数据消息 | 分析和构造新的 JSON 消息 | 结构化 JSON 消息 | `ConverterModule.java` |

此模块是一个典型的 Azure IoT Edge 模块。 它从其他模块（硬件模块，或本例中所示的 BLE 模拟模块）接受温度消息；然后将温度消息规范化为结构化的 JSON 消息（包括追加消息 ID、设置属性来指定是否需要触发温度警报，等等）。

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>列显程序模块

| 输入                          | 处理器 | 输出                     | 源文件          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 来自其他模块的任何消息 | 不适用       | 将消息记录到控制台 | `PrinterModule.java` |

这是一个简单的自释性模块，可在终端窗口中输出收到的消息。

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Azure IoT Edge 配置

运行模块之前的最后一步是配置 Azure IoT Edge 并在模块之间建立连接。

首先，需要声明 Java 加载程序（由于 Azure IoT Edge 支持不同语言的加载程序），后续部分中该加载程序的 `name` 可能会引用它。

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

声明加载程序后，还需要声明模块。 与声明加载程序一样，其 `name` 属性也可能会引用它们。 声明模块时，需要指定每个模块应使用的加载程序（应是前面定义的加载程序）和入口点（应是模块的规范化类名）。 `simulated_device` 模块是包含在 Azure IoT Edge 核心运行时包中的本机模块。 始终应在 JSON 文件中包含 `args`，即使其值为 `null`。

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

配置结束时，让我们建立连接。 每个连接由 `source` 和 `sink` 表示。 它们都应该引用预定义的模块。 `source` 模块的输出消息将转发到 `sink` 模块的输入。

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>运行模块

使用 `mvn package` 在 `target/` 文件夹中生成所有内容。 此外，建议使用 `mvn clean package`，以生成纯净的版本。

使用 `mvn exec:exec` 运行 Azure IoT Edge，此时，你应会发现，温度数据和所有属性将以固定的频率列显在控制台上。

若要终止应用程序，请按 `<Enter>` 键。

> [!IMPORTANT]
> 不建议使用 Ctrl + C 来终止 IoT Edge 网关应用程序， 因为这样做可能会导致该进程异常终止。


