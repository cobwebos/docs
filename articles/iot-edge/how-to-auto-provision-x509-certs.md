---
title: 使用 X.509 证书通过 DPS 自动预配设备 - Azure IoT Edge | Microsoft Docs
description: 使用 X.509 证书通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 761b031916dd9ead71f5be6a6887208a1f200f58
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966128"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>使用 X.509 证书创建和预配 IoT Edge 设备

借助 [Azure IoT 中心设备预配服务 (DPS)](../iot-dps/index.yml)，可以使用 X.509 证书自动预配 IoT Edge 设备。 如果你不熟悉自动预配过程，请在继续操作之前查看[预配](../iot-dps/about-iot-dps.md#provisioning-process)概述。

本文介绍如何通过以下步骤，在 IoT Edge 设备上使用 X.509 证书创建设备预配服务注册：

* 生成证书和密钥。
* 为某个设备创建单个注册，或者为一组设备创建组注册。
* 安装 IoT Edge 运行时并将设备注册到 IoT 中心。

将 X.509 证书用作一种证明机制是扩大生产规模和简化设备设置的极佳途径。 X.509 证书通常排列在证书信任链中。 从自签名证书或受信任的根证书开始，证书链中的每个证书为下一级证书签名。 此模式创建了从设备上安装的根证书到每个中间证书，再到最终“叶”证书的委托信任链。

## <a name="prerequisites"></a>先决条件

* 活动的 IoT 中心。
* 一个充当 IoT Edge 设备的物理设备或虚拟设备。
* 已安装最新版本的 [Git](https://git-scm.com/download/)。
* Azure 中的一个 IoT 中心设备预配服务实例，该实例已链接到 IoT 中心。
  * 如果你没有设备预配服务实例，请按照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明设置一个。
  * 运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

## <a name="generate-device-identity-certificates"></a>生成设备标识证书

设备标识证书是通过证书信任链连接到顶级 X.509 证书颁发机构 (CA) 证书的叶证书。 设备标识证书的公用名 (CN) 必须设置为该设备在 IoT 中心内使用的设备 ID。

设备标识证书仅用于预配 IoT Edge 设备，以及通过 Azure IoT 中心对设备进行身份验证。 设备标识证书不是签名证书，这与 CA 证书不同，后者是 IoT Edge 设备向模块或叶设备提供的用于验证的证书。 有关详细信息，请参阅 [Azure IoT Edge 证书用法详细信息](iot-edge-certs.md)。

创建设备标识证书后，应会获得两个文件：一个包含证书公共部分的 .cer 或 .pem 文件，一个包含证书私钥的 .cer 或 .pem 文件。 如果你打算在 DPS 中使用组注册，则还需要提供同一证书信任链中某个中间证书或根 CA 证书的公共部分。

若要使用 X.509 设置自动预配，需要以下文件：

* 设备标识证书及其私钥证书。 如果创建单个注册，则设备标识证书会上传到 DPS。 私钥会传递到 IoT Edge 运行时。
* 完整链证书，其中应至少包含设备标识和中间证书。 完整链证书会传递到 IoT Edge 运行时。
* 证书信任链中的中间或根 CA 证书。 如果创建组注册，则此证书会上传到 DPS。

> [!NOTE]
> 目前，libiothsm 中的限制会阻止使用在2038年1月1日或之后过期的证书。

### <a name="use-test-certificates"></a>使用测试证书

如果你没有可用于创建新标识证书的证书颁发机构，但想要尝试此方案，可以使用 Azure IoT Edge Git 存储库中包含的脚本来生成测试证书。 这些证书仅用于开发测试，不得在生产环境中使用。

若要创建测试证书，请遵循[创建演示证书用于测试 IoT Edge 设备功能](how-to-create-test-certificates.md)中的步骤。 请完成其中的两个必要部分来设置证书生成脚本，并创建根 CA 证书。 然后，遵循相应的步骤创建设备标识证书。 完成后，应会获得以下证书链和密钥对：

Linux：

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

需要在 IoT Edge 设备上使用这两个证书。 若要在 DPS 中使用单独的注册，需要上传 .cert.pem 文件。 若要在 DPS 中使用组注册，则还需要上传同一证书信任链中的某个中间证书或根 CA 证书。 如果使用演示证书，请将 `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` 证书用于组注册。

## <a name="create-a-dps-individual-enrollment"></a>创建 DPS 单独注册

使用生成的证书和密钥在 DPS 中为单个 IoT Edge 设备创建单独注册。 单独注册采用设备标识证书的公共部分，并将其与设备上的证书进行匹配。

若要预配多个 IoT Edge 设备，请遵循下一部分[创建 DPS 组注册](#create-a-dps-group-enrollment)中的步骤。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

有关设备预配服务中的注册的详细信息，请参阅[如何管理设备注册](../iot-dps/how-to-manage-enrollments.md)。

   > [!TIP]
   > 在 Azure CLI 中，可以创建[注册](/cli/azure/ext/azure-iot/iot/dps/enrollment)或[注册组](/cli/azure/ext/azure-iot/iot/dps/enrollment-group)，并使用“支持 Edge”  标志来指定某个设备或设备组是 IoT Edge 设备。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。 

1. 选择“添加个人注册”，然后完成以下步骤以配置注册：   

   * **机制**：选择“X.509”。 

   * **主要证书 .pem 或 .cer 文件**：上传设备标识证书中的公共文件。 如果使用脚本生成了测试证书，请选择以下文件：

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT 中心设备 ID**：根据需要，为设备提供一个 ID。 可以使用设备 ID 将单个设备指定为模块部署的目标。 如果未提供设备 ID，将使用 X.509 证书中的公用名 (CN)。

   * **IoT Edge 设备**：选择“True”，声明该注册适用于 IoT Edge 设备。 

   * **选择此设备可分配到的 IoT 中心**：选择要将设备连接到的链接 IoT 中心。 可以选择多个中心，设备将根据所选的分配策略分配到其中的一个中心。

   * **初始设备孪生状态**：如果需要，请添加要添加到设备孪生的标记值。 可以使用标记将设备组指定为自动部署的目标。 例如：

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. 选择“保存” 。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。 转到[安装 IoT Edge 运行时](#install-the-iot-edge-runtime)部分来设置 IoT Edge 设备。

## <a name="create-a-dps-group-enrollment"></a>创建 DPS 组注册

使用生成的证书和密钥在 DPS 中为多个 IoT Edge 设备创建组注册。 组注册使用证书信任链中的某个中间证书或根 CA 证书，该证书用于生成单个设备标识证书。

若要预配单个 IoT Edge 设备，请遵循前一部分[创建 DPS 单独注册](#create-a-dps-individual-enrollment)中的步骤。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-at-scale.md)。

### <a name="verify-your-root-certificate"></a>验证根证书

创建注册组时，可以选择使用已验证的证书。 可以在 DPS 通过证明你拥有根证书来验证某个证书。 有关详细信息，请参阅[如何对 X.509 CA 证书执行所有权证明](../iot-dps/how-to-verify-certificates.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在左侧菜单中选择“证书”。 

1. 选择“添加”以添加新证书。 

1. 输入证书的易记名称，然后浏览到表示 X.509 证书公共部分的 .cer 或 .pem 文件。

   如果使用演示证书，请上传 `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` 证书。

1. 选择“保存”  。

1. 该证书现在应会列在“证书”页上。  选择该证书打开其详细信息。

1. 选择“生成验证码”，然后复制生成的代码。 

1. 无论你是使用自己的 CA 证书还是演示证书，都可以使用 IoT Edge 存储库中提供的验证工具来执行所有权证明。 验证工具将使用你的 CA 证书，来为使用提供的验证码作为使用者名称的新证书签名。

   * Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * Linux：

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. 在 Azure 门户上的相同证书详细信息页中，上传新生成的验证证书。

1. 选择“验证”  。

### <a name="create-enrollment-group"></a>创建注册组

有关设备预配服务中的注册的详细信息，请参阅[如何管理设备注册](../iot-dps/how-to-manage-enrollments.md)。

1. 在 [Azure 门户](https://portal.azure.com)中，导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加注册组”，然后完成以下步骤以配置注册： 

   * **组名称**：为此组注册提供一个易记的名称。

   * **证明类型**：选择“证书”。 

   * **IoT Edge 设备**：选择“True”。  对于组注册，所有设备必须是 IoT Edge 设备，或者都不是 IoT Edge 设备。

   * **证书类型**：如果 DPS 中已存储了你的某个已验证 CA 证书，请选择“CA 证书”；如果你只是想要为此注册上传新文件，请选择“中间证书”。  

   * **主要证书**：如果在上一部分选择了“CA 证书”，请从下拉列表中选择证书。 如果选择了“中间证书”，请上传证书信任链中用于生成设备标识证书的 CA 证书中的公共文件。

   * **选择此设备可分配到的 IoT 中心**：选择要将设备连接到的链接 IoT 中心。 可以选择多个中心，设备将根据所选的分配策略分配到其中的一个中心。

   * **初始设备孪生状态**：如果需要，请添加要添加到设备孪生的标记值。 可以使用标记将设备组指定为自动部署的目标。 例如：

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

1. 选择“保存” 。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。 转到下一部分来设置 IoT Edge 设备。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。

按照 [安装 Azure IoT Edge 运行时](how-to-install-iot-edge.md)中的步骤操作，然后返回到本文来预配设备。

只有 IoT Edge 1.0.9 或更高版本才支持使用 DPS 进行的 X.509 预配。

## <a name="configure-the-device-with-provisioning-information"></a>用预配信息配置设备

在设备上安装运行时后，请使用它连接到设备预配服务和 IoT 中心所用的信息来配置设备。

准备好以下信息：

* DPS 的“ID 范围”值。  可以从 Azure 门户中 DPS 实例的概述页检索此值。
* 设备上的设备标识证书链文件。
* 设备上的设备标识密钥文件。
* 可选注册 ID。 如果未提供，则将从设备标识证书中的公用名提取 ID。

### <a name="linux-device"></a>Linux 设备

1. 在 IoT Edge 设备上打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. 查找文件的 "预配配置" 部分。 取消注释 DPS 对称密钥预配的行，并确保注释掉任何其他预配行。

   `provisioning:`该行应没有前面的空格，并且嵌套项应缩进两个空格。

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. `scope_id`将、和的值更新 `identity_cert` `identity_pk` 为你的 DPS 和设备信息。

   将 X.509 证书和密钥信息添加到 config.yaml 文件时，应以文件 URI 的形式提供路径。 例如：

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. 如果需要，请提供设备的 `registration_id`，或者保持注释掉此行，以使用标识证书的 CN 名称注册设备。

1. 重启 IoT Edge 运行时，使之拾取你在设备上所做的所有配置更改。

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="windows-device"></a>Windows 设备

1. 在管理员模式下打开 PowerShell 窗口。 在安装 IoT Edge 而不是 PowerShell (x86) 时，请确保使用 PowerShell 的 AMD64 会话。

1. Initialize-IoTEdge 命令在计算机上配置 IoT Edge 运行时  。 命令默认使用 Windows 容器进行手动设置，因此，请使用 `-DpsX509` 标志将自动预配用于 x.509 证书身份验证。

   请将 `{scope_id}`、`{identity cert chain path}` 和 `{identity key path}` 的占位符值替换为 DPS 实例中的相应值，以及设备上的文件路径。

   `-RegistrationId {registration_id}`如果要将设备 ID 设置为除标识证书的 CN 名称以外的其他名称，请添加。

   `-ContainerOs Linux`如果使用的是 Windows 上的 Linux 容器，请添加参数。

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >config.yaml 文件以文件 URI 的形式存储证书和密钥信息。 但是，Initialize-IoTEdge 命令将为你处理此格式设置步骤，因此，可以提供证书和密钥文件在设备上的绝对路径。

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

在设备上使用以下命令验证是否已成功安装并启动运行时。

### <a name="linux-device"></a>Linux 设备

检查 IoT Edge 服务的状态。

```cmd/sh
systemctl status iotedge
```

检查服务日志。

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

列出正在运行的模块。

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Windows 设备

检查 IoT Edge 服务的状态。

```powershell
Get-Service iotedge
```

检查服务日志。

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

列出正在运行的模块。

```powershell
iotedge list
```

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-at-scale.md)，或[使用 Azure CLI](how-to-deploy-cli-at-scale.md) 执行此操作。
