---
title: 为模块编写 createOptions - Azure IoT Edge | Microsoft Docs
description: 如何使用部署清单中的 createOptions 在运行时配置模块
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "80550339"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>如何配置 IoT Edge 模块的容器创建选项

使用部署清单中的 createOptions 参数可以在运行时配置模块容器  。 此参数扩展了你对模块的控制，可用于执行如下所述的任务：允许或限制模块对主机设备资源的访问，或配置网络。

IoT Edge 模块在 IoT Edge 设备上作为 Docker 兼容的容器实现。 Docker 提供许多用于创建容器的选项，而这些选项也适用于 IoT Edge 模块。 有关详细信息，请参阅 [Docker 容器创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)。

## <a name="format-create-options"></a>设置创建选项的格式

IoT Edge 部署清单接受 JSON 格式的创建选项。 以每个 edgeHub 模块自动包含的创建选项为例：

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

此 edgeHub 示例使用 HostConfig.PortBindings 参数将容器上公开的端口映射到主机设备上的端口  。

如果使用适用于 Visual Studio 或 Visual Studio Code 的 Azure IoT Tools 扩展，可以在 deployment.template.json 文件中以 JSON 格式编写创建选项  。 然后，当你使用扩展来生成 IoT Edge 解决方案或生成部署清单时，该扩展会以 IoT Edge 运行时预期的格式将 JSON 字符串化。 例如：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

编写创建选项时，一个技巧是使用 `docker inspect` 命令。 在开发过程中，使用 `docker run <container name>` 在本地运行模块。 在模块按预期方式运行后，运行 `docker inspect <container name>`。 此命令以 JSON 格式输出模块详细信息。 找到配置的参数，并复制 JSON。 例如：

[ ![在 Docker 中检查 edgeHub 后的结果](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png) ](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>常见方案

容器创建选项可以实现许多方案，下面是在生成 IoT Edge 解决方案时最常用的一些方案：

* [授予模块对主机存储的访问权限](how-to-access-host-storage-from-module.md)
* [将主机端口映射到模块端口](#map-host-port-to-module-port)
* [限制模块内存和 CPU 使用率](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>将主机端口映射到模块端口

如果模块需要与 IoT Edge 解决方案外部的服务通信，但不使用消息路由进行通信，你需要将主机端口映射到模块端口。

>[!TIP]
>对于同一设备上的模块到模块通信，不需要进行这种端口映射。 如果模块 A 需要查询模块 B 上托管的 API，无需进行任何端口映射即可实现此目的。 模块 B 需在其 dockerfile 中公开一个端口，例如：`EXPOSE 8080`。 然后模块 A 可以使用模块 B 的名称来查询 API，例如：`http://ModuleB:8080/api`。

首先，请确保已公开模块中的某个端口来侦听连接。 为此，可以在 dockerfile 中使用 [EXPOSE](https://docs.docker.com/engine/reference/builder/#expose) 指令。 例如，`EXPOSE 8080`。 如果未指定 expose 指令，则其默认值为 TCP 协议；你也可以指定 UDP。

然后，在 [Docker 容器创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)的 HostConfig 组中使用 PortBindings 设置，将模块中公开的端口映射到主机设备上的端口   。 例如，如果在模块中公开了端口 8080，并想要将其映射到主机设备的端口 80，则 template.json 文件中的创建选项将如以下示例所示：

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

将部署清单字符串化后，相同的配置将如以下示例所示：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>限制模块内存和 CPU 使用率

可以声明一个模块可以使用的主机资源量。 这种控制有助于确保一个模块不能使用过多的内存或 CPU，并防止其他进程在设备上运行。 可以在 HostConfig 组中使用 [Docker 容器创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)来管理这些设置，包括  ：

* **内存**：内存限制（字节）。 例如，268435456 字节 = 256 MB。
* **MemorySwap**：总内存限制（内存 + 交换）。 例如，536870912 字节 = 512 MB
* **CpuPeriod**：CPU 周期长度（微秒）。 默认值为 100000。因此，举例来说，如果值为 25000，则会将容器的限制设置为 CPU 资源的 25%。

采用 template.json 格式时，这些值如以下示例所示：

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

将最终部署清单字符串化后，这些值如以下示例所示：

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>后续步骤

有关创建选项的运作方式的更多示例，请参阅以下 IoT Edge 示例：

* [Raspberry Pi 3 上的自定义视觉和 Azure IoT Edge](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT Edge Blob 存储示例](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
