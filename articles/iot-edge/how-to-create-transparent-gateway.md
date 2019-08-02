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
ms.openlocfilehash: a91860e9ec8d503a01d079925466093d19bbbccf
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698608"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>将 IoT Edge 设备配置为充当透明网关

本文详细说明如何将 IoT Edge 设备配置为充当透明网关，供其他设备用来与 IoT 中心通信。 在本文中，术语“IoT Edge 网关”是指用作透明网关的 IoT Edge 设备。 有关详细信息，请参阅[如何将 IoT Edge 设备用作网关](./iot-edge-as-gateway.md)。

>[!NOTE]
>当前：
> * 支持 Edge 的设备无法连接到 IoT Edge 网关。 
> * 下游设备不能使用文件上传。

成功设置透明网关连接需要完成三个常规步骤。 本文介绍其中的第一个步骤：

1. **网关设备需要能够安全连接到下游设备，从下游设备接收通信，并将消息路由到正确的目标。**
2. 下游设备需有一个设备标识，才能在 IoT 中心进行身份验证并知道要通过其网关设备进行通信。 有关详细信息，请参阅[在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
3. 下游设备需要能够安全连接到其网关设备。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)。


充当网关的设备必须能够安全地连接到下游设备。 Azure IoT Edge 允许使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们可以将下游设备连接到充当透明网关的 IoT Edge 设备。 要维持合理的安全性，下游设备应确认网关设备的标识。 此标识检查可防止设备连接到潜在的恶意网关。

下游设备可以是包含通过 [Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)云服务创建的标识的任何应用程序或平台。 在许多情况下，这些应用程序使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md)。 在各种实际用途中，下游设备甚至可以是 IoT Edge 网关设备本身上运行的应用程序。 

可以创建任何启用设备网关拓扑所需的信任的证书基础结构。 在本文中，我们假设使用相同的证书设置来启用 IoT 中心的 [X.509 CA 安全性](../iot-hub/iot-hub-x509ca-overview.md)，其中涉及与特定 IoT 中心（IoT 中心根 CA）关联的 X.509 CA 证书，以及通过此 CA 签名的一系列证书和 IoT Edge 设备的 CA。

