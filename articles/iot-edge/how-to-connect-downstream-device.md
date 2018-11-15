---
title: 使用 Azure IoT Edge 配置下游设备 | Microsoft Docs
description: 如何将下游或叶设备配置为通过 Azure IoT Edge 网关设备进行连接。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 11/01/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7b9993650492574fc45b7f15fa3424060079f5fe
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/02/2018
ms.locfileid: "50915055"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>将下游设备连接到 Azure IoT Edge 网关

Azure IoT Edge 可实现透明网关方案，其中，一个或多个设备可以通过与 IoT 中心保持连接的单个网关设备传递其消息。 配置网关设备后，需要知道如何安全连接下游设备。 

本文列出了下游设备的常见连接问题，并引导你设置下游设备。具体内容包括： 

* 介绍传输层安全性 (TLS) 和证书基础知识。 
* 介绍 TLS 库在不同操作系统中的工作原理，以及每个操作系统如何处理证书。
* 演练不同语言的 Azure IoT 示例以帮助你入门。 

在本文中，术语“网关”和“IoT Edge 网关”是指配置为透明网关的 IoT Edge 设备。 

## <a name="prerequisites"></a>先决条件

在执行本文中的步骤之前，应准备好两个可供使用的设备：

1. 设置为透明网关的 IoT Edge 设备。 
    [将 IoT Edge 设备配置为充当透明网关](how-to-create-transparent-gateway.md)

    配置网关设备后，从网关复制 **azure-iot-test-only.root.ca.cert.pem** 证书，并使其可在下游设备上的任意位置使用。 

