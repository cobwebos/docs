---
title: 创建透明网关设备 - Azure IoT Edge | Microsoft Docs
description: 将 Azure IoT Edge 用作可处理来自下游设备的消息的透明网关
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5881adb7e2fc0d52cc2037d3d4a9e986b3e29d74
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058385"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文提供了详细的说明来配置 IoT Edge 设备用作透明网关的其他设备与 IoT 中心进行通信。 在本文中，术语“IoT Edge 网关”是指用作透明网关的 IoT Edge 设备  。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。 
> * 下游设备不能使用文件上传。

有三个常规步骤来设置成功的透明网关连接。 本文介绍的第一步：

1. **网关设备都需要能够安全地连接到下游设备、 从下游设备接收的通信和将消息路由到正确的目的地。**
2. 下游设备需要有设备标识，以便能够使用 IoT 中心进行身份验证和了解通过其网关设备进行通信。 有关详细信息，请参阅[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. 下游设备都需要能够安全地连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。


为要用作网关设备，它需要能够安全地连接到其下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 若要保持合理的安全性，下游设备应确认网关设备的标识。 此标识检查会阻止你的设备连接到潜在的恶意的网关。

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们假定此证书的安装程序要用来启用[X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)在 IoT 中心，其中涉及到特定 IoT 中心 （IoT 中心根 CA） 签名的证书的一系列相关联的 X.509 CA 证书使用此 CA 和 CA 为 IoT Edge 设备。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>"根 CA"本文通篇使用的术语是指最顶层的颁发机构公用证书的 PKI 证书链，而不一定代表联合的证书颁发机构的证书根。 在许多情况下，它是实际中间 CA 公用证书。 

网关连接的初始化期间显示其 IoT Edge 设备到下游设备的 CA 证书。 下游设备检查以确保 IoT Edge 设备 CA 证书由根 CA 证书签名。 此过程允许下游设备，若要确认网关来自受信任的源。

以下步骤将引导你完成创建证书，并安装在网关上正确的位置的过程。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。 

## <a name="prerequisites"></a>必备组件

要配置为网关的 Azure IoT Edge 设备。 IoT Edge 安装步骤用于以下操作系统之一：
  * [Windows](./how-to-install-iot-edge-windows.md)
  * [Linux x64](./how-to-install-iot-edge-linux.md)
  * [Linux ARM32](./how-to-install-iot-edge-linux-arm.md)

本文是指*网关主机名*几个时点。 在中声明的网关主机名**主机名**config.yaml 文件 IoT Edge 网关设备上的参数。 它用于在本文中，创建证书，并在下游设备的连接字符串中引用。 网关主机名必须是解析为 IP 地址，使用 DNS 或主机文件条目。

## <a name="generate-certificates-with-windows"></a>在 Windows 中生成证书

使用在本部分中步骤生成在 Windows 上的测试证书。 可以使用 Windows 计算机生成的证书，并再将其复制到任何受支持的操作系统上运行任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。 如果已在 Windows 设备上安装 OpenSSL，也可以跳过此步骤中，但请确保该 openssl.exe 位于 PATH 环境变量。 

可通过多种方法安装 OpenSSL：

* **更轻松：** 下载并安装任何[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如，从[SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/)。 将 openssl.exe 的完整路径添加到 PATH 环境变量。 
   
* **推荐：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 我们将此目录称作 *\<VCPKGDIR>* 。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. Vcpkg 被安装后，运行以下命令在 powershell 提示符下以 Windows x64 安装的 OpenSSL 包。 此安装通常需要大约 5 分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 将 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

### <a name="prepare-creation-scripts"></a>准备创建脚本

Azure IoT Edge git 存储库包含可用于生成测试证书的脚本。 在本部分中，将 IoT Edge 存储库克隆和执行脚本。 

1. 在管理员模式下打开 PowerShell 窗口。 

2. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 导航到要在其中工作的目录。 在整篇文章中，我们将调用此目录 *\<WRKDIR >* 。 将此工作目录中创建所有证书和密钥。

4. 将配置和脚本文件从克隆存储库复制到工作目录中。 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果下载存储库以 zip 格式，然后将其文件夹名称是`iotedge-master`和路径的其余部分是相同。 
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

7. 将 PowerShell 的全局命名空间所使用的脚本的函数。
   
   ```powershell
   . .\ca-certs.ps1
   ```

   在 PowerShell 窗口将显示的警告，此脚本生成的证书仅用于测试目的，并且不应在生产方案中使用。

8. 验证已正确安装 OpenSSL 并确保不会使用现有证书的名称冲突。 如果出现问题，脚本应说明如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. 创建根 CA 证书并将它注册一个中间证书。 证书全部放置在工作目录中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此脚本命令将创建多个证书和密钥文件，但我们将参考一个尤其是在本文后面：
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 使用以下命令创建 IoT Edge 设备的 CA 证书和私钥。 提供的网关主机名，可以在网关设备上 iotedge\config.yaml 文件中找到。 使用网关主机名来命名文件并在证书生成过程。 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   此脚本命令将创建多个证书和密钥文件，包括我们将更高版本在本文中引用的两个：
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

现在，你已有证书，直接跳转到[网关上安装的证书](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>在 Linux 中生成证书

使用在本部分中步骤生成在 Linux 上的测试证书。 可以使用 Linux 计算机生成的证书，然后将其复制到任何受支持的操作系统上运行任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="prepare-creation-scripts"></a>准备创建脚本

Azure IoT Edge git 存储库包含可用于生成测试证书的脚本。 在本部分中，将 IoT Edge 存储库克隆和执行脚本。 

1. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 导航到要在其中工作的目录。 我们将引用整篇文章为此目录 *\<WRKDIR >* 。 将此目录中创建所有的证书和密钥文件。
  
3. 将配置和脚本文件从克隆的 IoT Edge 存储库复制到工作目录中。

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

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   该脚本创建多个证书和密钥。 记下的一个，我们将引用在下一节中：
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 使用以下命令创建 IoT Edge 设备的 CA 证书和私钥。 提供的网关主机名，可以在网关设备上 iotedge/config.yaml 文件中找到。 使用网关主机名来命名文件并在证书生成过程。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   该脚本创建多个证书和密钥。 请记下 2，我们将引用在下一节中： 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>在网关上安装证书

创建证书链后，需要在 IoT Edge 网关设备上安装它，并将 IoT Edge 运行时配置为引用新证书。 

1. 复制 *\<WRKDIR>* 中的以下文件。 将其保存在 IoT Edge 设备上的任意位置。 我们将 IoT Edge 设备上的目标目录称作 *\<CERTDIR>* 。 

   * 设备 CA 证书 - `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * 设备 CA 私钥 - `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * Root CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   可以使用之类的服务[Azure 密钥保管库](https://docs.microsoft.com/azure/key-vault)或其功能类似于[安全复制协议](https://www.ssh.com/ssh/scp/)移动的证书文件。  如果生成 IoT Edge 设备本身上的证书，则可以跳过此步骤并使用工作目录的路径。

2. 打开 IoT Edge 安全守护程序配置文件。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 设置**证书**config.yaml 文件到 IoT Edge 设备上的证书和密钥文件的完整路径中的属性。 删除`#`之前证书属性以取消四个行的注释字符。 请记住，缩进 yaml 中的两个空格。

   * Windows:

      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>\\certs\\iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>\\private\\iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>\\certs\\azure-iot-test-only.root.ca.cert.pem"
      ```
   
   * Linux： 
      ```yaml
      certificates:
        device_ca_cert: "<CERTDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem"
        device_ca_pk: "<CERTDIR>/private/iot-edge-device-<gateway hostname>.key.pem"
        trusted_ca_certs: "<CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem"
      ```

4. 在 Linux 设备，请确保用户**iotedge**已读取将证书保存目录的权限。 

## <a name="deploy-edgehub-to-the-gateway"></a>将 EdgeHub 部署到网关

首次在设备上安装 IoT Edge，只有一个系统模块会自动启动： IoT Edge 代理。 要使设备充当网关，需要两个系统模块。 如果你尚未部署到网关设备之前的所有模块，创建你的设备以开始第二个系统模块的 IoT Edge 中心的初始部署。 因为你未添加任何模块在向导中，但它将确保这两个系统模块正在运行，部署将查找空。 

可以使用 `iotedge list` 命令检查哪些模块正在设备上运行。 如果该列表仅返回该模块**edgeAgent**而无需**edgeHub**，使用以下步骤：

1. 在 Azure 门户中导航到 IoT 中心。

2. 转到“IoT Edge”并选择要用作网关的 IoT Edge 设备  。

3. 选择“设置模块”  。

4. 选择“**下一步**”。

5. 在“指定路由”页中，应有一个默认路由可将来自所有模块的所有消息发送到 IoT 中心。  如果没有，请添加以下代码，然后选择“下一步”。 

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. 在“审阅模板”页中选择“提交”   。

## <a name="open-ports-on-gateway-device"></a>在网关设备上打开端口

标准 IoT Edge 设备不需要任何入站连接便可工作，因为与 IoT 中心之间的所有通信都是通过出站连接执行的。 网关设备则不同，因为他们需要从其下游设备接收消息。 如果下游设备和网关设备之间有防火墙，通信将需要通过防火墙也可以。

要使网关方案能够正常工作，必须为来自下游设备的入站流量打开 IoT Edge 中心的至少一个受支持协议。 支持的协议为 MQTT、 AMQP 和 HTTPS。 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息
IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 此功能，可将任何数据发送到云之前在网关上运行模块中执行分析。 

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。  可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。 

以下路由是将来自任何下游设备发送消息到一个名为模块的示例`ai_insights`，然后从`ai_insights`到 IoT 中心。

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

有关消息路由的详细信息，请参阅[部署模块和建立路由](./module-composition.md#declare-routes)。


## <a name="enable-extended-offline-operation"></a>启用扩展的脱机操作

从开始[v1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)可以为扩展脱机操作的 IoT Edge 运行时，配置了网关设备和下游设备连接到它。 

借助此功能，本地模块或下游设备可以重新进行身份验证与 IoT Edge 设备根据需要并使用消息和方法，即使在 IoT 中心断开连接时相互通信。 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

若要启用扩展的脱机功能，您可以建立 IoT Edge 网关设备并将连接到它的下游设备之间的父-子关系。 中更详细地说明了这些步骤[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)。

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 有关详细信息，请参阅[下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)并[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