![网关证书设置](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>在整篇文章中使用的术语“根 CA”是指 PKI 证书链最顶层的颁发机构公共证书，而不一定是联合证书颁发机构的证书根。 在许多情况下，它实际上是中间 CA 公共证书。 

网关在连接启动期间向下游设备出示其 IoT Edge 设备 CA 证书。 下游设备检查以确保 IoT Edge 设备 CA 证书由根 CA 证书签名。 此过程允许下游设备确认网关是否来自受信任的源。

以下步骤将演示创建证书并将它们安装在网关上的正确位置的过程。 可以使用任一计算机生成证书，然后将其复制到 IoT Edge 设备。 

## <a name="prerequisites"></a>先决条件

要配置为网关的 Azure IoT Edge 设备。 对以下操作系统之一使用 IoT Edge 安装步骤：
  * [Windows](how-to-install-iot-edge-windows.md)
  * [Linux](how-to-install-iot-edge-linux.md)

本文在多个位置提到了“网关主机名”。 网关主机名在 IoT Edge 网关设备上的 config.yaml 文件的 **hostname** 参数中声明。 它用于创建本文中所用的证书，并在下游设备的连接字符串中引用。 网关主机名必须能够解析成 IP 地址，不管是使用 DNS 还是主机文件条目。

## <a name="generate-certificates-with-windows"></a>在 Windows 中生成证书

使用本部分中的步骤在 Windows 上生成测试证书。 可以使用 Windows 计算机生成证书，然后将其复制到运行任何受支持操作系统的任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="install-openssl"></a>安装 OpenSSL

在用于生成证书的计算机上安装 OpenSSL for Windows。 如果已在 Windows 设备上安装 OpenSSL，则可以跳过此步骤，但请确保 PATH 环境变量中包含 openssl.exe。 

可通过多种方法安装 OpenSSL：

* **更轻松：** 下载并安装任何[第三方 OpenSSL 二进制文件](https://wiki.openssl.org/index.php/Binaries)，例如从 [SourceForge 上的 OpenSSL](https://sourceforge.net/projects/openssl/) 下载并安装。 将 openssl.exe 的完整路径添加到 PATH 环境变量。 
   
* **推荐：** 在计算机上下载 OpenSSL 源代码并自行生成二进制文件，或者通过 [vcpkg](https://github.com/Microsoft/vcpkg) 生成。 下面列出的说明使用 vcpkg 下载源代码，并在 Windows 计算机上编译和安装 OpenSSL，所用的步骤都很简单。

   1. 导航到要安装 vcpkg 的目录。 我们将此目录称作 *\<VCPKGDIR>* 。 按照说明下载并安装 [vcpkg](https://github.com/Microsoft/vcpkg)。
   
   2. 安装 vcpkg 后，在 PowerShell 提示符下运行以下命令以安装适用于 Windows x64 的 OpenSSL 包。 此安装通常需要大约 5 分钟才能完成。

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```
   3. 将 `<VCPKGDIR>\installed\x64-windows\tools\openssl` 添加到 PATH 环境变量，以便可以调用 openssl.exe 文件。

### <a name="prepare-creation-scripts"></a>准备创建脚本

Azure IoT Edge Git 存储库包含可用于生成测试证书的脚本。 在本部分，你将克隆 IoT Edge 存储库并执行脚本。 

1. 在管理员模式下打开 PowerShell 窗口。 

2. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 使用 `git clone` 命令或[下载 ZIP](https://github.com/Azure/iotedge/archive/master.zip)。 

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. 导航到要在其中工作的目录。 整篇文章将此目录称为 *\<WRKDIR>* 。 所有证书和密钥将此工作目录中创建。

4. 将克隆的存储库中的配置文件和脚本文件复制到该工作目录。 

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   如果下载了 ZIP 格式的存储库，则文件夹名称为 `iotedge-master`，路径的剩余部分相同。 
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

7. 将脚本使用的函数放入 PowerShell 全局命名空间中。
   
   ```powershell
   . .\ca-certs.ps1
   ```

   PowerShell 窗口中会显示一条警告，指出此脚本生成的证书仅用于测试目的，不应在生产方案中使用。

8. 验证 OpenSSL 是否已正确安装，并确保现有的证书不会发生名称冲突。 如果出现问题，脚本应说明如何在系统上修复这些问题。

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="create-certificates"></a>创建证书

在本部分，我们将创建三个证书，然后将它们连接起来以形成一个链。 将证书放入链文件可在 IoT Edge 网关设备和任何下游设备上轻松安装这些证书。  

1. 创建根 CA 证书，并使用它来签署一个中间证书。 证书全部放在工作目录中。

   ```powershell
   New-CACertsCertChain rsa
   ```

   此脚本命令将创建多个证书和密钥文件，但我们稍后在本文中只会引用其中特定的一个文件：
   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

2. 使用以下命令创建 IoT Edge 设备 CA 证书和私钥。 提供网关主机名（可在网关设备上的 iotedge\config.yaml 文件中找到）。 在生成证书的过程中，将使用该网关主机名来为文件命名。 

   ```powershell
   New-CACertsEdgeDevice "<gateway hostname>"
   ```

   此脚本命令将创建多个证书和密钥文件，包括我们稍后在本文中引用的两个文件：
   * `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`

现已创建证书，接下来请转到[在网关上安装证书](#install-certificates-on-the-gateway)

## <a name="generate-certificates-with-linux"></a>在 Linux 中生成证书

使用本部分中的步骤在 Linux 上生成测试证书。 可以使用 Linux 计算机生成证书，然后将其复制到运行任何受支持操作系统的任何 IoT Edge 设备。 

在本部分中生成的证书仅用于测试目的。 

### <a name="prepare-creation-scripts"></a>准备创建脚本

Azure IoT Edge Git 存储库包含可用于生成测试证书的脚本。 在本部分，你将克隆 IoT Edge 存储库并执行脚本。 

1. 克隆包含用于生成非生产证书的脚本的 git 存储库。 这些脚本可帮助创建必要的证书来设置透明网关。 

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. 导航到要在其中工作的目录。 在整篇文章中，此目录称为 *\<WRKDIR>* 。 所有证书和密钥文件都将在此目录中创建。
  
3. 将克隆的 IoT Edge 存储库中的配置文件和脚本文件复制到该工作目录。

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

   该脚本将创建多个证书和密钥。 请记下我们将在下一部分引用的那一个：
   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`

2. 使用以下命令创建 IoT Edge 设备 CA 证书和私钥。 提供网关主机名（可在网关设备上的 iotedge/config.yaml 文件中找到）。 在生成证书的过程中，将使用该网关主机名来为文件命名。 

   ```bash
   ./certGen.sh create_edge_device_certificate "<gateway hostname>"
   ```

   该脚本将创建多个证书和密钥。 请记下我们将在下一部分引用的那两个： 
   * `<WRKDIR>/certs/iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<gateway hostname>.key.pem`

## <a name="install-certificates-on-the-gateway"></a>在网关上安装证书

创建证书链后，需要在 IoT Edge 网关设备上安装它，并将 IoT Edge 运行时配置为引用新证书。 

1. 复制 *\<WRKDIR>* 中的以下文件。 将其保存在 IoT Edge 设备上的任意位置。 我们将 IoT Edge 设备上的目标目录称作 *\<CERTDIR>* 。 

   * 设备 CA 证书 - `<WRKDIR>\certs\iot-edge-device-<gateway hostname>-full-chain.cert.pem`
   * 设备 CA 私钥 - `<WRKDIR>\private\iot-edge-device-<gateway hostname>.key.pem`
   * 根 CA - `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

   可以使用 [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) 之类的服务或[安全复制协议](https://www.ssh.com/ssh/scp/)之类的功能来移动证书文件。  如果在 IoT Edge 设备本身上生成了证书，则可以跳过此步骤，并使用工作目录的路径。

2. 打开 IoT Edge 安全守护程序配置文件。 

   * Windows：`C:\ProgramData\iotedge\config.yaml`
   * Linux：`/etc/iotedge/config.yaml`

3. 将 config.yaml 文件中的 **certificate** 属性设置为 IoT Edge 设备上的证书和密钥文件的完整路径。 删除 certificate 属性前面的 `#` 字符，以取消注释四个代码行。 请记住，yaml 中的缩进为两个空格。

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

4. 在 Linux 设备上，确保用户 **iotedge** 对保存证书的目录拥有读取权限。 

## <a name="deploy-edgehub-to-the-gateway"></a>将 EdgeHub 部署到网关

首次在设备上安装 IoT Edge 时，只会自动启动一个系统模块，即 IoT Edge 代理。 要使设备充当网关，需要两个系统模块。 如果以前尚未将任何模块部署到网关设备，请创建设备的初始部署，以启动第二个系统模块，即 IoT Edge 中心。 该部署看上去是空的，因为尚未在向导中添加任何模块，但它会确保运行这两个系统模块。 

可以使用 `iotedge list` 命令检查哪些模块正在设备上运行。 如果列表中仅返回了模块 **edgeAgent** 而未返回 **edgeHub**，请使用以下步骤：

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

## <a name="open-ports-on-gateway-device"></a>在网关设备上打开端口

标准 IoT Edge 设备不需要任何入站连接便可工作，因为与 IoT 中心之间的所有通信都是通过出站连接执行的。 网关设备则不同，因为它们需要从其下游设备接收消息。 如果下游设备与网关设备之间有防火墙，则也需要能够通过防火墙进行通信。

要使网关方案能够正常工作，必须为来自下游设备的入站流量打开 IoT Edge 中心的至少一个受支持协议。 支持的协议为 MQTT、AMQP 和 HTTPS。 

| Port | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>路由来自下游设备的消息
IoT Edge 运行时可以像模块发送的消息一样路由从下游设备发送的消息。 使用此功能可将任何数据发送到云之前在网关上运行的模块中执行分析。 

目前，对由下游设备发送的消息进行路由的方式是将它们与由模块发送的消息区分开来。 由模块发送的消息全都包含名为“connectionModuleId”的系统属性，但由下游设备发送的消息则不包含此属性。 可以使用路由的 WHERE 子句以排除包含该系统属性的任何消息。 

以下示例路由可将消息从任何下游设备发送到名为 `ai_insights` 的模块，然后从 `ai_insights` 发送到 IoT 中心。

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

从 IoT Edge 运行时 [v1.0.4 版本](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4)开始，可配置网关设备和与之连接的下游设备，以处理扩展脱机操作。 

借助此功能，本地模块或下游设备可根据需要向 IoT Edge 设备重新进行身份验证，即使从 IoT 中心断开连接也可使用消息和方法相互进行通信。 有关详细信息，请参阅[了解 IoT Edge 设备、模块和子设备的扩展脱机功能](offline-capabilities.md)。

若要启用扩展脱机功能，请在 IoT Edge 网关设备和要与之连接的下游设备之间建立父子关系。 [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)中更详细地介绍了这些步骤。

## <a name="next-steps"></a>后续步骤

配置一个充当透明网关的 IoT Edge 设备后，需将下游设备配置为信任该网关，并向其发送消息。 有关详细信息，请参阅[将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)和[在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)。
