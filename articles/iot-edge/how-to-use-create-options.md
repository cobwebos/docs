---
title: 为模块写入创建选项 - Azure IoT 边缘 |微软文档
description: 如何使用部署清单中的 create 选项在运行时配置模块
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c07e161042a497a232cbd5e3f11128893a095381
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550339"
---
# <a name="how-to-configure-container-create-options-for-iot-edge-modules"></a>如何配置 IoT 边缘模块的容器创建选项

部署清单中的**createOptions**参数使您能够在运行时配置模块容器。 此参数扩展了您对模块的控制，并允许执行允许或限制模块访问主机设备资源或配置网络等任务。

IoT Edge 模块在 IoT Edge 设备上作为与 Docker 兼容的容器实现。 Docker 提供了许多用于创建容器的选项，这些选项也适用于 IoT Edge 模块。 有关详细信息，请参阅[Docker 容器创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)。

## <a name="format-create-options"></a>格式创建选项

IoT Edge 部署清单接受格式化为 JSON 的创建选项。 例如，以每个边缘Hub模块自动包含的创建选项为例：

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

此 EdgeHub 示例使用**HostConfig.PortBindings**参数将容器上的暴露端口映射到主机设备上的端口。

如果对 Visual Studio 或可视化工作室代码使用 Azure IoT 工具扩展名，则可以在**部署.template.json**文件中以 JSON 格式编写创建选项。 然后，当您使用扩展生成 IoT Edge 解决方案或生成部署清单时，它将以 IoT Edge 运行时期望的格式为您字符串化 JSON。 例如：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
```

编写创建选项的一个提示是使用`docker inspect`该命令。 作为开发过程的一部分，请使用 在本地使用`docker run <container name>`运行模块。 一旦模块以您想要的方式工作，运行`docker inspect <container name>`。 此命令以 JSON 格式输出模块详细信息。 查找您配置的参数，并复制 JSON。 例如：

[![码头检查边缘 Hub](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png)的结果](./media/how-to-use-create-options/docker-inspect-edgehub-inline-and-expanded.png#lightbox)

## <a name="common-scenarios"></a>常见方案

容器创建选项支持许多方案，但以下是构建 IoT Edge 解决方案时最常出现的方案：

* [为模块提供对主机存储的访问权限](how-to-access-host-storage-from-module.md)
* [将主机端口映射到模块端口](#map-host-port-to-module-port)
* [限制模块内存和 CPU 使用率](#restrict-module-memory-and-cpu-usage)

### <a name="map-host-port-to-module-port"></a>将主机端口映射到模块端口

如果您的模块需要与 IoT Edge 解决方案外部的服务通信，并且不使用消息路由来执行此操作，则需要将主机端口映射到模块端口。

>[!TIP]
>在同一设备上的模块到模块通信不需要此端口映射。 如果模块 A 需要查询模块 B 上托管的 API，则可以在没有任何端口映射的情况下执行此操作。 模块 B 需要公开其 docker 文件中的端口，例如`EXPOSE 8080`： 。 然后模块 A 可以使用模块 B 的名称查询 API，例如： `http://ModuleB:8080/api`。

首先，确保模块内的端口公开以侦听连接。 您可以使用 docker 文件中的[EXPOSE](https://docs.docker.com/engine/reference/builder/#expose)指令执行此操作。 例如，`EXPOSE 8080` 。 如果未指定，则公开指令默认为 TCP 协议，或者您可以指定 UDP。

然后，使用 Docker 容器**的 HostConfig**组中的**PortBindings**设置[创建选项](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)，将模块中的外露端口映射到主机设备上的端口。 例如，如果在模块内公开端口 8080，并希望将端口映射到主机设备的端口 80，则模板.json 文件中的创建选项类似于以下示例：

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

为部署清单进行字符串化后，相同的配置将类似于以下示例：

```json
"createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8080/tcp\":[{\"HostPort\":\"80\"}]}}}"
```

### <a name="restrict-module-memory-and-cpu-usage"></a>限制模块内存和 CPU 使用率

您可以声明模块可以使用的主机资源量。 此控件有助于确保一个模块不会消耗过多的内存或 CPU 使用率，并防止其他进程在设备上运行。 您可以使用[Docker 容器在](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate) **HostConfig**组中创建选项来管理这些设置，包括：

* **内存**： 内存限制（以字节为单位）。 例如，268435456 字节 = 256 MB。
* **内存交换**：总内存限制（内存 + 交换）。 例如，536870912 字节 = 512 MB
* **Cpu 周期**：CPU 周期的长度（以微秒为单位）。 默认值为 100000，因此，例如，值 25000 将容器限制为 CPU 资源的 25%。

在模板.json 格式中，这些值类似于以下示例：

```json
"createOptions": {
  "HostConfig": {
    "Memory": 268435456,
    "MemorySwap": 536870912,
    "CpuPeriod": 25000
  }
}
```

为最终部署清单进行字符串化后，这些值将类似于以下示例：

```json
"createOptions":"{\"HostConfig\":{\"Memory\":268435456,\"MemorySwap\":536870912,\"CpuPeriod\":25000}}"
```

## <a name="next-steps"></a>后续步骤

有关创建操作选项的更多示例，请参阅以下 IoT 边缘示例：

* [树莓派 3 上的自定义视觉和 Azure IoT 边缘](https://github.com/Azure-Samples/Custom-vision-service-iot-edge-raspberry-pi)
* [Azure IoT 边缘 Blob 存储示例](https://github.com/Azure-Samples/azure-iotedge-blobstorage-sample)
