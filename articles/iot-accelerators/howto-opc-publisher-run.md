---
title: 运行 OPC 发布服务器 - Azure | Microsoft Docs
description: 本文介绍如何运行和调试 OPC 发布服务器。 它还解决了性能和内存方面的问题。
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2f99f50ffcccb052526981a712ac5046836a44ae
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712901"
---
# <a name="run-opc-publisher"></a>运行 OPC 发布服务器

本文介绍如何运行和调试 OPC 发布服务器。 它还解决了性能和内存方面的问题。

## <a name="command-line-options"></a>命令行选项

使用 `--help` 命令行选项显示应用程序使用情况，如下所示：

```sh/cmd
Current directory is: /appdata
Log file is: <hostname>-publisher.log
Log level is: info

OPC Publisher V2.3.0
Informational version: V2.3.0+Branch.develop_hans_methodlog.Sha.0985e54f01a0b0d7f143b1248936022ea5d749f9

Usage: opcpublisher.exe <applicationname> [<IoT Hubconnectionstring>] [<options>]

OPC Edge Publisher to subscribe to configured OPC UA servers and send telemetry to Azure IoT Hub.
To exit the application, just press CTRL-C while it is running.

applicationname: the OPC UA application name to use, required
                  The application name is also used to register the publisher under this name in the
                  IoT Hub device registry.

IoT Hubconnectionstring: the IoT Hub owner connectionstring, optional

There are a couple of environment variables which can be used to control the application:
_HUB_CS: sets the IoT Hub owner connectionstring
_GW_LOGP: sets the filename of the log file to use
_TPC_SP: sets the path to store certificates of trusted stations
_GW_PNFP: sets the filename of the publishing configuration file

Command line arguments overrule environment variable settings.

Options:
      --pf, --publishfile=VALUE
                              the filename to configure the nodes to publish.
                                Default: '/appdata/publishednodes.json'
      --tc, --telemetryconfigfile=VALUE
                              the filename to configure the ingested telemetry
                                Default: ''
  -s, --site=VALUE           the site OPC Publisher is working in. if specified
                                this domain is appended (delimited by a ':' to
                                the 'ApplicationURI' property when telemetry is
                                sent to IoT Hub.
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --ic, --iotcentral     publisher will send OPC UA data in IoTCentral
                                compatible format (DisplayName of a node is used
                                as key, this key is the Field name in IoTCentral)
                                . you need to ensure that all DisplayName's are
                                unique. (Auto enables fetch display name)
                                Default: False
      --sw, --sessionconnectwait=VALUE
                              specify the wait time in seconds publisher is
                                trying to connect to disconnected endpoints and
                                starts monitoring unmonitored items
                                Min: 10
                                Default: 10
      --mq, --monitoreditemqueuecapacity=VALUE
                              specify how many notifications of monitored items
                                can be stored in the internal queue, if the data
                                can not be sent quick enough to IoT Hub
                                Min: 1024
                                Default: 8192
      --di, --diagnosticsinterval=VALUE
                              shows publisher diagnostic info at the specified
                                interval in seconds (need log level info).
                                -1 disables remote diagnostic log and diagnostic
                                output
                                0 disables diagnostic output
                                Default: 0
      --ns, --noshutdown=VALUE
                              same as runforever.
                                Default: False
      --rf, --runforever     publisher can not be stopped by pressing a key on
                                the console, but will run forever.
                                Default: False
      --lf, --logfile=VALUE  the filename of the logfile to use.
                                Default: './<hostname>-publisher.log'
      --lt, --logflushtimespan=VALUE
                              the timespan in seconds when the logfile should be
                                flushed.
                                Default: 00:00:30 sec
      --ll, --loglevel=VALUE the loglevel to use (allowed: fatal, error, warn,
                                info, debug, verbose).
                                Default: info
        --ih, --IoT Hubprotocol=VALUE
                              the protocol to use for communication with IoT Hub (
                                allowed values: Amqp, Http1, Amqp_WebSocket_Only,
                                  Amqp_Tcp_Only, Mqtt, Mqtt_WebSocket_Only, Mqtt_
                                Tcp_Only) or IoT EdgeHub (allowed values: Mqtt_
                                Tcp_Only, Amqp_Tcp_Only).
                                Default for IoT Hub: Mqtt_WebSocket_Only
                                Default for IoT EdgeHub: Amqp_Tcp_Only
      --ms, --IoT Hubmessagesize=VALUE
                              the max size of a message which can be send to
                                IoT Hub. when telemetry of this size is available
                                it will be sent.
                                0 will enforce immediate send when telemetry is
                                available
                                Min: 0
                                Max: 262144
                                Default: 262144
      --si, --IoT Hubsendinterval=VALUE
                              the interval in seconds when telemetry should be
                                send to IoT Hub. If 0, then only the
                                IoT Hubmessagesize parameter controls when
                                telemetry is sent.
                                Default: '10'
      --dc, --deviceconnectionstring=VALUE
                              if publisher is not able to register itself with
                                IoT Hub, you can create a device with name <
                                applicationname> manually and pass in the
                                connectionstring of this device.
                                Default: none
  -c, --connectionstring=VALUE
                              the IoT Hub owner connectionstring.
                                Default: none
      --hb, --heartbeatinterval=VALUE
                              the publisher is using this as default value in
                                seconds for the heartbeat interval setting of
                                nodes without
                                a heartbeat interval setting.
                                Default: 0
      --sf, --skipfirstevent=VALUE
                              the publisher is using this as default value for
                                the skip first event setting of nodes without
                                a skip first event setting.
                                Default: False
      --pn, --portnum=VALUE  the server port of the publisher OPC server
                                endpoint.
                                Default: 62222
      --pa, --path=VALUE     the enpoint URL path part of the publisher OPC
                                server endpoint.
                                Default: '/UA/Publisher'
      --lr, --ldsreginterval=VALUE
                              the LDS(-ME) registration interval in ms. If 0,
                                then the registration is disabled.
                                Default: 0
      --ol, --opcmaxstringlen=VALUE
                              the max length of a string opc can transmit/
                                receive.
                                Default: 131072
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
      --aa, --autoaccept     the publisher trusts all servers it is
                                establishing a connection to.
                                Default: False
      --tm, --trustmyself=VALUE
                              same as trustowncert.
                                Default: False
      --to, --trustowncert   the publisher certificate is put into the trusted
                                certificate store automatically.
                                Default: False
      --fd, --fetchdisplayname=VALUE
                              same as fetchname.
                                Default: False
      --fn, --fetchname      enable to read the display name of a published
                                node from the server. this will increase the
                                runtime.
                                Default: False
      --ss, --suppressedopcstatuscodes=VALUE
                              specifies the OPC UA status codes for which no
                                events should be generated.
                                Default: BadNoCommunication,
                                BadWaitingForInitialData
      --at, --appcertstoretype=VALUE
                              the own application cert store type.
                                (allowed values: Directory, X509Store)
                                Default: 'Directory'
      --ap, --appcertstorepath=VALUE
                              the path where the own application cert should be
                                stored
                                Default (depends on store type):
                                X509Store: 'CurrentUser\UA_MachineDefault'
                                Directory: 'pki/own'
      --tp, --trustedcertstorepath=VALUE
                              the path of the trusted cert store
                                Default: 'pki/trusted'
      --rp, --rejectedcertstorepath=VALUE
                              the path of the rejected cert store
                                Default 'pki/rejected'
      --ip, --issuercertstorepath=VALUE
                              the path of the trusted issuer cert store
                                Default 'pki/issuer'
      --csr                  show data to create a certificate signing request
                                Default 'False'
      --ab, --applicationcertbase64=VALUE
                              update/set this applications certificate with the
                                certificate passed in as bas64 string
      --af, --applicationcertfile=VALUE
                              update/set this applications certificate with the
                                certificate file specified
      --pb, --privatekeybase64=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as base64 string
      --pk, --privatekeyfile=VALUE
                              initial provisioning of the application
                                certificate (with a PEM or PFX fomat) requires a
                                private key passed in as file
      --cp, --certpassword=VALUE
                              the optional password for the PEM or PFX or the
                                installed application certificate
      --tb, --addtrustedcertbase64=VALUE
                              adds the certificate to the applications trusted
                                cert store passed in as base64 string (multiple
                                strings supported)
      --tf, --addtrustedcertfile=VALUE
                              adds the certificate file(s) to the applications
                                trusted cert store passed in as base64 string (
                                multiple filenames supported)
      --ib, --addissuercertbase64=VALUE
                              adds the specified issuer certificate to the
                                applications trusted issuer cert store passed in
                                as base64 string (multiple strings supported)
      --if, --addissuercertfile=VALUE
                              adds the specified issuer certificate file(s) to
                                the applications trusted issuer cert store (
                                multiple filenames supported)
      --rb, --updatecrlbase64=VALUE
                              update the CRL passed in as base64 string to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --uc, --updatecrlfile=VALUE
                              update the CRL passed in as file to the
                                corresponding cert store (trusted or trusted
                                issuer)
      --rc, --removecert=VALUE
                              remove cert(s) with the given thumbprint(s) (
                                multiple thumbprints supported)
      --dt, --devicecertstoretype=VALUE
                              the IoT Hub device cert store type.
                                (allowed values: Directory, X509Store)
                                Default: X509Store
      --dp, --devicecertstorepath=VALUE
                              the path of the iot device cert store
                                Default Default (depends on store type):
                                X509Store: 'My'
                                Directory: 'CertificateStores/IoT Hub'
  -i, --install              register OPC Publisher with IoT Hub and then exits.
                                Default:  False
  -h, --help                 show this message and exit
      --st, --opcstacktracemask=VALUE
                              ignored, only supported for backward comaptibility.
      --sd, --shopfloordomain=VALUE
                              same as site option, only there for backward
                                compatibility
                                The value must follow the syntactical rules of a
                                DNS hostname.
                                Default: not set
      --vc, --verboseconsole=VALUE
                              ignored, only supported for backward comaptibility.
      --as, --autotrustservercerts=VALUE
                              same as autoaccept, only supported for backward
                                cmpatibility.
                                Default: False
      --tt, --trustedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted cert store will always reside in a
                                directory.
      --rt, --rejectedcertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the rejected cert store will always reside in a
                                directory.
      --it, --issuercertstoretype=VALUE
                              ignored, only supported for backward compatibility.
                                the trusted issuer cert store will always
                                reside in a directory.
```

