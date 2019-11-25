---
title: 连接下游设备 - Azure IoT Edge | Microsoft Docs
description: How to configure downstream or leaf devices to connect to Azure IoT Edge gateway devices.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 719ec736fd2f28f8d8b3b226109bc988c872d10f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457123"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>将下游设备连接到 Azure IoT Edge 网关

This article provides instructions for establishing a trusted connection between downstream devices and IoT Edge transparent gateways. In a transparent gateway scenario, one or more devices can pass their messages through a single gateway device that maintains the connection to IoT Hub. 下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 A downstream device could even be an application running on the IoT Edge gateway device itself. 

There are three general steps to set up a successful transparent gateway connection. This article covers the third step:

1. The gateway device needs to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination. For more information, see [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md).
2. The downstream device needs a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **The downstream device needs to be able to securely connect to its gateway device.**

本文列出了下游设备的常见连接问题，并引导你设置下游设备。具体内容包括： 

* 介绍传输层安全性 (TLS) 和证书基础知识。 
* 介绍 TLS 库在不同操作系统中的工作原理，以及每个操作系统如何处理证书。
* 演练不同语言的 Azure IoT 示例以帮助你入门。 

在本文中，术语“网关”和“IoT Edge 网关”是指配置为透明网关的 IoT Edge 设备。 

## <a name="prerequisites"></a>必备组件 

Have the **azure-iot-test-only.root.ca.cert.pem** certificate file that was generated in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md) available on your downstream device. Your downstream device uses this certificate to validate the identity of the gateway device. 

## <a name="prepare-a-downstream-device"></a>准备下游设备

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 A downstream device could even be an application running on the IoT Edge gateway device itself. However, another IoT Edge device cannot be downstream of an IoT Edge gateway. 

>[!NOTE]
>IoT devices that have identities registered in IoT Hub can use [module twins](../iot-hub/iot-hub-devguide-module-twins.md) to isolate different process, hardware, or functions on a single device. IoT Edge gateways support downstream module connections using symmetric key authentication but not X.509 certificate authentication. 

若要将下游设备连接到 IoT Edge 网关，需要准备好以下两项：

* 配置了 IoT 中心设备连接字符串的设备或应用程序，该字符串中追加了用于将该设备或应用程序连接到网关的信息。 

    This step is explained in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* The device or application has to trust the gateway's **root CA** certificate to validate the TLS connections to the gateway device. 

    This step is explained in detail in the rest of this article. This step can be performed one of two ways: by installing the CA certificate in the operating system's certificate store, or (for certain languages) by referencing the certificate within applications using the Azure IoT SDKs.

## <a name="tls-and-certificate-fundamentals"></a>TLS 和证书基础知识

