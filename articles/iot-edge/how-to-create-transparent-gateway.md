---
title: 创建透明网关设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure IoT Edge 用作可处理来自下游设备的消息的透明网关
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d0ac7fa3a1dbb1c91da5b9919bc2c62de74213b5
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456782"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

This article provides detailed instructions for configuring an IoT Edge device to function as a transparent gateway for other devices to communicate with IoT Hub. 在本文中，术语“IoT Edge 网关”是指用作透明网关的 IoT Edge 设备。 For more information, see [How an IoT Edge device can be used as a gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>当前：
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。 
> * 下游设备不能使用文件上传。

There are three general steps to set up a successful transparent gateway connection. This article covers the first step:

1. **The gateway device needs to be able to securely connect to downstream devices, receive communications from downstream devices, and route messages to the proper destination.**
2. The downstream device needs to have a device identity to be able to authenticate with IoT Hub, and know to communicate through its gateway device. For more information, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. The downstream device needs to be able to securely connect to its gateway device. 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。


For a device to function as a gateway, it needs to be able to securely connect to its downstream devices. Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 To maintain reasonable security, the downstream device should confirm the identity of the gateway device. This identity check prevents your devices from connecting to potentially malicious gateways.

A downstream device in a transparent gateway scenario can be any application or platform that has an identity created with the [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) cloud service. 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 However, an IoT Edge device cannot be downstream of an IoT Edge gateway. 

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 In this article, we assume the same certificate setup that you would use to enable [X.509 CA security](../iot-hub/iot-hub-x509ca-overview.md) in IoT Hub, which involves an X.509 CA certificate associated to a specific IoT hub (the IoT hub root CA), a series of certificates signed with this CA, and a CA for the IoT Edge device.

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>The term "root CA" used throughout this article refers to the topmost authority public certificate of the PKI certificate chain, and not necessarily the certificate root of a syndicated certificate authority. In many cases, it is actually an intermediate CA public certificate. 

The gateway presents its IoT Edge device CA certificate to the downstream device during the initiation of the connection. The downstream device checks to make sure the IoT Edge device CA certificate is signed by the root CA certificate. This process allows the downstream device to confirm that the gateway comes from a trusted source.

The following steps walk you through the process of creating the certificates and installing them in the right places on the gateway. 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。 

## <a name="prerequisites"></a>必备组件

* A development machine to create certificates. 
* 要配置为网关的 Azure IoT Edge 设备。 Use the IoT Edge installation steps for one of the following operating systems:
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>在 Windows 中生成证书

Use the steps in this section to generate test certificates on Windows. You can use a Windows machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

在本部分中生成的证书仅用于测试目的。 

### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。 If you already have OpenSSL installed on your Windows device, you may skip this step, but ensure that openssl.exe is available in your PATH environment variable. 

There are several ways to install OpenSSL, including:

* **Easier:** Download and install any [third-party OpenSSL binaries](https://wiki.openssl.org/index.php/Binaries), for example, from [OpenSSL on SourceForge](https://sourceforge.net/projects/openssl/). 将 openssl.exe 的完整路径添加到 PATH 环境变量。 
   
* **建议方法：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 我们将此目录称作 *\<VCPKGDIR>* 。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. Once vcpkg is installed, run the following command from a powershell prompt to install the OpenSSL package for Windows x64. 此安装通常需要大约 5 分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 将 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

### <a name="prepare-creation-scripts"></a>准备创建脚本

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. 在管理员模式下打开 PowerShell 窗口。 

2. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 导航到要在其中工作的目录。 Throughout this article, we'll call this directory *\<WRKDIR>* . All certificates and keys will be created in this working directory.

4. Copy the configuration and script files from the cloned repo into your working directory. 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   If you downloaded the repo as a ZIP, then the folder name is `iotedge-master` and the rest of the path is the same. 
<!--
5. Set environment variable OPENSSL_CONF to use the openssl_root_ca.cnf configuration file.

    ```powershell
    $env:OPENSSL_CONF = "$PWD\openssl_root_ca.cnf"
    ```
-->
5. 启用 PowerShell 以运行脚本。

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

7. Bring the functions used by the scripts into PowerShell's global namespace.
   
   ```powershell
   . .\ca-certs.ps1
   ```

   The PowerShell window will display a warning that the certificates generated by this script are only for testing purposes, and should not be used in production scenarios.

8. Verify that OpenSSL has been installed correctly and make sure that there won't be name collisions with existing certificates. 如果出现问题，脚本应说明如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. Create the root CA certificate and have it sign one intermediate certificate. The certificates are all placed in your working directory.

   ```powershell
   New-CACertsCertChain rsa
   ```

   This script command creates several certificate and key files, but we're going to refer to one in particular later in this article:
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   This script command creates several certificate and key files, including two that we're going to refer to later in this article:
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

Now that you have the certificates, skip ahead to [Install certificates on the gateway](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>在 Linux 中生成证书

Use the steps in this section to generate test certificates on Linux. You can use a Linux machine to generate the certificates, and then copy them over to any IoT Edge device running on any supported operating system. 

在本部分中生成的证书仅用于测试目的。 

### <a name="prepare-creation-scripts"></a>准备创建脚本

The Azure IoT Edge git repository contains scripts that you can use to generate test certificates. In this section, you clone the IoT Edge repo and execute the scripts. 

1. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 导航到要在其中工作的目录。 We'll refer to this directory throughout the article as *\<WRKDIR>* . All certificate and key files will be created in this directory.
  
3. Copy the config and script files from the cloned IoT Edge repo into your working directory.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. Create the root CA certificate and one intermediate certificate. 这些证书位于 *\<WRKDIR>* 中。

   If you've already created root and intermediate certificates in this working directory, don't run this script again. Rerunning this script will overwrite the existing certificates. Instead, proceed to the next step. 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   The script creates several certificates and keys. Make note of one, which we'll refer to in the next section:
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. Create the IoT Edge device CA certificate and private key with the following command. Provide a name for the CA certificate, for example **MyEdgeDeviceCA**. The name is used to name the files and during certificate generation. 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   The script creates several certificates and keys. Make note of two, which we'll refer to in the next section: 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >If you provide a name other than **MyEdgeDeviceCA**, then the certificates and keys created by this command will reflect that name. 

## <a name="install-certificates-on-the-gateway"></a>在网关上安装证书

创建证书链后，需要在 IoT Edge 网关设备上安装它，并将 IoT Edge 运行时配置为引用新证书。 

1. 复制 *\<WRKDIR>* 中的以下文件。 将其保存在 IoT Edge 设备上的任意位置。 我们将 IoT Edge 设备上的目标目录称作 *\<CERTDIR>* 。 

   * 设备 CA 证书 - `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * 设备 CA 私钥 - `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   You can use a service like [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) or a function like [Secure copy protocol](https://www.ssh.com/ssh/scp/) to move the certificate files.  If you generated the certificates on the IoT Edge device itself, you can skip this step and use the path to the working directory.

2. 打开 IoT Edge 安全守护程序配置文件。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. Set the **certificate** properties in the config.yaml file to the full path to the certificate and key files on the IoT Edge device. Remove the `#` character before the certificate properties to uncomment the four lines. Remember that indents in yaml are two spaces.

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux： 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. On Linux devices, make sure that the user **iotedge** has read permissions for the directory holding the certificates. 

## <a name="deploy-edgehub-to-the-gateway"></a>将 EdgeHub 部署到网关

When you first install IoT Edge on a device, only one system module starts automatically: the IoT Edge agent. 要使设备充当网关，需要两个系统模块。 If you haven't deployed any modules to your gateway device before, create an initial deployment for your device to start the second system module, the IoT Edge hub. The deployment will look empty because you don't add any modules in the wizard, but it will make sure that both system modules are running. 

可以使用 `iotedge list` 命令检查哪些模块正在设备上运行。 If the list only returns the module **edgeAgent** without **edgeHub**, use the following steps:

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备。

3. 选择“设置模块”。

4. 选择“**下一步**”。

5. 在“指定路由”页中，应有一个默认路由可将来自所有模块的所有消息发送到 IoT 中心。 如果没有，请添加以下代码，然后选择“下一步”。

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. 在“审阅模板”页中选择“提交”。

## <a name="open-ports-on-gateway-device"></a>Open ports on gateway device

Standard IoT Edge devices don't need any inbound connectivity to function, because all communication with IoT Hub is done through outbound connections. Gateway devices are different because they need to receive messages from their downstream devices. If a firewall is between the downstream devices and the gateway device, then communication needs to be possible through the firewall as well.

For a gateway scenario to work, at least one of the IoT Edge hub's supported protocols must be open for inbound traffic from downstream devices. The supported protocols are MQTT, AMQP, and HTTPS. 

| Port | 协议 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息
IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 This feature allows you to perform analytics in a module running on the gateway before sending any data to the cloud. 

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。 可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。 

The below route is an example that would send messages from any downstream device to a module named `ai_insights`, and then from `ai_insights` to IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

有关消息路由的详细信息，请参阅[部署模块和建立路由](./module-composition.md#declare-routes)。


## <a name="enable-extended-offline-operation"></a>Enable extended offline operation

Starting with the [v1.0.4 release](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) of the IoT Edge runtime, the gateway device and downstream devices connecting to it can be configured for extended offline operation. 

With this capability, local modules or downstream devices can re-authenticate with the IoT Edge device as needed and communicate with each other using messages and methods even when disconnected from the IoT hub. 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

To enable extended offline capabilities, you establish a parent-child relationship between an IoT Edge gateway device and downstream devices that will connect to it. Those steps are explained in more detail in [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 Continue on to [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for the next steps in setting up your transparent gateway scenario. 
