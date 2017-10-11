---
title: "部署 Azure IoT 套件连接的工厂网关 | Microsoft Docs"
description: "如何在 Windows 或 Linux 中部署网关，以便连接到已连接的工厂预配置解决方案。"
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
ms.date: 07/24/2017
ms.author: dobett
ms.openlocfilehash: b0e6ae705911d7c18643c77b7fe08fdffffa5eb1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2017
---
# <a name="deploy-a-gateway-on-windows-or-linux-for-the-connected-factory-preconfigured-solution"></a>在 Windows 或 Linux 上为已连接的工厂预配置解决方案部署网关

需要为已连接的工厂预配置解决方案部署网关的软件具有两个组件：

* OPC 代理建立到 IoT 中心的连接。 OPC 代理随即等待集成 OPC 浏览器发出命令和控制消息，该浏览器在连接的工厂解决方案门户中运行。
* OPC 发布服务器连接到现有的本地 OPC UA 服务器，并将遥测消息从服务器转发到 IoT 中心。

这两个组件为开源组件，在 GitHub 可用作源，也可用作 Docker 容器：

| GitHub | DockerHub |
| ------ | --------- |
| [OPC 发布服务器][lnk-publisher-github] | [OPC 发布服务器][lnk-publisher-docker] |
| [OPC 代理][lnk-proxy-github] | [OPC 代理][lnk-proxy-docker] |

这两个组件中的任意一个都不需要网关防火墙中面向公众的 IP 地址或缺口。 OPC 代理和 OPC 发布服务器仅使用出站端口 443、5671 和 8883。