将下游设备安全连接到 IoT Edge 所存在的难题就如同通过 Internet 进行其他任何客户端/服务器安全通信。 客户端和服务器使用[传输层安全性 (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security) 通过 Internet 安全通信。 TLS 是使用称作“证书”的标准[公钥基础结构 (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) 构造生成的。 TLS is a fairly involved specification and addresses a wide range of topics related to securing two endpoints. This section summarizes the concepts relevant for you to securely connect devices to an IoT Edge gateway.

当客户端连接到某个服务器时，该服务器将出示称作“服务器证书链”的证书链。 证书链通常包含根证书颁发机构 (CA) 证书、一个或多个中间 CA 证书，以及服务器证书本身。 客户端通过以加密方式验证整个服务器证书链来与服务器建立信任。 This client validation of the server certificate chain is called *server chain validation*. The client cryptographically challenges the service to prove possession of the private key associated with the server certificate in a process called *proof of possession*. The combination of server chain validation and proof of possession is called *server authentication*. 若要验证服务器证书链，客户端需要使用创建（或发出）服务器证书时所用的根 CA 证书的副本。 一般情况下，在连接到网站时，浏览器中会预配置常用的 CA 证书，使客户端能够顺利完成验证过程。 

当某个设备连接到 Azure IoT 中心时，该设备为客户端，IoT 中心云服务为服务器。 IoT 中心云服务由公开且广泛使用的名为“Baltimore CyberTrust 根”的根 CA 证书提供支持。 由于大多数设备上已安装 IoT 中心 CA 证书，因此，许多 TLS 实现（OpenSSL、Schannel、LibreSSL）在服务器证书验证期间会自动使用该证书。 可成功连接到 IoT 中心的设备在尝试连接到 IoT Edge 网关时可能会出现问题。

当某个设备连接到 IoT Edge 网关时，下游设备为客户端，网关设备为服务器。 Azure IoT Edge 允许操作员（或用户）在适当的情况下生成网关证书链。 操作员可以选择使用公共 CA 证书（例如 Baltimore），或使用自签名的（或内部）根 CA 证书。 公共 CA 证书往往会产生相关的费用，因此通常在生产方案中使用。 最好是使用自签名的 CA 证书进行开发和测试。 The transparent gateway setup articles listed in the introduction use self-signed root CA certificates. 

对 IoT Edge 网关使用自签名的根 CA 证书时，需要在尝试连接到该网关的所有下游设备上安装或提供该证书。 

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

若要详细了解 IoT Edge 证书和对生产造成的某些影响，请参阅 [IoT Edge 证书用法详细信息](iot-edge-certs.md)。

## <a name="provide-the-root-ca-certificate"></a>Provide the root CA certificate

To verify the gateway device's certificates, the downstream device needs its own copy of the root CA certificate. If you used the scripts provided in the IoT Edge git repository to create test certificates, then the root CA certificate is called **azure-iot-test-only.root.ca.cert.pem**. If you haven't already as part of the other downstream device preparation steps, move this certificate file to any directory on your downstream device. You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate file.

## <a name="install-certificates-in-the-os"></a>Install certificates in the OS

Installing the root CA certificate in the operating system's certificate store generally allows most applications to use the root CA certificate. There are some exceptions, like NodeJS applications that don't use the OS certificate store but rather use the Node runtime's internal certificate store. If you can't install the certificate at the operating system level, skip ahead to [Use certificates with Azure IoT SDKs](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

以下示例命令演示如何在 Ubuntu 主机上安装 CA 证书。 This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

You should see a message that says, "Updating certificates in /etc/ssl/certs... 1 added, 0 removed; done."

### <a name="windows"></a>Windows

以下示例步骤演示如何在 Windows 主机上安装 CA 证书。 This example assumes that you're using the **azure-iot-test-only.root.ca.cert.pem** certificate from the prerequisites articles, and that you've copied the certificate into a location on the downstream device.

You can install certificates using PowerShell's [Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate?view=win10-ps) as an administrator:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

You can also install certificates using the **certlm** utility: 

1. 在“开始”菜单中，搜索并选择“管理计算机证书”。 此时会打开一个名为 **certlm** 的实用工具。
2. 导航到“证书 - 本地计算机” > “受信任的根证书颁发机构”。
3. 右键单击“证书”，并选择“所有任务” > “导入”。 此时应会启动证书导入向导。 
4. 按指导执行步骤，导入证书文件 `<path>/azure-iot-test-only.root.ca.cert.pem`。 完成后，应看到“已成功导入”消息。 

还可以按本文稍后的 .NET 示例中所示，使用 .NET API 以编程方式安装证书。 

通常，应用程序使用 Windows 提供的名为 [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) 的 TLS 堆栈来通过 TLS 进行安全连接。 在尝试建立 TLS 连接之前，Schannel 要求所有证书已安装在 Windows 证书存储中。

## <a name="use-certificates-with-azure-iot-sdks"></a>配合 Azure IoT SDK 使用证书

本部分介绍 Azure IoT SDK 如何使用简单的示例应用程序连接到 IoT Edge 设备。 所有示例的目标是连接设备客户端并将遥测消息发送到网关，然后关闭连接并退出。 

在使用应用程序级示例之前，请做好两项准备：

* Your downstream device's IoT Hub connection string modified to point to the gateway device, and any certificates required to authenticate your downstream device to IoT Hub. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

* 已复制并保存在下游设备上某个位置的根 CA 证书的完整路径。

    例如，`<path>/azure-iot-test-only.root.ca.cert.pem` 。 

### <a name="nodejs"></a>NodeJS

本部分提供用于将 Azure IoT NodeJS 设备客户端连接到 IoT Edge 网关的示例应用程序。 For NodeJS applications, you must install the root CA certificate at the application level as shown here. NodeJS applications don't use the system's certificate store. 

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

1. Get the sample for **send_message** from the [Azure IoT device SDK for Python samples](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/advanced-edge-scenarios). 
2. Ensure that you are either running in an IoT Edge container, or in a debug scenario, have the `EdgeHubConnectionString` and `EdgeModuleCACertificateFile` environment variables set.
3. 参阅 SDK 文档，获取有关如何在设备上运行该示例的说明。 


## <a name="test-the-gateway-connection"></a>测试网关连接

Use this sample command to test that your downstream device can connect to the gateway device: 

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

This command tests connections over MQTTS (port 8883). If you're using a different protocol, adjust the command as neccessary for AMQPS (5671) or HTTPS (433).

The output of this command may be long, including information about all the certificates in the chain. If your connection is successful, you'll see a line like `Verification: OK` or `Verify return code: 0 (ok)`.

![Verify gateway connection](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Troubleshoot the gateway connection

If your leaf device has intermittent connection to its gateway device, try the following steps for resolution. 

1. Is the gateway hostname in the connection string the same as the hostname value in the IoT Edge config.yaml file on the gateway device?
2. Is the gateway hostname resolvable to an IP Address? You can resolve intermittent connections either by using DNS or by adding a host file entry on the leaf device.
3. Are communication ports open in your firewall? Communication based on the protocol used (MQTTS:8883/AMQPS:5671/HTTPS:433) must be possible between downstream device and the transparent IoT Edge.

## <a name="next-steps"></a>后续步骤

了解 IoT Edge 如何将[脱机功能](offline-capabilities.md)扩展到下游设备。 