通常情况下，仅在首次运行应用程序时，需指定 IoT 中心所有者连接字符串。 会加密连接字符串，并将其存储在该平台的证书存储中。 在之后运行时，应用程序将从证书存储中读取连接字符串。 如果在每次运行时指定连接字符串，则将删除并重新创建为 IoT 中心设备注册表中的应用程序创建的设备。

## <a name="run-natively-on-windows"></a>在 Windows 上执行本机运行

使用 Visual Studio 打开“opcpublisher.sln”项目，生成解决方案，并将其发布  。 可以在发布的“目标目录”中启动应用程序，如下所示  ：

```cmd
dotnet opcpublisher.dll <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-self-built-container"></a>使用自生成的容器

生成自己的容器，并启动它，如下所示：

```sh/cmd
docker run <your-container-name> <applicationname> [<IoT Hubconnectionstring>] [options]
```

## <a name="use-a-container-from-microsoft-container-registry"></a>使用 Microsoft 容器注册表中的容器

Microsoft 容器注册表中有一个预生成的容器可用。 启动它，如下所示：

```sh/cmd
docker run mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

查看 [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) 以了解支持的操作系统和处理器体系结构。 如果你的 OS 和 CPU 体系结构受支持，Docker 会自动选择正确的容器。

## <a name="run-as-an-azure-iot-edge-module"></a>作为 Azure IoT Edge 模块运行

