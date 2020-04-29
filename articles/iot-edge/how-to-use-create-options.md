---
title: 为模块编写 createOptions-Azure IoT Edge |Microsoft Docs
description: 如何在部署清单中使用 createOptions 在运行时配置模块
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80550339"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>如何为 IoT Edge 模块配置容器创建选项

部署清单中的**createOptions**参数使你能够在运行时配置模块容器。 此参数可在模块中展开控制，并允许执行或限制模块访问主机设备资源或配置网络等任务。

IoT Edge 模块作为 IoT Edge 设备上的 Docker 兼容容器实现。 Docker 提供了许多用于创建容器的选项，这些选项也适用于 IoT Edge 模块。 有关详细信息，请参阅[Docker 容器创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)。

## <a name="format-create-options"></a>格式创建选项

IoT Edge 部署清单接受格式为 JSON 的 create 选项。 例如，采用自动包含在每个 edgeHub 模块中的 "创建" 选项：

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "5671/tcp": [
        {
          "HostPort": "5671"
        }
      ],
      "8883/tcp": [
        {
          "HostPort": "8883"
        }
      ],
      "443/tcp": [
        {
          "HostPort": "443"
        }
      ]
    }
  }
}
```

此 edgeHub 示例使用**PortBindings**参数将容器上已公开的端口映射到主机设备上的端口。

如果你使用适用于 Visual Studio 或 Visual Studio Code 的 Azure IoT 工具扩展，则可以在**部署. 模板 json**文件中以 JSON 格式编写创建选项。 然后，当你使用该扩展来生成 IoT Edge 解决方案或生成部署清单时，它将采用 IoT Edge 运行时期望的格式为你 json.stringify JSON。 例如：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

编写创建选项的一个提示是使用`docker inspect`命令。 作为开发过程的一部分，请使用`docker run <container name>`在本地运行模块。 模块按照所需的方式工作后，运行`docker inspect <container name>`。 此命令以 JSON 格式输出模块详细信息。 找到配置的参数，并复制 JSON。 例如：

[![Docker 的结果检查 edgeHub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>常见方案

容器创建选项可实现许多方案，但以下是生成 IoT Edge 解决方案时最常遇到的一些情况：

* [授予模块访问主机存储的权限](how-to-access-host-storage-from-module.md)
* [将主机端口映射到模块端口](#map-host-port-to-module-port)
* [限制模块内存和 CPU 使用率](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>将主机端口映射到模块端口

如果模块需要与 IoT Edge 解决方案外部的服务通信，并且未使用消息路由来执行此操作，则需要将主机端口映射到模块端口。

>[!TIP]
>对于同一设备上的模块到模块通信，不需要此端口映射。 如果模块 A 需要查询模块 B 上托管的 API，则无需任何端口映射即可执行此操作。 模块 B 需要在其 dockerfile 中公开端口，例如： `EXPOSE 8080`。 然后模块 A 可以使用模块 B 的名称查询 API，例如： `http://ModuleB:8080/api`。

首先，请确保已公开模块内的端口来侦听连接。 可以使用 dockerfile 中的[公开](https://docs.docker.com/engine/reference/builder/#expose)指令来执行此操作。 例如，`EXPOSE 8080` 。 如果未指定，则公开指令默认为 TCP 协议，或者你可以指定 UDP。

然后，使用 Docker 容器的**HostConfig**组中的**PortBindings**设置，[创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)，将模块中的已公开端口映射到主机设备上的端口。 例如，如果你在模块内部公开了端口8080，并且想要将其映射到主机设备的端口80，则模板 json 文件中的 create 选项将类似于以下示例：

```json
"createOptions": {
  "HostConfig": {
    "PortBindings": {
      "8080/tcp": [
        {
          "HostPort": "80"
        }
      ]
    }
  }
}
```

一旦字符串化部署清单，相同的配置就会类似于以下示例：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>限制模块内存和 CPU 使用率

可以声明模块可以使用的主机资源数量。 此控件有助于确保一个模块不会消耗太多内存或 CPU 使用情况，并防止其他进程在设备上运行。 你可以在**HostConfig**组中通过[Docker 容器 create 选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)管理这些设置，包括：

* **内存**：内存限制（以字节为单位）。 例如，268435456字节 = 256 MB。
* **MemorySwap**：总内存限制（内存 + 交换）。 例如，536870912字节 = 512 MB
* **CpuPeriod**： CPU 时间的长度（以微秒为单位）。 默认值为100000，例如，值25000将容器限制为 CPU 资源的25%。

在模板 json 格式中，这些值类似于以下示例：

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

字符串化最终部署清单后，这些值将类似于以下示例：

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>后续步骤

有关创建选项的更多示例，请参阅以下 IoT Edge 示例：

* [Raspberry Pi 3 上的自定义视觉和 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge blob 存储示例](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
