---
title: "使用连接了 Azure IoT 套件的工厂 OPC 发布服务器 | Microsoft Docs"
description: "如何生成和部署连接的工厂 OPC 发布服务器引用实现。"
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
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 934f4deb3139c136c871ab0125ba45267e1d1b05
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2017
---
# <a name="opc-publisher-for-azure-iot-edge"></a>Azure IoT Edge 的 OPC 发布服务器

本文将介绍如何使用 OPC 发布服务器引用实现。 引用实现将演示如何使用 Azure IoT Edge 执行以下操作：

- 连接到现有的 OPC UA 服务器。
- 使用 JSON 有效负载将这些服务器中采用 OPC UA Pub/Sub 格式的 JSON 编码遥测数据发布到 Azure IoT 中心。 可以使用 Azure IoT Edge 支持的任何传输协议。

此引用应用程序包括：

- 用于连接到网络上现有 OPC UA 服务器的 OPC UA 客户端。
- 在可用于管理发布内容的端口 62222 上进行侦听的 OPC UA 服务器。

应用程序使用 .NET Core 实现，并可以在 .NET Core 所支持的平台上运行。

当发布服务器建立到终结点的连接时，它将实现重试逻辑。 发布服务器需要终结点在指定数量的保持活动状态的请求内响应。 利用此重试逻辑，发布服务器可以检测特定情况，例如，OPC UA 服务器断电情况。

对于 OPC UA 服务器的每个不同的发布时间间隔，它将创建一个单独的订阅，将通过该订阅更新采用此发布时间间隔的所有节点。

为了减少网络负载，发布服务器支持将数据批量发送到 IoT 中心。 只有在达到配置的包大小时，才会将批数据发送到 IoT 中心。

此应用程序使用 OPC Foundation 的 OPC UA 引用堆栈，因此许可限制适用。 请访问 http://opcfoundation.github.io/UA-.NETStandardLibrary/，了解 OPC UA 文档和许可条款。

