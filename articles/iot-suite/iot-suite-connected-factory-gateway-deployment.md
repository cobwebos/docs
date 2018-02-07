---
title: "部署连接工厂网关 - Azure | Microsoft Docs"
description: "如何在 Windows 或 Linux 上部署网关，以便连接到连接工厂预配置解决方案。"
services: 
suite: iot-suite
documentationcenter: na
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 4606cb676c3ab7c8c8511579f43d251ff7d2ae8a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2018
---
# <a name="deploy-an-edge-gateway-for-the-connected-factory-preconfigured-solution-on-windows-or-linux"></a>在 Windows 或 Linux 上为连接工厂预配置解决方案部署边缘网关

需要为*连接工厂*预配置解决方案部署边缘网关的软件具有两个组件：

- *OPC 代理*与连接工厂建立连接。 OPC 代理随后等待集成 OPC 浏览器发出命令和控制消息，该浏览器在连接工厂解决方案门户中运行。

- *OPC 发布服务器*连接到现有的本地 OPC UA 服务器，并将遥测消息从服务器转发到连接工厂。 可以通过[用于 OPC UA 的 OPC 经典适配器](https://github.com/OPCFoundation/UA-.NETStandard/blob/master/ComIOP/README.md)连接 OPC 经典设备。

这两个组件为开源组件，在 GitHub 上可用作源，在 DockerHub 上可用作 Docker 容器：

| GitHub | DockerHub |
| ------ | --------- |
| [OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher) | [OPC 发布服务器](https://hub.docker.com/r/microsoft/iot-edge-opc-publisher/)   |
| [OPC 代理](https://github.com/Azure/iot-edge-opc-proxy)         | [OPC 代理](https://hub.docker.com/r/microsoft/iot-edge-opc-proxy/) |

这两个组件都不需要网关防火墙中面向公众的 IP 地址或开放式入站端口。 OPC 代理和 OPC 发布服务器组件仅使用出站端口 443。

本文中的步骤演示如何在 Windows 或 Linux 上使用 Docker 部署边缘网关。 使用网关可以连接到连接工厂预配置解决方案。 也可以使用不带连接工厂的组件。

> [!NOTE]
> 这两个组件都可用作 [Azure IoT Edge](https://github.com/Azure/iot-edge) 中的模块。

## <a name="choose-a-gateway-device"></a>选择网关设备

如果还没有网关设备，Microsoft 建议从其中一个合作伙伴处购买商业网关。 有关与连接工厂解决方案兼容的网关设备的列表，请访问 [Azure IoT 设备目录](https://catalog.azureiotsuite.com/?q=opc)。 按照设备随附的说明操作，设置网关。

或者，使用以下说明手动配置一个现有网关设备。

## <a name="install-and-configure-docker"></a>安装并配置 Docker

在基于 Windows 的网关设备上安装 [Docker for Windows](https://www.docker.com/docker-windows)，或使用包管理器在基于 Linux 的网关设备上安装 Docker。

在 Docker for Windows 安装过程中，选择主机上的驱动器，以与 Docker 共享。 以下屏幕截图显示正在共享 Windows 系统上的 **D** 盘，以允许从 Docker 容器内访问主机驱动器：

![安装 Docker for Windows](./media/iot-suite-connected-factory-gateway-deployment/image1.png)

> [!NOTE]
> 也可在从“设置”对话框安装 Docker 后，执行此步骤。 右键单击 Windows 系统托盘中的“Docker”图标，选择“设置”。 如果主要 Windows 更新已部署到系统（如 Windows Fall Creators 更新），则取消共享驱动器并再次共享它们以刷新访问权限。

如果使用的是 Linux，则无需进行其他配置就能启用对文件系统的访问。

对于 Windows，在与 Docker 共享的驱动器上创建一个文件夹；对于 Linux，则在根文件系统下创建一个文件夹。 本演练将此文件夹称作 `<SharedFolder>`。

在 Docker 命令中引用 `<SharedFolder>` 时，务必针对操作系统使用正确的语法。 下面有两个示例，一个适用于 Windows，一个适用于 Linux：

- 如果在 Windows 上将文件夹 `D:\shared` 用作 `<SharedFolder>`，则 Docker 命令语法为 `d:/shared`。

- 如果在 Linux 上将文件夹 `/shared` 用作 `<SharedFolder>`，则 Docker 命令语法为 `/shared`。

有关详细信息，请参阅[使用卷](https://docs.docker.com/engine/admin/volumes/volumes/) Docker 引擎参考。

## <a name="configure-the-opc-components"></a>配置 OPC 组件

安装 OPC 组件之前，先完成以下步骤准备好环境：

1. 若要完成网关部署，需提供连接工厂部署中 IoT 中心的 **iothubowner** 连接字符串。 在 [Azure 门户](http://portal.azure.com/)中，导航到 IoT 中心中在部署连接工厂解决方案时创建的资源组。 单击“共享访问策略”，访问 iothubowner 连接字符串：

    ![找到 IoT 中心连接字符串](./media/iot-suite-connected-factory-gateway-deployment/image2.png)

    复制“连接字符串 - 主键”值。

1. 若要允许 Docker 容器之间通信，需具有用户定义的桥接网络。 若要为容器创建桥接网络，请在命令提示符处运行以下命令：

    ```cmd/sh
    docker network create -d bridge iot_edge
    ```

    若要确保已创建 **iot_edge** 桥接网络，请运行以下命令：

    ```cmd/sh
    docker network ls
    ```

    **iot_edge** 桥接网络包含在网络列表中。

若要运行 OPC 发布服务器，请在命令提示符处运行以下命令：

```cmd/sh
docker run --rm -it -v <SharedFolder>:/docker -v x509certstores:/root/.dotnet/corefx/cryptography/x509stores --network iot_edge --name publisher -h publisher -p 62222:62222 --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-publisher:2.1.3 publisher "<IoTHubOwnerConnectionString>" --lf /docker/publisher.log.txt --as true --si 1 --ms 0 --tm true --vc true --di 30
```

- [OPC 发布服务器 GitHub](https://github.com/Azure/iot-edge-opc-publisher) 和 [docker run reference](https://docs.docker.com/engine/reference/run/)（Docker 运行参考）针对以下内容提供了详细信息：

  - 在容器名称 (`microsoft/iot-edge-opc-publisher:2.1.3`) 前面指定的 Docker 命令行选项。
  - 在容器名称 (`microsoft/iot-edge-opc-publisher:2.1.3`) 后面指定的 OPC 发布服务器命令行参数的含义。

- `<IoTHubOwnerConnectionString>` 是来自 Azure 门户的 **iothubowner** 共享访问策略连接字符串。 已在前面步骤中复制此连接字符串。 仅在首次运行 OPC 发布服务器时才需要此连接字符串。 后续运行时，应省略此字符串，因为它会带来安全风险。

- [安装并配置 Docker](#install-and-configure-docker) 部分中介绍了所用的 `<SharedFolder>` 及其语法。 OPC 发布服务器使用 `<SharedFolder>` 来读写 OPC 发布服务器配置文件，写入日志文件，并使这些文件在容器外可用。

- OPC 发布服务器从 publishednodes.json 文件中读取其配置，它是从 `<SharedFolder>/docker` 文件夹读取并写入该文件夹。 此配置文件定义 OPC 发布服务器应订阅给定 OPC UA 服务器上的哪些 OPC UA 节点数据。 GitHub 上的 [OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher)页介绍了 **publishednodes.json** 文件的完整语法。 添加网关时，把一个空的 publishednodes.json 放到文件夹中：

    ```json
    [
    ]
    ```

- 每当 OPC UA 服务器向 OPC 发布服务器发送数据更改通知时，都会向 IoT 中心发送新值。 根据批处理设置，OPC 发布服务器可能会先收集数据，然后再将数据批量发送到 IoT 中心。

- Docker 不支持 NetBIOS 名称解析，仅支持 DNS 名称解析。 如果网络上没有 DNS 服务器，可以使用上一命令行示例中所示的解决方法。 上一命令行示例使用 `--add-host` 参数向容器主机文件中添加条目。 此条目允许主机名查找给定的 `<OpcServerHostname>`，并解析为给定的 IP 地址 `<IpAddressOfOpcServerHostname>`。

- OPC UA 使用 X.509 证书进行身份验证和加密。 需在要连接的 OPC UA 服务器上放置 OPC 发布服务器证书，以确保它信任 OPC 发布服务器。 OPC 发布服务器证书存储位于 `<SharedFolder>/CertificateStores` 文件夹中。 可在 `CertificateStores` 文件夹的 `trusted/certs` 文件夹中找到 OPC 发布服务器证书。

  OPC UA 服务器配置步骤取决于所使用的设备。 OPC UA 服务器用户手册中通常记录了这些步骤。

若要安装 OPC 代理，请在命令提示符处运行以下命令：

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db
```

只需在系统上运行一次安装。

使用以下命令运行 OPC 代理：

```cmd/sh
docker run -it --rm -v <SharedFolder>:/mapped --network iot_edge --name proxy --add-host <OpcServerHostname>:<IpAddressOfOpcServerHostname> microsoft/iot-edge-opc-proxy:1.0.2 -D /mapped/cs.db
```

OPC 代理会在安装期间保存连接字符串。 后续运行时，应省略此连接字符串，因为它会带来安全风险。

## <a name="enable-your-gateway"></a>启用网关

完成以下步骤，以便在连接工厂预配置解决方案中启用网关：

1. 当两个组件都在运行时，浏览到连接工厂解决方案门户中的“连接自己的 OPC UA 服务器”页面。 只有解决方案中的管理员才能使用此页面。 输入发布服务器终结点 URL (opc.tcp://publisher:62222)，单击“连接”。

1. 在连接工厂门户与 OPC 发布服务器之间建立信任关系。 显示证书警告时单击“继续”。 接下来，将显示 OPC 发布服务器不信任 UA Web 客户端的错误。 若要解决此错误，请将 **UA Web 客户端**证书从网关上的 `<SharedFolder>/CertificateStores/rejected/certs` 文件夹复制到 `<SharedFolder>/CertificateStores/trusted/certs` 文件夹。 无需重启网关。

现可从云连接到网关，并准备好将 OPC UA 服务器添加到解决方案中。

## <a name="add-your-own-opc-ua-servers"></a>添加自己的 OPC UA 服务器

若要将自己的 OPC UA 服务器添加到连接工厂预配置解决方案中，请执行以下操作：

1. 浏览到连接工厂解决方案门户中的“连接自己的 OPC UA 服务器”页面。

    1. 启动想要连接的 OPC UA 服务器。 确保可以通过容器中运行的 OPC 发布服务器和 OPC 代理来访问 OPC UA 服务器（请参阅之前有关名称解析的注释）。
    1. 输入 OPC UA 服务器 (`opc.tcp://<host>:<port>`) 的终结点 URL，然后单击“连接”。
    1. 作为连接设置的一部分，已建立连接工厂门户（OPC UA 客户端）和尝试连接的 OPC UA 服务器之间的信任关系。 在连接的工厂仪表板中，将显示“无法验证要连接的服务器的证书”警告。 显示证书警告时单击“继续”。
    1. 更难以设置的是尝试连接的 OPC UA 服务器的证书配置。 对于基于 PC 的 OPC UA 服务器，可能会在仪表板中显示一个你可确认的警告对话框。 对于嵌入式 OPC UA 服务器系统，请参阅 OPC UA 服务器的文档以查看完成该任务的方式。 若要完成此任务，可能需要连接工厂门户的 OPC UA 客户端的证书。 管理员可以在**连接你自己的 OPC UA 服务器**页面上下载此证书：

        ![解决方案门户](./media/iot-suite-connected-factory-gateway-deployment/image4.png)

1. 浏览 OPC UA 服务器的 OPC UA 节点树，右键单击要将值发送到连接工厂的 OPC 节点，并选择“发布”。

1. 遥测现从网关设备流入。 可在连接工厂门户中“新工厂”下的“工厂位置”视图中查看此遥测。

## <a name="next-steps"></a>后续步骤

若要深入了解预连接工厂预配置解决方案的体系结构，请参阅[连接工厂预配置解决方案演练](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-sample-walkthrough)。

了解 [OPC 发布服务器引用实现](https://docs.microsoft.com/azure/iot-suite/iot-suite-connected-factory-publisher)。