2. 在 IoT 中心具有设备标识的下游设备。 
    不能使用 IoT Edge 设备作为下游设备。 应使用已在 IoT 中心注册为常规 IoT 设备的设备。 可在门户的“IoT 设备”部分注册新设备。 也可以使用 Azure CLI [注册设备](../iot-hub/quickstart-send-telemetry-c.md#register-a-device)。 复制连接字符串，以便在后续部分使用。 

    目前，只有使用对称密钥身份验证的下游设备可以通过 IoT Edge 网关进行连接。 目前不支持 X.509 证书颁发机构和自签名的 X.509 证书。 

## <a name="prepare-a-downstream-device"></a>准备下游设备

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 

若要将下游设备连接到 IoT Edge 网关，需要准备好以下两项：

1. 配置了 IoT 中心设备连接字符串的设备或应用程序，该字符串中追加了用于将该设备或应用程序连接到网关的信息。 

    该连接字符串的格式类似于：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`。 将包含网关设备主机名的 **GatewayHostName** 属性追加到连接字符串的末尾。 **GatewayHostName** 的值应与网关设备的 config.yaml 文件中的 **hostname** 值匹配。 

    最终的字符串类似于：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`。

2. 设备或应用程序必须信任网关的**根 CA** 或**所有者 CA** 证书才能验证网关设备的 TLS 连接。 

    本文的余下内容将详细介绍这个较复杂的步骤。 可通过两种方法之一执行此步骤：在操作系统的证书存储中安装 CA 证书；（适用于特定的语言）使用 Azure IoT SDK 在应用程序中引用证书。

## <a name="tls-and-certificate-fundamentals"></a>TLS 和证书基础知识

将下游设备安全连接到 IoT Edge 所存在的难题就如同通过 Internet 进行其他任何客户端/服务器安全通信。 客户端和服务器使用[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 通过 Internet 安全通信。 TLS 是使用称作“证书”的标准[公钥基础结构 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 构造生成的。 TLS 是一种相当复杂的规范，阐述了与保护两个终结点相关的各种主题。不过，以下部分简要说明了将设备安全连接到 IoT Edge 网关所要满足的条件。

当客户端连接到某个服务器时，该服务器将出示称作“服务器证书链”的证书链。 证书链通常包含根证书颁发机构 (CA) 证书、一个或多个中间 CA 证书，以及服务器证书本身。 客户端通过以加密方式验证整个服务器证书链来与服务器建立信任。 客户端对服务器证书链进行的这种验证称作“服务器身份验证”。 若要验证服务器证书链，客户端需要使用创建（或发出）服务器证书时所用的根 CA 证书的副本。 一般情况下，在连接到网站时，浏览器中会预配置常用的 CA 证书，使客户端能够顺利完成验证过程。 

当某个设备连接到 Azure IoT 中心时，该设备为客户端，IoT 中心云服务为服务器。 IoT 中心云服务由公开且广泛使用的名为“Baltimore CyberTrust 根”的根 CA 证书提供支持。 由于大多数设备上已安装 IoT 中心 CA 证书，因此，许多 TLS 实现（OpenSSL、Schannel、LibreSSL）在服务器证书验证期间会自动使用该证书。 可成功连接到 IoT 中心的设备在尝试连接到 IoT Edge 网关时可能会出现问题。

当某个设备连接到 IoT Edge 网关时，下游设备为客户端，网关设备为服务器。 Azure IoT Edge 允许操作员（或用户）在适当的情况下生成网关证书链。 操作员可以选择使用公共 CA 证书（例如 Baltimore），或使用自签名的（或内部）根 CA 证书。 公共 CA 证书往往会产生相关的费用，因此通常在生产方案中使用。 最好是使用自签名的 CA 证书进行开发和测试。 先决条件部分所列的有关透明网关设置的文章使用自签名的根 CA 证书。 

对 IoT Edge 网关使用自签名的根 CA 证书时，需要在尝试连接到该网关的所有下游设备上安装或提供该证书。 

若要详细了解 IoT Edge 证书和对生产造成的某些影响，请参阅 [IoT Edge 证书用法详细信息](iot-edge-certs.md)。

## <a name="install-certificates-using-the-os"></a>使用 OS 安装证书

本文使用“所有者 CA”来表示根 CA 证书，因为这是网关先决条件文章中的脚本使用的术语。 

一般情况下，在操作系统的证书存储中安装所有者 CA 证书可让大多数应用程序使用所有者 CA 证书。 但存在一些例外情况，例如，NodeJS 应用程序不使用 OS 证书存储，而是使用 Node 运行时的内部证书存储。 如果无法在操作系统级别安装证书，请参阅本文稍后的特定于语言的示例，以便在应用程序中配合 Azure IoT SDK 使用证书。 

### <a name="ubuntu"></a>Ubuntu

以下示例命令演示如何在 Ubuntu 主机上安装 CA 证书。 此示例假设使用先决条件文章中的 **azure-iot-test-only.root.ca.cert.pem** 证书，并且已将该证书复制到下游设备上的某个位置。  

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

应会看到有一条消息指出“正在更新 /etc/ssl/certs 中的证书...已添加 1 个，已删除 0 个；已完成。”

### <a name="windows"></a>Windows

以下示例步骤演示如何在 Windows 主机上安装 CA 证书。 此示例假设使用先决条件文章中的 **azure-iot-test-only.root.ca.cert.pem** 证书，并且已将该证书复制到下游设备上的某个位置。  

1. 在“开始”菜单中，搜索并选择“管理计算机证书”。 此时会打开一个名为 **certlm** 的实用工具。
2. 导航到“证书 - 本地计算机” > “受信任的根证书颁发机构”。
3. 右键单击“证书”，并选择“所有任务” > “导入”。 此时应会启动证书导入向导。 
4. 按指导执行步骤，导入证书文件 `<path>/azure-iot-test-only.root.ca.cert.pem`。 完成后，应看到“已成功导入”消息。 

还可以按本文稍后的 .NET 示例中所示，使用 .NET API 以编程方式安装证书。 

通常，应用程序使用 Windows 提供的名为 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) 的 TLS 堆栈来通过 TLS 进行安全连接。 在尝试建立 TLS 连接之前，Schannel 要求所有证书已安装在 Windows 证书存储中。

## <a name="use-certificates-with-azure-iot-sdks"></a>配合 Azure IoT SDK 使用证书

本文将根 CA 证书称作“所有者 CA”，因为它是先决条件文章中用于生成自签名证书的脚本所用的术语。 

本部分介绍 Azure IoT SDK 如何使用简单的示例应用程序连接到 IoT Edge 设备。 所有示例的目标是连接设备客户端并将遥测消息发送到网关，然后关闭连接并退出。 

### <a name="common-concepts-across-all-azure-iot-sdks"></a>所有 Azure IoT SDK 中的通用概念

在使用应用程序级示例之前，请做好两项准备：

1. 将下游设备的 IoT 中心连接字符串修改为指向网关设备。

    该连接字符串的格式类似于：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;`。 将包含网关设备主机名的 **GatewayHostName** 属性追加到连接字符串的末尾。 **GatewayHostName** 的值应与网关设备的 config.yaml 文件中的 **hostname** 值匹配。 

    最终的字符串类似于：`HostName=yourHub.azure-devices.net;DeviceId=yourDevice;SharedAccessKey=XXXYYYZZZ=;GatewayHostName=mygateway.contoso.com`。

2. 已复制并保存在下游设备上某个位置的根 CA 证书的完整路径。

    例如，`<path>/azure-iot-test-only.root.ca.cert.pem`。 

### <a name="nodejs"></a>NodeJS

本部分提供用于将 Azure IoT NodeJS 设备客户端连接到 IoT Edge 网关的示例应用程序。 对于 Linux 和 Windows 主机，必须按如下所示在应用程序级别安装根 CA 证书，因为 NodeJS 应用程序不使用系统的证书存储。 

1. 从[适用于 Node.js 的 Azure IoT 设备 SDK 示例存储库](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)获取 **edge_downstream_device.js** 的示例。 
2. 查看 **readme.md** 文件，确保满足运行该示例的所有先决条件。 
3. 在 edge_downstream_device.js 文件中，更新 **connectionString** 和 **edge_ca_cert_path** 变量。 
4. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。 

若要了解所运行的示例，请参阅以下代码片段，其中演示了客户端 SDK 如何读取证书文件，并使用它来建立安全的 TLS 连接： 

```nodejs
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

这是测试所有内容是否已正确设置的示例命令。 应会看到一条消息指出“verified OK”。

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="next-steps"></a>后续步骤

了解 IoT Edge 如何将[脱机功能](offline-capabilities.md)扩展到下游设备。 