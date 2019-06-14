---
title: 连接下游设备 - Azure IoT Edge | Microsoft Docs
description: 如何配置下游或叶设备连接到 Azure IoT Edge 网关设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a66355ca1a0c9c2c144f04cd944efe22467d3ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058505"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>将下游设备连接到 Azure IoT Edge 网关

本文提供了有关建立下游设备与 IoT Edge 透明网关之间的受信任的连接的说明。 在透明网关方案中，一个或多个设备可以将其消息传递通过单个网关设备维护与 IoT 中心的连接。 下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游设备甚至可能是 IoT Edge 网关设备本身上运行的应用程序。 

有三个常规步骤来设置成功的透明网关连接。 本文介绍第三个步骤：

1. 网关设备需要安全地连接到下游设备、 从下游设备接收的通信和将消息路由到正确的目的地。 有关详细信息，请参阅[配置为充当透明网关的 IoT Edge 设备](how-to-create-transparent-gateway.md)。
2. 下游设备需要设备标识，以便能够使用 IoT 中心进行身份验证，并且知道通过其网关设备进行通信。 有关详细信息，请参阅[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. **下游设备都需要能够安全地连接到其网关设备。**

本文列出了下游设备的常见连接问题，并引导你设置下游设备。具体内容包括： 

* 介绍传输层安全性 (TLS) 和证书基础知识。 
* 介绍 TLS 库在不同操作系统中的工作原理，以及每个操作系统如何处理证书。
* 演练不同语言的 Azure IoT 示例以帮助你入门。 

在本文中，术语“网关”和“IoT Edge 网关”是指配置为透明网关的 IoT Edge 设备。   

## <a name="prepare-a-downstream-device"></a>准备下游设备

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 下游设备甚至可能是 IoT Edge 网关设备本身上运行的应用程序。 

若要将下游设备连接到 IoT Edge 网关，需要准备好以下两项：

* 配置了 IoT 中心设备连接字符串的设备或应用程序，该字符串中追加了用于将该设备或应用程序连接到网关的信息。 

    此步骤中所述[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)。

* 设备或应用程序必须信任该网关**根 CA**证书来验证网关设备的 TLS 连接。 

    此步骤是本文的其余部分中详细介绍。 此步骤可以是执行的一种通过两种方式： 通过引用中使用 Azure IoT Sdk 的应用程序的证书安装在操作系统的证书存储区或 （对于某些语言） 中的 CA 证书。

## <a name="tls-and-certificate-fundamentals"></a>TLS 和证书基础知识

将下游设备安全连接到 IoT Edge 所存在的难题就如同通过 Internet 进行其他任何客户端/服务器安全通信。 客户端和服务器使用[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 通过 Internet 安全通信。 TLS 是使用称作“证书”的标准[公钥基础结构 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 构造生成的。 TLS 是涉及相当多的规范，以及处理各种与保护两个终结点相关的主题。 本部分概述，以便安全地将设备连接到 IoT Edge 网关相关的概念。

当客户端连接到某个服务器时，该服务器将出示称作“服务器证书链”的证书链。  证书链通常包含根证书颁发机构 (CA) 证书、一个或多个中间 CA 证书，以及服务器证书本身。 客户端通过以加密方式验证整个服务器证书链来与服务器建立信任。 调用此客户端验证的服务器证书链*服务器链验证*。 客户端密码学角度上难题的服务，以证明持有与名为的进程中的服务器证书关联的私钥*所有权的证明*。 调用服务器链验证的组合以及所有权证明*服务器身份验证*。 若要验证服务器证书链，客户端需要使用创建（或发出）服务器证书时所用的根 CA 证书的副本。 一般情况下，在连接到网站时，浏览器中会预配置常用的 CA 证书，使客户端能够顺利完成验证过程。 

当某个设备连接到 Azure IoT 中心时，该设备为客户端，IoT 中心云服务为服务器。 IoT 中心云服务由公开且广泛使用的名为“Baltimore CyberTrust 根”的根 CA 证书提供支持。  由于大多数设备上已安装 IoT 中心 CA 证书，因此，许多 TLS 实现（OpenSSL、Schannel、LibreSSL）在服务器证书验证期间会自动使用该证书。 可成功连接到 IoT 中心的设备在尝试连接到 IoT Edge 网关时可能会出现问题。

当某个设备连接到 IoT Edge 网关时，下游设备为客户端，网关设备为服务器。 Azure IoT Edge 允许操作员（或用户）在适当的情况下生成网关证书链。 操作员可以选择使用公共 CA 证书（例如 Baltimore），或使用自签名的（或内部）根 CA 证书。 公共 CA 证书往往会产生相关的费用，因此通常在生产方案中使用。 最好是使用自签名的 CA 证书进行开发和测试。 在简介中列出的透明网关安装程序文章使用自签名的根 CA 证书。 

对 IoT Edge 网关使用自签名的根 CA 证书时，需要在尝试连接到该网关的所有下游设备上安装或提供该证书。 

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

若要详细了解 IoT Edge 证书和对生产造成的某些影响，请参阅 [IoT Edge 证书用法详细信息](iot-edge-certs.md)。

## <a name="provide-the-root-ca-certificate"></a>提供的根 CA 证书

若要验证网关设备的证书，下游设备都需要其自己的根 CA 证书的副本。 如果在 IoT Edge git 存储库中提供的脚本用于创建测试证书，则称为根 CA 证书**azure iot-测试 only.root.ca.cert.pem**。 如果尚未在其他下游设备准备步骤中，此证书将文件移动到的任何目录下游设备上。 可以使用之类的服务[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault)或其功能类似于[安全复制协议](https://www.ssh.com/ssh/scp/)移动证书文件。

## <a name="install-certificates-in-the-os"></a>在 OS 中安装证书

通常在操作系统的证书存储区中安装根 CA 证书允许大多数应用程序使用根 CA 证书。 有一些例外情况，类似于 NodeJS 应用程序不使用 OS 证书的存储，但使用节点运行时的内部证书存储区。 如果您不能安装在操作系统级别证书，请跳到[配合 Azure IoT Sdk 使用证书](#use-certificates-with-azure-iot-sdks)。 

### <a name="ubuntu"></a>Ubuntu

以下示例命令演示如何在 Ubuntu 主机上安装 CA 证书。 此示例假定你使用的**azure iot-测试 only.root.ca.cert.pem**中的先决条件文章中，证书，已将此证书复制到下游设备上的位置。

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

应会看到有一条消息指出“正在更新 /etc/ssl/certs 中的证书...已添加 1 个，已删除 0 个；已完成。”

### <a name="windows"></a>Windows

以下示例步骤演示如何在 Windows 主机上安装 CA 证书。 此示例假定你使用的**azure iot-测试 only.root.ca.cert.pem**中的先决条件文章中，证书，已将此证书复制到下游设备上的位置。

1. 在“开始”菜单中，搜索并选择“管理计算机证书”。  此时会打开一个名为 **certlm** 的实用工具。
2. 导航到“证书 - 本地计算机” > “受信任的根证书颁发机构”。  
3. 右键单击“证书”，并选择“所有任务” > “导入”。    此时应会启动证书导入向导。 
4. 按指导执行步骤，导入证书文件 `<path>/azure-iot-test-only.root.ca.cert.pem`。 完成后，应看到“已成功导入”消息。 

还可以按本文稍后的 .NET 示例中所示，使用 .NET API 以编程方式安装证书。 

通常，应用程序使用 Windows 提供的名为 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) 的 TLS 堆栈来通过 TLS 进行安全连接。 在尝试建立 TLS 连接之前，Schannel 要求所有证书已安装在 Windows 证书存储中。 

## <a name="use-certificates-with-azure-iot-sdks"></a>配合 Azure IoT SDK 使用证书

本部分介绍 Azure IoT SDK 如何使用简单的示例应用程序连接到 IoT Edge 设备。 所有示例的目标是连接设备客户端并将遥测消息发送到网关，然后关闭连接并退出。 

在使用应用程序级示例之前，请做好两项准备：

* 下游设备的 IoT 中心连接字符串修改为指向网关设备，并在下游设备到 IoT 中心进行身份验证所需的任何证书。 有关详细信息，请参阅[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)。

* 已复制并保存在下游设备上某个位置的根 CA 证书的完整路径。

    例如，`<path>/azure-iot-test-only.root.ca.cert.pem`。 

### <a name="nodejs"></a>NodeJS

本部分提供用于将 Azure IoT NodeJS 设备客户端连接到 IoT Edge 网关的示例应用程序。 对于 NodeJS 应用程序，必须安装在应用程序级别如下所示的根 CA 证书。 NodeJS 应用程序不使用系统的证书存储区。 

1. 从[适用于 Node.js 的 Azure IoT 设备 SDK 示例存储库](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)获取 **edge_downstream_device.js** 的示例。 
2. 查看 **readme.md** 文件，确保满足运行该示例的所有先决条件。 
3. 在 edge_downstream_device.js 文件中，更新 **connectionString** 和 **edge_ca_cert_path** 变量。 
4. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。 

若要了解所运行的示例，请参阅以下代码片段，其中演示了客户端 SDK 如何读取证书文件，并使用它来建立安全的 TLS 连接： 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

本部分介绍用于将 Azure IoT .NET 设备客户端连接到 IoT Edge 网关的示例应用程序。 但是，.NET 应用程序可自动使用 Linux 和 Windows 主机上的系统证书存储中安装的任何证书。

1. 从 [IoT Edge .NET 示例文件夹](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)获取 **EdgeDownstreamDevice** 的示例。 
2. 查看 **readme.md** 文件，确保满足运行该示例的所有先决条件。 
3. 在 **Properties / launchSettings.json** 文件中，更新 **DEVICE_CONNECTION_STRING** 和 **CA_CERTIFICATE_PATH** 变量。 若要使用主机系统上受信任证书存储中安装的证书，请将此变量留空。 
4. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。 

若要通过 .NET 应用程序以编程方式在证书存储中安装受信任的证书，请参阅 **EdgeDownstreamDevice / Program.cs** 文件中的 **InstallCACert()** 函数。 此操作是幂等的，因此可以使用相同的值运行多次，而不会造成其他影响。 

### <a name="c"></a>C

本部分介绍用于将 Azure IoT C 设备客户端连接到 IoT Edge 网关的示例应用程序。 C SDK 可以配合许多 TLS 库（包括 OpenSSL、WolfSSL 和 Schannel）运行。 有关详细信息，请参阅 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)。 

1. 从[适用于 C 的 Azure IoT 设备 SDK 示例](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)获取 **iotedge_downstream_device_sample** 应用程序。 
2. 查看 **readme.md** 文件，确保满足运行该示例的所有先决条件。 
3. 在 iotedge_downstream_device_sample.c 文件中，更新 **connectionString** 和 **edge_ca_cert_path** 变量。 
4. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。 

适用于 C 的 Azure IoT 设备 SDK 提供一个用于在设置客户端时注册 CA 证书的选项。 此操作不会在任何位置安装证书，而是使用内存中证书的字符串格式。 建立连接时，将向底层 TLS 堆栈提供已保存的证书。 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

在 Windows 主机上，如果你不使用 OpenSSL 或其他 TLS 库，则 SDK 默认使用 Schannel。 要使 Schannel 正常工作，应在 Windows 证书存储中安装 IoT Edge 根 CA 证书，而不要使用 `IoTHubDeviceClient_SetOption` 操作进行设置。 

### <a name="java"></a>Java

本部分介绍用于将 Azure IoT Java 设备客户端连接到 IoT Edge 网关的示例应用程序。 

1. 从[适用于 Java 的 Azure IoT 设备 SDK 示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)获取 **Send-event** 的示例。 
2. 查看 **readme.md** 文件，确保满足运行该示例的所有先决条件。 
3. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。

### <a name="python"></a>Python

本部分介绍用于将 Azure IoT Python 设备客户端连接到 IoT Edge 网关的示例应用程序。 

1. 从[适用于 Python 的 Azure IoT 设备 SDK 示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples)获取 **edge_downstream_client** 的示例。 
2. 查看 **readme.md** 文件，确保满足运行该示例的所有先决条件。 
3. 在 edge_downstream_client.py 文件中，更新 **CONNECTION_STRING** 和 **TRUSTED_ROOT_CA_CERTIFICATE_PATH** 变量。 
4. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。 


## <a name="test-the-gateway-connection"></a>测试网关连接

这是测试，所有内容都已设置正确的示例命令。 应会看到一条消息指出“verified OK”。

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>对网关连接进行故障排除

如果叶设备与其网关设备之间的连接是断断续续的，请尝试执行以下步骤来解决问题。 

1. 是在连接字符串中的网关主机名上的网关设备的 IoT Edge config.yaml 文件中的主机名值相同？
2. 是网关主机名解析为 IP 地址？ 使用 DNS 或叶设备上添加的主机文件条目，可以解决间歇性的连接。
3. 防火墙中是否打开了通信端口？ 根据使用的协议通信 (MQTTS:8883 / AMQPS:5671 / HTTPS:433) 必须能下游设备和透明的 IoT Edge 之间。

## <a name="next-steps"></a>后续步骤

了解 IoT Edge 如何将[脱机功能](offline-capabilities.md)扩展到下游设备。 