可以在 [Azure IoT Edge 的 OPC 发布服务器](https://github.com/Azure/iot-edge-opc-publisher) GitHub 存储库中找到 OPC 发布服务器源代码。

## <a name="prerequisites"></a>先决条件

要生成应用程序，操作系统将需要 [.NET Core SDK 1.1](https://docs.microsoft.com/dotnet/core/sdk) 。

## <a name="build-the-application"></a>构建应用程序

### <a name="as-native-windows-application"></a>作为本机 Windows 应用程序

使用 Visual Studio 2017 打开 `OpcPublisher.sln` 项目，然后按 F7 生成解决方案。

### <a name="as-docker-container"></a>作为 Docker 容器

若要生成应用程序作为 Windows Docker 容器，请使用 `Dockerfile.Windows` 配置文件。

若要生成应用程序作为 Linux Docker 容器，请使用 `Dockerfile` 配置文件。

通过开发计算机上存储库的根目录，在控制台窗口中键入以下命令：

`docker build -f <docker-configfile-to-use> -t <your-container-name> .`

`docker build` 的 `-f` 选项是可选选项，默认使用 `Dockerfile` 配置文件。

借助 Docker，还可以直接从 git 存储库进行生成。 可以使用以下命令生成 Linux Docker 容器：

`docker build -t <your-container-name> .https://github.com/Azure/iot-edge-opc-publisher`

## <a name="configure-the-opc-ua-nodes-to-publish"></a>配置要发布的 OPC UA 节点

若要配置应将其值发布到 Azure IoT 中心的 OPC UA 节点，请创建 JSON 格式的配置文件。 此配置文件的默认名称是 `publishednodes.json`。 在应用程序使用 OPC UA 服务器方法 PublishNode 或 UnpublishNode 时，它将更新并保存此配置文件。

此配置文件的语法如下所示：

```json
[
    {
        // example for an EnpointUrl is: opc.tcp://win10iot:51210/UA/SampleServer
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "OpcNodes": [
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised publishing interval).
            {
                // The identifier specifies the NamespaceUri and the node identifier in XML notation as specified in Part 6 of the OPC UA specification in the XML Mapping section.
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258"
            },
            // Publisher will request the server at EndpointUrl to sample the node with the OPC sampling interval specified on command line (or the default value: OPC publishing interval)
            // and the subscription will publish the node value with an OPC publishing interval of 4 seconds.
            // Publisher will use for each dinstinct publishing interval (of nodes on the same EndpointUrl) a separate subscription. All nodes without a publishing interval setting,
            // will be on the same subscription and the OPC UA stack will publish with the lowest sampling interval of a node.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                "OpcPublishingInterval": 4000
            },
            // Publisher will request the server at EndpointUrl to sample the node with the given sampling interval of 1 second
            // and the subscription will publish the node value with the OPC publishing interval specified on command line (or the default value: server revised interval).
            // If the OPC publishing interval is set to a lower value, Publisher will adjust the OPC publishing interval of the subscription to the OPC sampling interval value.
            {
                "ExpandedNodeId": "nsu=http://opcfoundation.org/UA/;i=2258",
                // the OPC sampling interval to use for this node.
                "OpcSamplingInterval": 1000
            }
        ]
    },

    // the format below is only supported for backward compatibility. you need to ensure that the
    // OPC UA server on the configured EndpointUrl has the namespaceindex you expect with your configuration.
    // please use the ExpandedNodeId syntax instead.
    {
        "EndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
        "NodeId": {
            "Identifier": "ns=0;i=2258"
        }
    }
    // please consult the OPC UA specification for details on how OPC monitored node sampling interval and OPC subscription publishing interval settings are handled by the OPC UA stack.
    // the publishing interval of the data to Azure IoT Hub is controlled by the command line settings (or the default: publish data to IoT Hub at least each 1 second).
]
```

## <a name="run-the-application"></a>运行应用程序

### <a name="command-line-options"></a>命令行选项

若要查看应用程序的完整使用情况，请使用 `--help` 命令行选项。 以下示例将演示命令的结构：

```cmd/sh
OpcPublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]
```

`applicationname` 是要使用的 OPC UA 应用程序名称。 此参数是必需的。 应用程序名称还用于在 IoT 中心设备注册表中注册发布服务器。

`IoT Hubconnectionstring` 是 IoT 中心所有者连接字符串。 此参数是可选的。

可以使用以下选项：

```cmd/sh
--pf, --publishfile=VALUE
  the filename to configure the nodes to publish.
    Default: './publishednodes.json'
--sd, --shopfloordomain=VALUE
  the domain of the shopfloor. if specified this
    domain is appended (delimited by a ':' to the '
    ApplicationURI' property when telemetry is
    sent to IoT Hub.
    The value must follow the syntactical rules of a
    DNS hostname.
    Default: not set
--sw, --sessionconnectwait=VALUE
  specify the wait time in seconds publisher is
    trying to connect to disconnected endpoints and
    starts monitoring unmonitored items
    Min: 10
    Default: 10
--vc, --verboseconsole=VALUE
  the output of publisher is shown on the console.
    Default: False
--ih, --IoT Hubprotocol=VALUE
  the protocol to use for communication with Azure
    IoT Hub (allowed values: Amqp, Http1, Amqp_
    WebSocket_Only, Amqp_Tcp_Only, Mqtt, Mqtt_
    WebSocket_Only, Mqtt_Tcp_Only).
    Default: Mqtt
--ms, --IoT Hubmessagesize=VALUE
  the max size of a message which can be send to
    IoT Hub. when telemetry of this size is available
    it will be sent.
    0 will enforce immediate send when telemetry is
    available
    Min: 0
    Max: 256 * 1024
    Default: 4096
--si, --IoT Hubsendinterval=VALUE
  the interval in seconds when telemetry should be
    send to IoT Hub. If 0, then only the
    IoT Hubmessagesize parameter controls when
    telemetry is sent.
    Default: '1'
--lf, --logfile=VALUE
  the filename of the logfile to use.
    Default: './Logs/<applicationname>.log.txt'
--pn, --portnum=VALUE
  the server port of the publisher OPC server
    endpoint.
    Default: 62222
--pa, --path=VALUE
  the endpoint URL path part of the publisher OPC
    server endpoint.
    Default: '/UA/Publisher'
--lr, --ldsreginterval=VALUE
  the LDS(-ME) registration interval in ms. If 0,
    then the registration is disabled.
    Default: 0
--ot, --operationtimeout=VALUE
  the operation timeout of the publisher OPC UA
    client in ms.
    Default: 120000
--oi, --opcsamplinginterval=VALUE
  the publisher is using this as default value in
    milliseconds to request the servers to sample
    the nodes with this interval
    this value might be revised by the OPC UA
    servers to a supported sampling interval.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a negative value will set the sampling interval
    to the publishing interval of the subscription
    this node is on.
    0 will configure the OPC UA server to sample in
    the highest possible resolution and should be
    taken with care.
    Default: 1000
--op, --opcpublishinginterval=VALUE
  the publisher is using this as default value in
    milliseconds for the publishing interval setting
    of the subscriptions established to the OPC UA
    servers.
    please check the OPC UA specification for
    details how this is handled by the OPC UA stack.
    a value less than or equal zero will let the
    server revise the publishing interval.
    Default: 0
--ct, --createsessiontimeout=VALUE
  specify the timeout in seconds used when creating
    a session to an endpoint. On unsuccessful
    connection attemps a backoff up to 5 times the
    specified timeout value is used.
    Min: 1
    Default: 10
--ki, --keepaliveinterval=VALUE
  specify the interval in seconds the publisher is
    sending keep alive messages to the OPC servers
    on the endpoints it is connected to.
    Min: 2
    Default: 2
--kt, --keepalivethreshold=VALUE
  specify the number of keep alive packets a server
    can miss, before the session is disconneced
    Min: 1
    Default: 5
--st, --opcstacktracemask=VALUE
  the trace mask for the OPC stack. See github OPC .
    NET stack for definitions.
    To enable IoT Hub telemetry tracing set it to 711.
    Default: 285  (645)
--as, --autotrustservercerts=VALUE
  the publisher trusts all servers it is
    establishing a connection to.
    Default: False
--tm, --trustmyself=VALUE
  the publisher certificate is put into the trusted
    certificate store automatically.
    Default: True
--fd, --fetchdisplayname=VALUE
  enable to read the display name of a published
    node from the server. this will increase the
    runtime.
    Default: False
--at, --appcertstoretype=VALUE
  the own application cert store type.
    (allowed values: Directory, X509Store)
    Default: 'X509Store'
--ap, --appcertstorepath=VALUE
  the path where the own application cert should be
    stored
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/own'
--tt, --trustedcertstoretype=VALUE
  the trusted cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--tp, --trustedcertstorepath=VALUE
  the path of the trusted cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/trusted'
--rt, --rejectedcertstoretype=VALUE
  the rejected cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--rp, --rejectedcertstorepath=VALUE
  the path of the rejected cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/rejected'
--it, --issuercertstoretype=VALUE
  the trusted issuer cert store type.
    (allowed values: Directory, X509Store)
    Default: Directory
--ip, --issuercertstorepath=VALUE
  the path of the trusted issuer cert store
    Default (depends on store type):
    X509Store: 'CurrentUser\UA_MachineDefault'
    Directory: 'CertificateStores/issuers'
--dt, --devicecertstoretype=VALUE
  the IoT Hub device cert store type.
    (allowed values: Directory, X509Store)
    Default: X509Store
--dp, --devicecertstorepath=VALUE
  the path of the iot device cert store
    Default Default (depends on store type):
    X509Store: 'My'
    Directory: 'CertificateStores/IoT Hub'
-h, --help
  show this message and exit
```

可以使用以下环境变量来控制应用程序：
- `_HUB_CS`：设置 IoT 中心所有者连接字符串
- `_GW_LOGP`：设置要使用的日志文件的文件名
- `_TPC_SP`：设置用于存储受信任工作站的证书的路径
- `_GW_PNFP`：设置发布配置文件的文件名

命令行自变量会否决环境变量设置。

通常情况下，仅在首次启动应用程序时，指定 IoT 中心所有者连接字符串。 会加密连接字符串，并将其存储在该平台的证书存储中。

在后续调用中，从平台的证书存储中读取并重复使用该连接字符串。 如果在每次启动时指定该连接字符串，则每次都会在 IoT 中心设备注册表中删除该设备并重新创建。

### <a name="native-on-windows"></a>Windows 本机

若要在 Windows 上本机运行应用程序，请使用 Visual Studio 2017 打开 `OpcPublisher.sln` 项目、生成解决方案，并进行发布。 可以启动已使用以下容器发布到目标目录中的应用程序：

```cmd
dotnet OpcPublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-self-built-container"></a>使用自生成的容器

若要在自生成的容器中运行该应用程序，请生成并启动你自己的容器：

```cmd/sh
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-a-container-from-hubdockercom"></a>使用 hub.docker.com 上提供的容器

DockerHub 上提供可用的预生成容器。 若要启动容器，请运行以下命令：

```cmd/sh
docker run microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="important-when-using-a-container"></a>使用容器时的重要提示

#### <a name="access-to-the-publisher-opc-ua-server"></a>访问发布服务器 OPC UA 服务器

默认情况下，发布服务器 OPC UA 服务器侦听端口 62222。 若要在容器中公开此入站端口，需要使用 `docker run` 选项 `-p`：

```cmd/sh
docker run -p 62222:62222 microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="enable-intercontainer-name-resolution"></a>启用容器间名称解析

若要启用此容器到其他容器的名称解析，用户必须：

- 创建用户定义的 Docker 桥接网络。
- 使用 `--network` 选项将此容器连接到网络。
- 使用 `--name` 选项为此容器分配一个名称。

以下示例将介绍一些配置选项：

```cmd/sh
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

现在可使用名称 `publisher` 由其他容器通过网络访问该容器。

#### <a name="assign-a-hostname"></a>分配主机名

发布服务器使用运行它的计算机的主机名来生成证书和终结点。 Docker 选择随机主机名，除非你使用 `-h` 选项设置一个主机名。 下面将介绍一个示例，将容器的内部主机名设置为 `publisher`：

```cmd/sh
docker run -h publisher microsoft/iot-edge-opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

#### <a name="using-bind-mounts-shared-filesystem"></a>使用绑定挂载（共享文件系统）

在某些情况下，想要从主机上的位置读取配置信息或将日志文件写入这些位置，而不使用容器文件系统。 若要配置此行为，请在绑定挂载模式下使用 `docker run` 的 `-v` 选项。 例如：

```cmd/sh
-v //D/docker:/build/out/Logs
-v //D/docker:/build/out/CertificateStores
-v //D/docker:/shared
-v //D/docker:/root/.dotnet/corefx/cryptography/x509stores
```

#### <a name="store-for-x509-certificates"></a>X509 证书存储

存储 X509 证书并不适用于绑定挂载，因为存储路径的权限对于所有者需要为 `rw`。 而你需要在卷模式下使用 `docker run` 的 `-v` 选项。

## <a name="debug-the-application"></a>调试应用程序

### <a name="native-on-windows"></a>Windows 本机

使用 Visual Studio 2017 打开 `OpcPublisher.sln` 项目，并按 F5 开始调试此应用。

### <a name="in-a-docker-container"></a>在 Docker 容器中

Visual Studio 2017 支持在 Docker 容器中使用 `docker-compose` 调试应用程序。 但是，此方法不允许传递命令行参数。

VS2017 支持的一个替代调试选项是通过 `ssh` 进行调试。 可以使用存储库根目录中的 Docker 生成配置文件 `Dockerfile.ssh` 来创建启用 SSH 的容器：

```cmd/sh
docker build -f .\Dockerfile.ssh -t publisherssh .
```

现在可以启动容器来调试发布服务器：

```cmd/sh
docker run -it publisherssh
```

在容器中，必须手动启动 ssh 守护程序：

```cmd/sh
service ssh start
```

此时，可以使用密码 `Passw0rd` 创建一个 ssh 会话作为用户 `root`。

若要准备调试容器中的应用程序，请完成以下附加步骤：

1. 在主机端，创建一个 `launch.json` 文件：

    ```json
    {
      "version": "0.2.0",
      "adapter": "<path>\\plink.exe",
      "adapterArgs": "root@localhost -pw Passw0rd -batch -T ~/vsdbg/vsdbg --interpreter=vscode",
      "languageMappings": {
        "C#": {
          "languageId": "3F5162F8-07C6-11D3-9053-00C04FA302A1",
          "extensions": [ "*" ]
        }
      },
      "exceptionCategoryMappings": {
        "CLR": "449EC4CC-30D2-4032-9256-EE18EB41B62B",
        "MDA": "6ECE07A9-0EDE-45C4-8296-818D8FC401D4"
      },
      "configurations": [
        {
          "name": ".NET Core Launch",
          "type": "coreclr",
          "cwd": "~/publisher",
          "program": "Opc.Ua.Publisher.dll",
          "args": "<put-the-publisher-command-line-options-here>",

          "request": "launch"
        }
      ]
    }
    ```

1. 生成项目并将其发布到所选的目录。

1. 使用一个工具（如 `WinSCP`）将已发布的文件复制到容器中的目录 `/root/publisher` 中。 如果选择使用其他目录，请更新 `launch.json` 文件中的 `cdw` 属性。

现在，可以在 Visual Studio 命令窗口中使用以下命令开始进行调试：

```cmd
DebugAdapterHost.Launch /LaunchJson:"<path-to-the-launch.json-file-you-saved>"
```

## <a name="next-steps"></a>后续步骤

建议下一步了解如何[在 Windows 或 Linux 上为连接工厂预配置解决方案部署网关](iot-suite-connected-factory-gateway-deployment.md)。