本文中的步骤演示如何在 [Windows](#windows-deployment) 或 [Linux](#linux-deployment) 上使用 Docker 部署网关。 使用网关可以连接已连接的工厂预配置解决方案。

> [!NOTE]
> 在 Docker 容器中运行的网关软件是 [Azure IoT Edge]。

## <a name="windows-deployment"></a>Windows 部署

> [!NOTE]
> 如果还没有网关设备，Microsoft 建议从其中一个合作伙伴处购买商业网关。 有关与已连接的工厂解决方案兼容的网关设备的列表，请访问 [Azure IoT 设备目录]。 按照设备随附的说明操作，设置网关。 或者，使用以下说明手动设置其中一个现有网关。

### <a name="install-docker"></a>安装 Docker

在基于 Windows 的网关设备上安装 [适用于 Windows 的 Docker] 。 在 Windows Docker 安装过程中，选择主机上的驱动器，以与 Docker 共享。 以下屏幕截图显示正在共享 Windows 系统上的 D 驱动器：

![安装 Docker][img-install-docker]

然后在共享驱动器的根目录中创建名为 docker 的文件夹。
也可在从“设置”菜单安装 docker 后，执行此步骤。

### <a name="configure-the-gateway"></a>配置网关

1. 需要具备 Azure IoT 套件已连接的工厂部署的 iothubowner 连接字符串，才能完成网关部署。 在 [Azure 门户]，导航到 IoT 中心中在部署已连接的工厂解决方案时创建的资源组。 单击“共享访问策略”，访问 iothubowner 连接字符串：

    ![找到 IoT 中心连接字符串][img-hub-connection]

    复制“连接字符串 - 主键”值。

1. 在命令提示符中通过以下代码一次性运行两个网关模块，为 IoT 中心配置网关：

    `docker run -it --rm -h <ApplicationName> -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * &lt;ApplicationName&gt; 是提供给 OPC UA 发布服务器的名称，采用 publisher.&lt;完全限定的域名&gt;格式。 例如，如果工厂网络名称是“myfactorynetwork.com”，则 ApplicationName 的值是“publisher.myfactorynetwork.com”。
    * &lt;IoTHubOwnerConnectionString&gt; 是在上一步中复制的 iothubowner 连接字符串。 此连接字符串仅用于此步骤中，在后续的步骤中无需再使用它：

    稍后使用映射的 D:\\docker 文件夹（`-v` 参数）保存网关模块所用的两个 X.509 证书。

### <a name="run-the-gateway"></a>运行网关

1. 使用以下命令，重启网关：

    `docker run -it --rm -h <ApplicationName> --expose 62222 -p 62222:62222 -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v //D/docker:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v //D/docker:/shared -v //D/docker:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `docker run -it --rm -v //D/docker:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. 出于安全考虑，D:\\docker 文件夹中保存的这两个 X.509 证书包含私钥。 将此文件夹的访问限制为通过用于运行 Docker 容器的凭据（通常为管理员）进行访问。 右键单击 D:\\docker 文件夹，依次选择“属性”、“安全”、“编辑”。 为管理员提供完全控制，并删除其他人：

    ![授予 Docker 共享的访问权限][img-docker-share]

1. 验证网络连接。 在命令提示符中，输入命令 `ping publisher.<your fully qualified domain name>` 对网关执行 ping 操作。 无法访问目标时，将网关的 IP 地址和名称添加到网关上的主机文件中。 主机文件位于“Windows\\System32\\drivers\\etc”文件夹。

1. 然后，尝试使用在网关上运行的本地 OPC UA 客户端连接到发布服务器。 OPC UA 终结点 URL 为 `opc.tcp://publisher.<your fully qualified domain name>:62222`。 如果没有 OPC UA 客户端，可以下载并使用[开源 OPC UA 客户端]。

1. 成功完成这些本地测试后，浏览到已连接工厂解决方案门户中的“连接自己的 OPC UA 服务器”页面。 输入发布服务器终结点 URL (`tcp://publisher.<your fully qualified domain name>:62222`)，并单击“连接”。 将收到证书警告，并单击“继续”。 接下来，将收到发布服务器不信任 UA Web 客户端的错误。 若要解决此错误，请在网关上将“UA Web 客户端”证书从“D:\\docker\\Rejected Certificates\\certs”文件夹复制到“D:\\docker\\UA Applications\\certs”文件夹。 无需重启网关。 重复此步骤。 现可从云连接到网关，并准备好将 OPC UA 服务器添加到解决方案中。

### <a name="add-your-opc-ua-servers"></a>添加 OPC UA 服务器

1. 浏览到已连接工厂解决方案门户中的“连接自己的 OPC UA 服务器”页面。 按照前面部分的步骤操作，在已连接的工厂门户和 OPC UA 服务器之间建立信任关系。 此步骤会在已连接工厂门户的证书和 OPC UA 服务器的证书间建立相互信任的关系，并创建连接。

1. 浏览 OPC UA 服务器的 OPC UA 节点树，右键单击该 OPC 节点，并选择“发布”。 若要以此方式进行发布，OPC UA 服务器和发布服务器必须处于同一网络中。 换言之，如果发布服务器的完全限定的域名是 publisher.mydomain.com，则 OPC UA 服务器的完全限定的域名必须为 myopcuaserver.mydomain.com 之类的域名。 如果你的设置不同，可将节点手动添加到 D:\\docker 文件夹中的 publishesnodes.json 文件。 OPC 节点第一次成功发布时会自动生成 publishesnodes.json 文件。

1. 遥测现从网关设备流入。 可在已连接的工厂门户中“新工厂”下的“工厂位置”视图中查看此遥测。

## <a name="linux-deployment"></a>Linux 部署

> [!NOTE]
> 如果还没有网关设备，Microsoft 建议从其中一个合作伙伴处购买商业网关。 有关与已连接的工厂解决方案兼容的网关设备的列表，请访问 [Azure IoT 设备目录]。 按照设备随附的说明操作，设置网关。 或者，使用以下说明手动设置其中一个现有网关。

在 Linux 网关设备上[安装 Docker]。

### <a name="configure-the-gateway"></a>配置网关

1. 需要具备 Azure IoT 套件已连接的工厂部署的 iothubowner 连接字符串，才能完成网关部署。 在 [Azure 门户]，导航到 IoT 中心中在部署已连接的工厂解决方案时创建的资源组。 单击“共享访问策略”，访问 iothubowner 连接字符串：

    ![找到 IoT 中心连接字符串][img-hub-connection]

    复制“连接字符串 - 主键”值。

1. 在 shell 中通过以下代码一次性运行两个网关模块，为 IoT 中心配置网关：

    `sudo docker run -it --rm -h <ApplicationName> -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/ -v /shared:/root/.dotnet/corefx/cryptography/x509stores microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName> "<IoTHubOwnerConnectionString>"`

    `sudo docker run --rm -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -i -c "<IoTHubOwnerConnectionString>" -D /mapped/cs.db`

    * &lt;ApplicationName&gt; 是 OPC UA 应用程序的名称，该应用程序是网关以 publisher.&lt;完全限定的域名&gt;的格式创建的。 例如，publisher.microsoft.com。
    * &lt;IoTHubOwnerConnectionString&gt; 是在上一步中复制的 iothubowner 连接字符串。 此连接字符串仅用于此步骤中，在后续的步骤中无需再使用它：

    稍后使用 /shared 文件夹（`-v` argument 参数）保存网关模块所用的两个 X.509 证书。

### <a name="run-the-gateway"></a>运行网关

1. 使用以下命令，重启网关：

    `sudo docker run -it -h <ApplicationName> --expose 62222 -p 62222:62222 –-rm -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/Logs -v /shared:/build/src/GatewayApp.NetCore/bin/Debug/netcoreapp1.0/publish/CertificateStores -v /shared:/shared -v /shared:/root/.dotnet/corefx/cryptography/x509stores -e _GW_PNFP="/shared/publishednodes.JSON" microsoft/iot-gateway-opc-ua:1.0.0 <ApplicationName>`

    `sudo docker run -it -v /shared:/mapped microsoft/iot-gateway-opc-ua-proxy:0.1.3 -D /mapped/cs.db`

1. 出于安全考虑，/shared 文件夹中保存的这两个 X.509 证书包含私钥。 将此文件夹的访问限制为通过用于运行 Docker 容器的凭据进行访问。 若只需设置根的权限，请在文件夹中使用 `chmod` shell 命令。

1. 验证网络连接。 在 shell 中，输入命令 `ping publisher.<your fully qualified domain name>` 对网关执行 ping 操作。 无法访问目标时，将网关的 IP 地址和名称添加到网关的主机文件中。 主机文件位于 /etc 文件夹中。

1. 然后，尝试使用在网关上运行的本地 OPC UA 客户端连接到发布服务器。 OPC UA 终结点 URL 为 `opc.tcp://publisher.<your fully qualified domain name>:62222`。 如果没有 OPC UA 客户端，可以下载并使用[开源 OPC UA 客户端]。

1. 成功完成这些本地测试后，浏览到已连接工厂解决方案门户中的“连接自己的 OPC UA 服务器”页面。 输入发布服务器终结点 URL (`tcp://publisher.<your fully qualified domain name>:62222`)，并单击“连接”。 将收到证书警告，并单击“继续”。 接下来，将收到发布服务器不信任 UA Web 客户端的错误。 若要解决此错误，请在网关上将“UA Web 客户端”证书从“/shared/Rejected Certificates/certs”文件夹复制到“/shared/UA Applications/certs”文件夹。 无需重启网关。 重复此步骤。 现可从云连接到网关，并准备好将 OPC UA 服务器添加到解决方案中。

### <a name="add-your-opc-ua-servers"></a>添加 OPC UA 服务器

1. 浏览到已连接工厂解决方案门户中的“连接自己的 OPC UA 服务器”页面。 按照前面部分的步骤操作，在已连接的工厂门户和 OPC UA 服务器之间建立信任关系。 此步骤会在已连接工厂门户的证书和 OPC UA 服务器的证书间建立相互信任的关系，并创建连接。

1. 浏览 OPC UA 服务器的 OPC UA 节点树，右键单击该 OPC 节点，并选择“发布”。 若要以此方式进行发布，OPC UA 服务器和发布服务器必须处于同一网络中。 换言之，如果发布服务器的完全限定的域名是 publisher.mydomain.com，则 OPC UA 服务器的完全限定的域名必须为 myopcuaserver.mydomain.com 之类的域名。 如果你的设置不同，可将节点手动添加到 /shared 文件夹中的 publishesnodes.json 文件。 OPC 节点第一次成功发布时会自动生成 publishesnodes.json。

1. 遥测现从网关设备流入。 可在已连接的工厂门户中“新工厂”下的“工厂位置”视图中查看此遥测。

## <a name="next-steps"></a>后续步骤

若要深入了解已连接工厂预配置解决方案的体系结构，请参阅[已连接的工厂预配置解决方案演练][lnk-walkthrough]。

[img-install-docker]: ./media/iot-suite-connected-factory-gateway-deployment/image1.png
[img-hub-connection]: ./media/iot-suite-connected-factory-gateway-deployment/image2.png
[img-docker-share]: ./media/iot-suite-connected-factory-gateway-deployment/image3.png

[适用于 Windows 的 Docker]: https://www.docker.com/docker-windows
[Azure IoT 设备目录]: https://catalog.azureiotsuite.com/?q=opc
[Azure 门户]: http://portal.azure.com/
[开源 OPC UA 客户端]: https://github.com/OPCFoundation/UA-.NETStandardLibrary/tree/master/SampleApplications/Samples/Client.Net4
[安装 Docker]: https://www.docker.com/community-edition#/download
[lnk-walkthrough]: iot-suite-connected-factory-sample-walkthrough.md
[Azure IoT Edge]: https://github.com/Azure/iot-edge

[lnk-publisher-github]: https://github.com/Azure/iot-edge-opc-publisher
[lnk-publisher-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua
[lnk-proxy-github]: https://github.com/Azure/iot-edge-opc-proxy
[lnk-proxy-docker]: https://hub.docker.com/r/microsoft/iot-gateway-opc-ua-proxy