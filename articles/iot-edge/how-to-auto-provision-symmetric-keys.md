---
title: 使用对称密钥证明自动设置使用 DPS 的设备-Azure IoT Edge |Microsoft Docs
description: 使用对称密钥证明通过设备预配服务测试 Azure IoT Edge 的自动设备预配
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a91162271bf36e1d68e087a60723497e7fc261ec
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275741"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>使用对称密钥证明创建和预配 IoT Edge 设备

可以使用[设备预配服务](../iot-dps/index.yml)自动预配 Azure IoT Edge 设备，就像预配未启用 Edge 的设备一样。 如果你不熟悉自动预配过程，请在继续操作之前查看[自动预配的概念](../iot-dps/concepts-auto-provisioning.md)。

本文介绍如何在 IoT Edge 设备上使用对称密钥证明创建设备预配服务的单个注册, 步骤如下:

* 创建 IoT 中心设备预配服务 (DPS) 的实例。
* 为设备创建个人注册。
* 安装 IoT Edge 运行时并连接到 IoT 中心。

对称密钥证明是一种通过设备预配服务实例对设备进行身份验证的简单方法。 此证明方法表示不熟悉设备预配或不具备严格安全要求的开发人员的“Hello world”体验。 使用[TPM](../iot-dps/concepts-tpm-attestation.md)的设备证明更为安全, 应用于更严格的安全要求。

## <a name="prerequisites"></a>系统必备

* 活动 IoT 中心
* 物理或虚拟设备

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>设置 IoT 中心设备预配服务

在 Azure 中创建 IoT 中心设备预配服务的新实例，并将其链接到 IoT 中心。 可以遵照[设置 IoT 中心 DPS](../iot-dps/quick-setup-auto-provision.md) 中的说明操作。

运行设备预配服务后，从概述页复制“ID 范围”的值。  配置 IoT Edge 运行时时，需要使用此值。

## <a name="choose-a-unique-registration-id-for-the-device"></a>选择设备的唯一注册 ID

必须定义唯一注册 ID 来标识每个设备。 可以使用 MAC 地址、序列号或设备中的任何唯一信息。

在此示例中，我们使用 MAC 地址和序列号的组合，构成以下注册 ID 字符串。

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

为设备创建一个唯一注册 ID。 有效字符为小写字母数字和短划线（“-”）。

## <a name="create-a-dps-enrollment"></a>创建 DPS 注册

使用设备的注册 ID 在 DPS 中创建单个注册。

在 DPS 中创建注册时，可以声明“初始设备孪生状态”。  在设备孪生中可以设置标记，以便按解决方案中所需的任何指标（例如区域、环境、位置或设备类型）将设备分组。 这些标记用于创建[自动部署](how-to-deploy-monitor.md)。

> [!TIP]
> 使用对称密钥证明时, 还可以使用组注册, 其中包含与单个注册相同的决策。

1. 在[Azure 门户](https://portal.azure.com)中, 导航到 IoT 中心设备预配服务的实例。

1. 在“设置”下，选择“管理注册”。  

1. 选择“添加个人注册”，然后完成以下步骤以配置注册：   

   1. 对于 "**机制**", 请选择 "**对称密钥**"。

   1. 选中 "**自动生成密钥**" 复选框。

   1. 提供你为设备创建的**注册 ID** 。

   1. 如果需要, 请为设备提供**IoT 中心设备 ID** 。 可以使用设备 ID 将单个设备指定为模块部署的目标。 如果未提供设备 ID，则会使用注册 ID。

   1. 选择 " **True** " 以声明为 IoT Edge 设备注册注册。 对于组注册, 所有设备都必须 IoT Edge 设备上, 或者都不能。

   1. 接受设备预配服务的分配策略中的默认值, 以了解**如何将设备分配给集线器**或选择特定于此注册的其他值。

   1. 选择要将设备连接到的链接“IoT 中心”。  可以选择多个中心，设备将根据所选的分配策略分配到其中的一个中心。

   1. 选择在第一次请求预配时,**如何对设备数据进行重新设置**。

   1. 根据需要，将标记值添加到“初始设备孪生状态”。  可以使用标记将设备组指定为模块部署的目标。 例如：

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

   1. 确保 "**启用条目**" 设置为 "**启用**"。

   1. 选择**保存**。

既然此设备已存在注册，IoT Edge 运行时在安装期间可以自动预配设备。 请确保在创建设备密钥时复制注册的**主密钥**值以使用。

## <a name="derive-a-device-key"></a>派生一个设备密钥

设备使用派生的设备密钥和你的唯一注册 ID, 在预配期间使用注册执行对称密钥证明。 若要生成设备密钥, 请使用从 DPS 注册复制的密钥来计算设备的唯一注册 ID 的[HMAC-SHA256](https://wikipedia.org/wiki/HMAC) , 并将结果转换为 Base64 格式。

不要在设备代码中包含注册的主密钥或辅助密钥。

### <a name="linux-workstations"></a>Linux 工作站

如果使用的是 Linux 工作站，可以使用 openssl 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

将**REG_ID**的值替换为设备的注册 ID。

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>基于 Windows 的工作站

如果使用的是基于 Windows 的工作站，可以使用 PowerShell 生成派生的设备密钥，如以下示例中所示。

将“键”  值替换为前面记录的“主键”  。

将**REG_ID**的值替换为设备的注册 ID。

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

IoT Edge 运行时部署在所有 IoT Edge 设备上。 该运行时的组件在容器中运行，允许你将其他容器部署到设备，以便在边缘上运行代码。

预配设备时, 需要以下信息:

* DPS **ID 范围**值
* 你创建的设备**注册 ID**
* 用于对称密钥证明的设备的派生设备密钥

### <a name="linux-device"></a>Linux 设备

按照设备体系结构的说明进行操作。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。

* [在 Linux (x64) 上安装 Azure IoT Edge 运行时](how-to-install-iot-edge-linux.md)
* [在 Linux 上安装 Azure IoT Edge 运行时 (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

对称密钥预配的配置文件中的部分如下所示:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

将`{scope_id}`、 `{registration_id}`和的占位符值替换为先前收集的数据。`{symmetric_key}`

### <a name="windows-device"></a>Windows 设备

按照说明在生成了派生设备密钥的设备上安装 IoT Edge 运行时。 确保将 IoT Edge 运行时配置为自动预配而不是手动预配。

[安装和自动预配 IoT Edge](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>验证是否成功安装

如果运行时成功启动，则可以进入 IoT 中心，开始将 IoT Edge 模块部署到你的设备。 在设备上使用以下命令验证是否已成功安装并启动运行时。

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

可以验证是否使用了在设备预配服务中创建的个人注册。 在 Azure 门户中导航到设备预配服务实例。 打开创建的个人注册的注册详细信息。 注意注册状态是否为“已分配”并且设备 ID 已列出。 

## <a name="next-steps"></a>后续步骤

使用设备预配服务注册过程可以在预配新设备的同时，设置设备 ID 和设备孪生标记。 可以在自动设备管理中，使用这些值将单个设备或设备组指定为目标。 了解如何[使用 Azure 门户大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)，或[使用 Azure CLI](how-to-deploy-monitor-cli.md) 执行此操作。
