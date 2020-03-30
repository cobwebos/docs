---
title: 通过 Windows PowerShell 界面连接到和管理 Microsoft Azure 数据盒边缘设备 |微软文档
description: 描述如何通过 Windows PowerShell 界面连接到数据框边缘并管理数据框边缘。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265475"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>通过 Windows PowerShell 管理 Azure 数据框边缘设备

Azure 数据框边缘解决方案允许您处理数据，并将其通过网络发送到 Azure。 本文介绍了数据盒边缘设备的一些配置和管理任务。 您可以使用 Azure 门户、本地 Web UI 或 Windows PowerShell 界面来管理设备。

本文重点介绍使用 PowerShell 接口执行的任务。

本文包括以下过程：

- 连接到 PowerShell 接口
- 创建支持包
- 上传证书
- 重置设备
- 查看设备信息
- 获取计算日志
- 监控和排除计算模块故障

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 接口

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

您还可以上载 IoT Edge 证书，以便在 IoT Edge 设备与可能连接到它的下游设备之间启用安全连接。 您需要安装三个 IoT 边缘证书 *（.pem*格式）：

- 根 CA 证书或所有者 CA
- 设备 CA 证书
- 设备密钥证书

下面的示例显示了此 cmdlet 安装 IoT Edge 证书的用法：

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
运行此 cmdlet 时，系统将提示您提供网络共享的密码。

有关证书的详细信息，请转到[Azure IoT 边缘证书](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs)或在[网关上安装证书](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway)。

## <a name="view-device-information"></a>查看设备信息
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重置设备

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>获取计算日志

如果在设备上配置了计算角色，您还可以通过 PowerShell 接口获取计算日志。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 使用`Get-AzureDataBoxEdgeComputeRoleLogs`获取设备的计算日志。

    下面的示例显示了此 cmdlet 的用法：

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    以下是用于 cmdlet 的参数的说明：
    - `Path`：提供要创建计算日志包的共享的网络路径。
    - `Credential`：提供网络共享的用户名。 运行此 cmdlet 时，需要提供共享密码。
    - `FullLogCollection`：此参数可确保日志包包含所有计算日志。 默认情况下，日志包仅包含日志的子集。

## <a name="monitor-and-troubleshoot-compute-modules"></a>监控和排除计算模块故障

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>退出远程会话

要退出远程 PowerShell 会话，关闭 PowerShell 窗口。

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。
