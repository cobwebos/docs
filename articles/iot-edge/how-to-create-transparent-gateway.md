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
ms.openlocfilehash: c005dcd91412552e2b10c27a7809ca4bc46d4709
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792334"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文详细说明了如何将 IoT Edge 设备配置为可用作其他设备与 IoT 中心通信的透明网关。 在本文中，术语“IoT Edge 网关”是指用作透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。 
> * 下游设备不能使用文件上传。

设置成功透明的网关连接有三个常规步骤。 本文介绍第一步：

1. **网关设备需要能够安全地连接到下游设备，接收来自下游设备的通信，并将消息路由到适当的目标。**
2. 下游设备需要具有设备标识，才能通过 IoT 中心进行身份验证，并且知道要通过其网关设备进行通信。 有关详细信息，请参阅[对 Azure IoT 中心的下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. 下游设备需要能够安全地连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。


要使设备充当网关，需要能够安全地连接到其下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 为了保持合理的安全性，下游设备应确认网关设备的标识。 此标识检查可防止你的设备连接到可能的恶意网关。

透明网关方案中的下游设备可以是具有使用[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 但是，IoT Edge 设备不能是 IoT Edge 网关的下游。 

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们将采用与在 IoT 中心中启用[X.509 ca 安全性](../iot-hub/iot-hub-x509ca-overview.md)相同的证书设置，其中包括与特定 IoT 中心关联的 x.509 ca 证书（iot 中心根 CA）、一系列使用此 CA 签名的证书，以及 IoT Edge 设备的 CA。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>本文中使用的 "根 CA" 一词是指 PKI 证书链的最顶层颁发机构公共证书，不一定是联合证书颁发机构的证书根目录。 在许多情况下，它实际上是一个中间 CA 公共证书。 

网关在连接启动过程中向下游设备提供其 IoT Edge 设备 CA 证书。 下游设备将进行检查以确保 IoT Edge 设备 CA 证书已由根 CA 证书签名。 此过程允许下游设备确认网关来自受信任的源。

以下步骤将引导完成创建证书的过程，并将其安装在网关上的适当位置。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。 

## <a name="prerequisites"></a>必备组件

* 用于创建证书的开发计算机。 
* 要配置为网关的 Azure IoT Edge 设备。 对于以下操作系统之一，请使用 IoT Edge 安装步骤：
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

## <a name="generate-certificates-with-windows"></a>在 Windows 中生成证书

使用本部分中的步骤在 Windows 上生成测试证书。 可以使用 Windows 计算机生成证书，然后将其复制到任何受支持的操作系统上运行的任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。 如果 Windows 设备上已安装 OpenSSL，则可以跳过此步骤，但请确保 OpenSSL 在 PATH 环境变量中可用。 

可以通过多种方式来安装 OpenSSL，包括：

* **更简单：** 下载并安装所有[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如，从[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)。 将 openssl.exe 的完整路径添加到 PATH 环境变量。 
   
* **建议方法：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 我们将此目录称作 *\<VCPKGDIR>* 。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. 安装 vcpkg 后，从 powershell 提示符运行以下命令，以安装适用于 Windows x64 的 OpenSSL 包。 此安装通常需要大约 5 分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 将 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

### <a name="prepare-creation-scripts"></a>准备创建脚本

Azure IoT Edge git 存储库包含可用于生成测试证书的脚本。 在本部分中，将克隆 IoT Edge 存储库，并执行脚本。 

1. 在管理员模式下打开 PowerShell 窗口。 

2. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 导航到要在其中工作的目录。 在本文中，我们将 *\<WRKDIR >* 中调用此目录。 将在此工作目录中创建所有证书和密钥。

4. 将复制的存储库中的配置和脚本文件复制到工作目录。 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果将存储库下载为 ZIP，则文件夹名称为 `iotedge-master`，其余路径相同。 
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

7. 将脚本使用的函数引入 PowerShell 的全局命名空间。
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 窗口将显示一条警告，指出此脚本生成的证书仅用于测试目的，不应在生产方案中使用。

8. 验证是否已正确安装 OpenSSL，并确保现有证书不存在名称冲突。 如果出现问题，脚本应说明如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. 创建根 CA 证书并使其签名一个中间证书。 所有证书都放置在工作目录中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此脚本命令会创建多个证书和密钥文件，但我们将在本文的稍后部分介绍其中一项：
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 在以下命令中创建 IoT Edge 设备 CA 证书和私钥。 提供 CA 证书的名称，例如**MyEdgeDeviceCA**。 此名称用于命名文件和生成证书。 

   ```powershell
   New-CACertsEdgeDeviceCA "MyEdgeDeviceCA"
   ```

   此脚本命令会创建多个证书和密钥文件，其中包括本文后面要介绍的两个证书和密钥：
   * `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >如果提供的名称不是**MyEdgeDeviceCA**，则此命令创建的证书和密钥将反映该名称。 

现在，你已拥有证书，请跳到在[网关上安装证书](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>在 Linux 中生成证书

使用本部分中的步骤在 Linux 上生成测试证书。 你可以使用 Linux 计算机生成证书，然后将其复制到任何受支持的操作系统上运行的任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="prepare-creation-scripts"></a>准备创建脚本

Azure IoT Edge git 存储库包含可用于生成测试证书的脚本。 在本部分中，将克隆 IoT Edge 存储库，并执行脚本。 

1. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 导航到要在其中工作的目录。 我们将在本文中将此目录称为 *\<WRKDIR >* 。 将在此目录中创建所有证书和密钥文件。
  
3. 将克隆的 IoT Edge 存储库中的配置和脚本文件复制到工作目录。

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

1. 创建根 CA 证书和一个中间证书。 这些证书位于 *\<WRKDIR>* 中。

   如果已在此工作目录中创建了根证书和中间证书，请不要再次运行此脚本。 重新运行此脚本将覆盖现有证书。 而是转到下一步。 

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   此脚本会创建多个证书和密钥。 请记下一个，我们将在下一节中提到：
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 在以下命令中创建 IoT Edge 设备 CA 证书和私钥。 提供 CA 证书的名称，例如**MyEdgeDeviceCA**。 此名称用于命名文件和生成证书。 

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "MyEdgeDeviceCA"
   ```

   此脚本会创建多个证书和密钥。 请注意两个，我们将在下一部分中参考： 
   * `<WRKDIR>/certs/iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-ca-MyEdgeDeviceCA.key.pem`

   >[!TIP]
   >如果提供的名称不是**MyEdgeDeviceCA**，则此命令创建的证书和密钥将反映该名称。 

## <a name="install-certificates-on-the-gateway"></a>在网关上安装证书

创建证书链后，需要在 IoT Edge 网关设备上安装它，并将 IoT Edge 运行时配置为引用新证书。 

1. 复制 *\<WRKDIR>* 中的以下文件。 将其保存在 IoT Edge 设备上的任意位置。 我们将 IoT Edge 设备上的目标目录称作 *\<CERTDIR>* 。 

   * 设备 CA 证书 - `<WRKDIR>\certs\iot-edge-device-ca-MyEdgeDeviceCA-full-chain.cert.pem`
   * 设备 CA 私钥 - `<WRKDIR>\private\iot-edge-device-ca-MyEdgeDeviceCA.key.pem`
   * 根 CA-`<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   你可以使用诸如[Azure Key Vault](https://docs.microsoft.com/azure/key-vault)之类的服务或类似于[安全复制协议](https://www.ssh.com/ssh/scp/)的函数来移动证书文件。  如果在 IoT Edge 设备本身生成了证书，则可以跳过此步骤并使用工作目录的路径。

2. 打开 IoT Edge 安全守护程序配置文件。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 将 yaml 文件中的**证书**属性设置为 IoT Edge 设备上的证书和密钥文件的完整路径。 删除证书属性之前的 `#` 字符，取消注释四行。 请记住，yaml 中的缩进是两个空格。

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

4. 在 Linux 设备上，确保用户**iotedge**具有保存证书的目录的读取权限。 

## <a name="deploy-edgehub-to-the-gateway"></a>将 EdgeHub 部署到网关

首次在设备上安装 IoT Edge 时，只会自动启动一个系统模块： IoT Edge 代理。 要使设备充当网关，需要两个系统模块。 如果之前尚未将任何模块部署到网关设备，请为你的设备创建一个初始部署，以启动第二个系统模块 IoT Edge 集线器。 部署将显示为空，因为你不会在向导中添加任何模块，但它将确保两个系统模块都在运行。 

可以使用 `iotedge list` 命令检查哪些模块正在设备上运行。 如果此列表仅返回不包含**edgeHub**的模块**edgeAgent** ，请使用以下步骤：

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

## <a name="open-ports-on-gateway-device"></a>打开网关设备上的端口

标准 IoT Edge 设备不需要任何入站连接即可正常工作，因为与 IoT 中心之间的所有通信都是通过出站连接来完成的。 网关设备不同，因为它们需要从其下游设备接收消息。 如果防火墙位于下游设备和网关设备之间，则还需要通过防火墙进行通信。

要使网关方案正常工作，必须为来自下游设备的入站流量打开至少一个 IoT Edge 集线器支持的协议。 受支持的协议包括： MQTT、AMQP、HTTPS、MQTT over Websocket 和大于 Websocket 的 AMQP。 

| Port | 协议 |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS | 

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息
IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 此功能可让你在将任何数据发送到云之前，在网关上运行的模块中执行分析。 

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。 可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。 

下面的路由是一个示例，该示例将消息从任何下游设备发送到名为 `ai_insights`的模块，然后从 `ai_insights` 发送到 IoT 中心。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

有关消息路由的详细信息，请参阅[部署模块和建立路由](./module-composition.md#declare-routes)。


## <a name="enable-extended-offline-operation"></a>启用扩展脱机操作

从 IoT Edge 运行时的[v 1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)开始，可以为连接到该网关设备的网关设备和下游设备配置扩展脱机操作。 

利用此功能，本地模块或下游设备可以根据需要重新进行身份验证，并使用消息和方法彼此通信，即使在从 IoT 中心断开连接时也 IoT Edge 是如此。 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

若要启用扩展的脱机功能，请在将连接到 IoT Edge 网关设备与下游设备之间建立父子关系。 [向 Azure IoT 中心验证下游设备的身份](how-to-authenticate-downstream-device.md)中更详细地介绍了这些步骤。

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 继续在[Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)，了解设置透明网关方案的后续步骤。 
