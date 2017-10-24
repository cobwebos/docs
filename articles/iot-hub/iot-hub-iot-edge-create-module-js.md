---
title: "使用 Node.js 创建 Azure IoT Edge 模块 | Microsoft Docs"
description: "本教程演示如何使用最新的 Azure IoT Edge NPM 包和 Yeoman 生成器编写 BLE 数据转换器模块。"
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.openlocfilehash: e23c4aa7bb3eb4fab18d5a13cbad28e07c18d8f2
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>使用 Node.js 创建 Azure IoT Edge 模块

本教程演示如何为 JS 中的 Azure IoT Edge 创建模块。

本教程将演练环境设置，以及如何使用最新的 Azure IoT Edge NPM 包编写 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 数据转换器模块。

## <a name="prerequisites"></a>先决条件

本部分设置 IoT Edge 模块开发环境。 内容适用于 64 位 Windows 和 64 位 Linux (Ubuntu 14+) 操作系统。

需要以下软件：
* [Git 客户端](https://git-scm.com/downloads)。
* [Node LTS](https://nodejs.org)。
* `npm install -g yo`。
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>体系结构

Azure IoT Edge 平台在很大程度上采用 [Von Neumann 体系结构](https://en.wikipedia.org/wiki/Von_Neumann_architecture)。 这意味着，整个 Azure IoT Edge 体系结构就是一个处理输入并生成输出的系统；每个模块也是一个微小的输入-输出子系统。 本教程介绍以下两个模块：

1. 一个接收 [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) 模拟信号并将其转换为 [JSON](https://en.wikipedia.org/wiki/JSON) 格式消息的模块。
2. 一个列显收到的 [JSON](https://en.wikipedia.org/wiki/JSON) 消息的模块。

下图显示了此项目的典型端到端数据流：

![三个模块之间的数据流](media/iot-hub-iot-edge-create-module/dataflow.png "输入：模拟的 BLE 模块；处理器：转换器模块；输出：列显程序模块")

## <a name="set-up-the-environment"></a>设置环境
下面介绍如何快速设置环境，以开始使用 JS 编写第一个 BLE 转换器模块。

### <a name="create-module-project"></a>创建模块项目
1. 打开命令行窗口，然后运行 `yo az-iot-gw-module`。
2. 遵照屏幕上的步骤完成模块项目初始化。

### <a name="project-structure"></a>项目结构
JS 模块项目包括以下组件：

`modules` - 自定义的 JS 模块源文件。 请将默认的 `sensor.js` 和 `printer.js` 替换为你自己的模块文件。

`app.js` - 用于启动 Edge 实例的入口文件。

`gw.config.json` - 用于自定义 Edge 要加载的模块的配置文件。

`package.json` - 模块项目的元数据信息。

`README.md` - 模块项目的基本文档。


### <a name="package-file"></a>包文件

此 `package.json` 声明模块项目所需的所有元数据信息，包括名称、版本、入口、脚本、运行时和开发依赖项。

以下代码片段演示如何对 BLE 转换器示例项目进行配置。
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>入口文件
`app.js` 定义初始化 Edge 实例的方法。 此处我们不需要进行任何更改。

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>模块的接口
可将 Azure IoT Edge 模块视为数据处理器，其作用是：接收输入、处理输入并生成输出。

输入可能是来自硬件（例如运动探测器）的数据、来自其他模块的消息，或其他任何信息（例如，计时器定期生成的随机数）。

输出类似于输入，它可能会触发硬件行为（例如，使 LED 闪烁）、向其他模块发送消息，或其他任何动作（例如，在控制台上列显信息）。

模块使用 `message` 对象互相通信。 `message` 的**内容**是一个字节数组，可以代表所需的任何类型的数据。 **属性**也会出现在 `message` 中，它们只是字符串到字符串的映射。 可将属性视为 HTTPS 请求中的标头，或文件的元数据。

若要在 JS 中开发 Azure IoT Edge 模块，需要创建一个可以实现所需方法 `receive()` 的新模块对象。 此时，还可以选择实现可选的 `create()`、`start()` 或 `destroy()` 方法。 以下代码片段演示如何创建 JS 模块对象的基架。

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>转换器模块
| 输入                    | 处理器                              | 输出                 | 源文件            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| 温度数据消息 | 分析和构造新的 JSON 消息 | 结构化 JSON 消息 | `converter.js` |

此模块是一个典型的 Azure IoT Edge 模块。 它从其他模块（硬件模块，或本例中所示的 BLE 模拟模块）接受温度消息；然后将温度消息规范化为结构化的 JSON 消息（包括追加消息 ID、设置属性来指定是否需要触发温度警报，等等）。

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>列显程序模块
| 输入                          | 处理器 | 输出                     | 源文件          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| 来自其他模块的任何消息 | 不适用       | 将消息记录到控制台 | `printer.js` |

这是一个简单的自释性模块，可在终端窗口中输出收到的消息（属性、内容）。

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>配置
运行模块之前的最后一步是配置 Azure IoT Edge 并在模块之间建立连接。

首先，需要声明 `node` 加载程序（由于 Azure IoT Edge 支持不同语言的加载程序），后续部分中该加载程序的 `name` 可能会引用它。

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

声明加载程序后，还需要声明模块。 与声明加载程序一样，其 `name` 属性也可能会引用它们。 声明模块时，需要指定每个模块应使用的加载程序（应是前面定义的加载程序）和入口点（应是模块的规范化类名）。 `simulated_device` 模块是包含在 Azure IoT Edge 核心运行时包中的本机模块。 请在 JSON 文件中包含 `args`，即使其值为 `null`。

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
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
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
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>运行模块
1. `npm install`
2. `npm start`

若要终止应用程序，请按 `<Enter>` 键。

> [!IMPORTANT]
> 不建议使用 Ctrl + C 来终止 IoT Edge 应用程序。 因为这可能会导致该进程异常终止。
