---
title: 通过 Windows PowerShell 界面连接和管理 Microsoft Azure Stack Edge 设备 |Microsoft Docs
description: 描述如何通过 Windows PowerShell 界面连接到 Azure Stack 边缘并进行管理。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: ee69015b52024ba4edaaa414a0d9dda753510b3e
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570091"
---
# <a name="manage-an-azure-stack-edge-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure Stack Edge 设备

Azure Stack Edge 解决方案使你能够处理数据，并通过网络将数据发送到 Azure。 本文介绍 Azure Stack Edge 设备的一些配置和管理任务。 你可以使用 Azure 门户、本地 web UI 或 Windows PowerShell 界面来管理你的设备。

本文重点介绍使用 PowerShell 接口执行的任务。 

本文包括以下过程：

- 连接到 PowerShell 接口
- 创建支持包
- 上传证书
- 重置设备
- 查看设备信息
- 获取计算日志
- 计算模块的监视和故障排除

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

你还可以上传 IoT Edge 证书，以在你的 IoT Edge 设备与可能连接到该设备的下游设备之间启用安全连接。 需要安装三个 IoT Edge 证书（*pem*格式）：

- 根 CA 证书或所有者 CA
- 设备 CA 证书
- 设备密钥证书

下面的示例演示如何使用此 cmdlet 安装 IoT Edge 证书：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
运行此 cmdlet 时，系统将提示你提供网络共享的密码。

有关证书的详细信息，请参阅[Azure IoT Edge 证书](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs)"或[" 在网关上安装证书 "](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

## <a name="view-device-information"></a>查看设备信息
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重置设备

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>获取计算日志

如果在设备上配置了计算角色，则还可以通过 PowerShell 界面获取计算日志。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. `Get-AzureDataBoxEdgeComputeRoleLogs`使用获取设备的计算日志。

    以下示例显示了此 cmdlet 的用法：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    下面是用于 cmdlet 的参数的说明：
    - `Path`：提供要在其中创建计算日志包的共享的网络路径。
    - `Credential`：提供网络共享的用户名。 运行此 cmdlet 时，需要提供共享密码。
    - `FullLogCollection`：此参数可确保日志包将包含所有计算日志。 默认情况下，日志包仅包含一小部分的日志。

## <a name="monitor-and-troubleshoot-compute-modules"></a>计算模块的监视和故障排除

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>退出远程会话

若要退出远程 PowerShell 会话，请关闭 PowerShell 窗口。

## <a name="next-steps"></a>后续步骤

- 将[Azure Stack Edge](azure-stack-edge-deploy-prep.md)部署 Azure 门户。