OPC 发布服务器已准备好用作 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge) 模块。 如果将 OPC 发布服务器用作 IoT Edge 模块，唯一受支持的传输协议是 Amqp_Tcp_Only 和 Mqtt_Tcp_Only   。

若要将 OPC 发布服务器作为模块添加到 IoT Edge 部署，请转到 Azure 门户中的 IoT 中心设置并完成以下步骤：

1. 转到 IoT Edge，然后创建或选择你的 IoT Edge 设备  。
1. 选择“设置模块”  。
1. 选择“部署模块”下的“添加”，然后选择“IoT Edge 模块”    。
1. 在“名称”字段中，输入“发布服务器”   。
1. 在“映像 URI”字段中，输入 `mcr.microsoft.com/iotedge/opc-publisher:<tag>` 
1. 可以在 [Docker Hub](https://hub.docker.com/_/microsoft-iotedge-opc-publisher) 找到可用标记
1. 将以下 JSON 粘贴到“容器创建选项”字段  ：

    ```json
    {
        "Hostname": "publisher",
        "Cmd": [
            "--aa"
        ]
    }
    ```

    此配置会配置 IoT Edge，使用 OPC 发布服务器映像启动名为“发布服务器”的容器  。 此容器系统的主机名设置为“发布服务器”  。 使用以下命令行参数调用 OPC 发布服务器：`--aa`。 使用此选项，OPC 发布服务器信任它连接到的 OPC UA 服务器的证书。 可以使用 OPC 发布服务器命令行的任何选项。 唯一的限制是 IoT Edge 支持的“容器创建选项”的大小  。

1. 将其他设置保留不变，然后选择“保存”  。
1. 如果要使用另一个 IoT Edge 模块在本地处理 OPC 发布服务器的输出，请返回到“设置模块”页  。 然后转到“指定路由”选项卡，并添加类似以下 JSON 的新路由  ：

    ```json
    {
      "routes": {
        "processingModuleToIoT Hub": "FROM /messages/modules/processingModule/outputs/* INTO $upstream",
        "opcPublisherToProcessingModule": "FROM /messages/modules/publisher INTO BrokeredEndpoint(\"/modules/processingModule/inputs/input1\")"
      }
    }
    ```

1. 返回“设置模块”页上，选择“下一步”，直到到达配置的最后一页   。
1. 选择“提交“，将配置发送到 IoT Edge  。
1. 如果已在边缘设备上启动 IoT Edge 并且 docker 容器发布服务器正在运行，则可以通过 `docker logs -f publisher` 或检查日志文件签出 OPC 发布服务器的日志输出  。 在前面的示例中，日志文件在 `d:\iiotegde\publisher-publisher.log` 上方。 也可使用 [iot-edge-opc-publisher-diagnostics 工具](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)。

### <a name="make-the-configuration-files-accessible-on-the-host"></a>使配置文件可以在主机上访问

若要在主机文件系统中可访问 IoT Edge 模块配置文件，请使用以下“容器创建选项”  。 下面的示例是使用适用于 Windows 的 Linux 容器进行部署的过程：

```json
{
    "Hostname": "publisher",
    "Cmd": [
        "--pf=./pn.json",
        "--aa"
    ],
    "HostConfig": {
        "Binds": [
            "d:/iiotedge:/appdata"
        ]
    }
}
```

通过这些选项，OPC 发布服务器从文件 `./pn.json` 中读取应发布的节点，并在启动时将容器的工作目录设置为 `/appdata`。 通过这些设置，OPC 发布服务器从容器中读取文件 `/appdata/pn.json` 以获取其配置。 如果不使用 `--pf` 选项，OPC 发布服务器将尝试读取默认配置文件 `./publishednodes.json`。

使用默认名称 `publisher-publisher.log` 的日志文件将写入 `/appdata`，此目录中也会创建 `CertificateStores` 目录。

若要使所有这些文件在主机文件系统中可用，容器配置需要绑定装载卷。 `d://iiotedge:/appdata` 绑定映射目录 `/appdata`（容器启动时的当前工作目录）到主机目录 `d://iiotedge`。 如果不使用此选项，则下次启动容器时不会保留任何文件数据。

如果运行 Windows 容器，`Binds` 参数的语法是不同。 在启动容器时，工作目录是 `c:\appdata`。 若要将目录 `d:\iiotedge` 中的配置文件放置在主机上，请指定 `HostConfig` 部分中的以下映射：

```json
"HostConfig": {
    "Binds": [
        "d:/iiotedge:c:/appdata"
    ]
}
```

如果在 Linux 上运行 Linux 容器，`Binds` 参数的语法也不同。 在启动容器时，工作目录是 `/appdata`。 若要将目录 `/iiotedge` 中的配置文件放置在主机上，请指定 `HostConfig` 部分中的以下映射：

```json
"HostConfig": {
    "Binds": [
        "/iiotedge:/appdata"
    ]
}
```

## <a name="considerations-when-using-a-container"></a>使用容器的注意事项

以下部分列出了使用容器时需要记住的一些事项：

### <a name="access-to-the-opc-publisher-opc-ua-server"></a>访问 OPC 发布服务器 OPC UA 服务器

默认情况下，OPC 发布服务器 OPC UA 服务器在端口 62222 侦听。 若要在容器中公开此入站端口，请使用以下命令：

```sh/cmd
docker run -p 62222:62222 mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="enable-intercontainer-name-resolution"></a>启用容器间名称解析

若要启用从容器内部到其他容器的名称解析，请创建一个用户定义 docker 桥接网络，并使用 `--network` 选项将容器连接到此网络。 同时请使用 `--name` 选项为此容器分配一个名称，如下所示：

```sh/cmd
docker network create -d bridge iot_edge
docker run --network iot_edge --name publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

现在，同一网络上的其他容器可以使用名称 `publisher` 来访问此容器。

### <a name="access-other-systems-from-within-the-container"></a>从容器内访问其他系统

可以使用在上一部分中所述的参数访问其他容器。 如果托管 Docker 的操作系统已启用 DNS，则可访问 DNS 已知的所有系统。

在使用 NetBIOS 名称解析的网络中，通过 `--add-host` 选项启动容器可实现对其他系统的访问。 此选项可以有效地将条目添加到容器的主机文件：

```cmd/sh
docker run --add-host mydevbox:192.168.178.23  mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="assign-a-hostname"></a>分配主机名

OPC 发布服务器使用运行它的计算机的主机名来生成证书和终结点。 如果未通过 `-h` 选项设置主机名，Docker 将随机选择主机名。 下面的示例演示如何将容器的内部主机名设置为 `publisher`：

```sh/cmd
docker run -h publisher mcr.microsoft.com/iotedge/opc-publisher <applicationname> [<IoT Hubconnectionstring>] [options]
```

### <a name="use-bind-mounts-shared-filesystem"></a>使用绑定装载（共享文件系统）

可以选择用主机文件系统来存储配置信息和日志文件，而不是使用容器文件系统。 若要配置此选项，请在绑定装载模式下使用 `docker run` 的 `-v` 选项。

## <a name="opc-ua-x509-certificates"></a>OPC UA X.509 证书

当 OPC UA 客户端和服务器建立连接和对它们之间的通信进行加密时，OPC UA 使用 X.509 证书对它们进行身份验证。 OPC 发布服务器使用 OPC UA 堆栈所维护的证书存储来管理所有证书。 在启动时，OPC 发布服务器自行检查是否有证书。 如果在证书存储中未发现证书，并且在命令行中没有传入证书，OPC 发布服务器将创建自签名证书。 有关详细信息，请参阅 `OpcApplicationConfigurationSecurity.cs` 中的 InitApplicationSecurityAsync 方法  。

自签名证书不提供任何安全性，因为它们未由受信任的 CA 签名。

OPC 发布服务器提供以下命令行选项：

- 检索 OPC 发布服务器使用的当前应用程序证书的 CSR 信息。
- 使用 CA 签名证书预配 OPC 发布服务器。
- 使用新密钥对和匹配的 CA 签名证书预配 OPC 发布服务器。
- 将证书添加到受信任的对等或受信任的颁发者证书存储。
- 添加 CRL。
- 将证书从受信任的对等或受信任的颁发者证书存储中删除。

所有这些选项支持使用文件或 base64 编码字符串传入参数。

所有证书存储的默认存储类型是文件系统，你可以使用命令行选项进行更改。 因为容器无法在其文件系统中提供持久存储，因此必须选择其他存储类型。 使用 Docker `-v` 选项将证书存储持久保存在主机文件系统中或 Docker 卷上。 如果使用的是 Docker 卷，可以使用 base64 编码字符串传入证书。

运行时环境会影响证书持久保存的方式。 避免在每次运行该应用程序时创建新的证书存储：

- 在 Windows 上执行本机运行时，无法使用 `Directory` 类型的应用程序证书存储，因为无法访问私钥。 在本例中，使用选项 `--at X509Store`。
- 作为 Linux docker 容器运行，你可以使用 docker 运行选项 `-v <hostdirectory>:/appdata` 将证书存储映射到主机文件系统。 此选项使证书在应用程序运行时具有持久性。
- 如果作为 Linux docker 容器运行并且你希望为应用程序证书使用 X509 存储，请使用 docker 运行选项 `-v x509certstores:/root/.dotnet/corefx/cryptography/x509stores` 和应用程序选项 `--at X509Store`

## <a name="performance-and-memory-considerations"></a>性能和内存注意事项

本部分介绍用于管理内存和性能的选项：

### <a name="command-line-parameters-to-control-performance-and-memory"></a>控制性能和内存的命令行参数

运行 OPC 发布服务器时，需要注意性能要求和在主机上可用的内存资源。

内存和性能相互依赖，并且两者都取决于配置的要发布的节点数。 请确保以下参数满足你的要求：

- IoT 中心发送间隔：`--si`
- IoT 中心消息大小（默认 `1`）：`--ms`
- 受监视的项目队列容量：`--mq`

`--mq` 参数控制内部队列的容量上限，该队列缓冲所有 OPC 节点值更改通知。 如果 OPC 发布服务器无法足够快地将消息发送到 IoT 中心，此队列将缓冲通知。 参数设置可缓冲的通知数。 如果在测试运行中看到此队列中的项数增加，为避免丢失消息，你应该：

- 缩短 IoT 中心发送间隔
- 增大 IoT 中心消息大小

`--si` 参数强制 OPC 发布服务器按指定间隔将消息发送到 IoT 中心。 一旦达到 `--ms` 参数指定的消息大小，或一旦达到 `--si` 参数指定的间隔，OPC 发布服务器就会立即发送消息。 若要禁用消息大小选项，请使用 `--ms 0`。 在这种情况下，OPC 发布服务器使用最大的 IoT 中心消息大小 256 kB 来批量处理数据。

`--ms` 参数允许你将批处理消息发送到 IoT 中心。 你正在使用的协议决定，与发送有效负载的实际时间相比，将消息发送到 IoT 中心的开销是否较高。 如果你的场景允许在 IoT 中心引入数据时出现延迟，请将 OPC 发布服务器配置为使用最大消息大小 256 kB。

在生产场景中使用 OPC 发布服务器之前，请在生产条件下测试性能和内存使用情况。 可以使用 `--di` 参数指定 OPC 发布服务器写入诊断信息的间隔（以秒为单位）。

### <a name="test-measurements"></a>测试度量

以下示例诊断显示使用 `--si` 和 `--ms` 参数的不同值进行测量，这些参数在 OPC 发布间隔为 1 秒时发布 500 个节点。  该测试在 Windows 10 上本机使用 OPC 发布服务器调试版本，测试时间为 120 秒。 IoT 中心协议是默认的 MQTT 协议。

#### <a name="default-configuration---si-10---ms-262144"></a>默认配置 (--si 10 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:33:05 (started @ 26.10.2017 15:31:09)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54363
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54363
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:33:04
bytes sent to IoT Hub: 12709429
avg msg size: 116600
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 10
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

使用默认配置时，每 10 秒或当 256 kB 数据可供 IoT 中心引入时，数据会发送到 IoT 中心。 此配置会增加大约 10 秒的中等延迟，但由于消息较大，丢失数据的可能性最低。 该诊断输出显示没有丢失 OPC 节点更新：`monitored item notifications enqueue failure: 0`。

#### <a name="constant-send-interval---si-1---ms-0"></a>固定发送间隔 (--si 1 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:35:59 (started @ 26.10.2017 15:34:03)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54243
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54243
---------------------------------
messages sent to IoT Hub: 109
last successful msg sent @: 26.10.2017 15:35:59
bytes sent to IoT Hub: 12683836
avg msg size: 116365
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 1
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

当消息大小设置为 0 时，OPC 发布服务器使用支持的最大 IoT 中心消息大小 256 kB，在内部批量处理数据。 诊断输出显示平均消息大小为 115,019 字节。 在此配置下，OPC 发布服务器不会丢失任何 OPC 节点值更新，而且与默认值相比，具有较低的延迟。

### <a name="send-each-opc-node-value-update---si-0---ms-0"></a>发送每个 OPC 节点值更新 (--si 0 --ms 0)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:39:33 (started @ 26.10.2017 15:37:37)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 8184
monitored item notifications enqueued: 54232
monitored item notifications enqueue failure: 44624
monitored item notifications dequeued: 1424
---------------------------------
messages sent to IoT Hub: 1423
last successful msg sent @: 26.10.2017 15:39:33
bytes sent to IoT Hub: 333046
avg msg size: 234
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 96
--si setting: 0
--ms setting: 0
--ih setting: Mqtt
==========================================================================
```

在此配置下，会针对每个 OPC 节点值更改发送一条消息到 IoT 中心。 诊断显示平均消息大小为 234 字节，这是较小的大小。 此配置的优点是 OPC 发布服务器不会增加任何延迟。 已丢失 OPC 节点值更新 (`monitored item notifications enqueue failure: 44624`) 的数量较高，这使得此配置不适用于发布大量遥测数据的场景。

### <a name="maximum-batching---si-0---ms-262144"></a>最大批处理 (--si 0 --ms 262144)

```log
==========================================================================
OpcPublisher status @ 26.10.2017 15:42:55 (started @ 26.10.2017 15:41:00)
---------------------------------
OPC sessions: 1
connected OPC sessions: 1
connected OPC subscriptions: 5
OPC monitored items: 500
---------------------------------
monitored items queue bounded capacity: 8192
monitored items queue current items: 0
monitored item notifications enqueued: 54137
monitored item notifications enqueue failure: 0
monitored item notifications dequeued: 54137
---------------------------------
messages sent to IoT Hub: 48
last successful msg sent @: 26.10.2017 15:42:55
bytes sent to IoT Hub: 12565544
avg msg size: 261782
msg send failures: 0
messages too large to sent to IoT Hub: 0
times we missed send interval: 0
---------------------------------
current working set in MB: 90
--si setting: 0
--ms setting: 262144
--ih setting: Mqtt
==========================================================================
```

此配置批处理尽可能多的 OPC 节点值更新。 最大的 IoT 中心消息大小为 256kb，此处已配置。 没有请求发送间隔，这意味着 IoT 中心引入的数据量决定了延迟。 此配置丢失任何 OPC 节点值的概率最小，适用于发布大量节点。 当使用此配置时，如果消息大小未达到 256 kB，请确保你的场景没有引入高延迟的条件。

## <a name="debug-the-application"></a>调试应用程序

若要调试该应用程序，请使用 Visual Studio 打开 opcpublisher.sln 解决方案文件，并使用 Visual Studio 调试工具  。

如果需要访问 OPC 发布服务器中的 OPC UA 服务器，请确保你的防火墙允许访问服务器侦听的端口。 默认端口是：62222。

## <a name="control-the-application-remotely"></a>远程控制应用程序

可以使用 IoT 中心直接方法配置要发布的节点。

OPC 发布服务器实现一些额外的 IoT 中心直接方法调用，以便读取：

- 常规信息。
- OPC 会话、订阅和受监视项目的诊断信息。
- 有关 IoT 中心消息和事件的诊断信息。
- 启动日志。
- 日志的最后 100 行。
- 关闭应用程序。

以下 GitHub 存储库包含[配置要发布的节点](https://github.com/Azure-Samples/iot-edge-opc-publisher-nodeconfiguration)的工具和[读取诊断信息](https://github.com/Azure-Samples/iot-edge-opc-publisher-diagnostics)的工具。 这两个工具都还可用作 Docker 中心的容器。

## <a name="use-a-sample-opc-ua-server"></a>使用示例 OPC UA 服务器

如果没有实际的 OPC UA 服务器，则可以使用[示例 OPC UA PLC](https://github.com/Azure-Samples/iot-edge-opc-plc) 以开始。 此示例 PLC 也可在 Docker 中心上使用。

它实现了许多标记，这些标记生成随机数据和带有异常的标记。 如果需要模拟其它标记值，可以扩展该示例。

## <a name="next-steps"></a>后续步骤

现在，你已了解如何运行 OPC 发布服务器，建议下一步了解 [OPC Twin](overview-opc-twin.md) 和 [OPC Vault](overview-opc-vault.md)。
