---
title: 连接到并管理 Microsoft Azure 数据框边缘设备通过 Windows PowerShell 接口 |Microsoft Docs
description: 介绍如何连接到并通过 Windows PowerShell 接口管理数据框的边缘。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b3effdbace2be582bfe85d0402088f8aa0d96fe7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556449"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell-preview"></a>管理 Azure 数据框边缘设备通过 Windows PowerShell （预览版）

Azure 数据框边缘解决方案，可处理数据并将其通过网络发送到 Azure。 本文介绍了一些数据框 Edge 设备的配置和管理任务。 可以使用 Azure 门户、 本地 web UI 或 Windows PowerShell 界面来管理你的设备。

本文重点介绍使用 PowerShell 接口执行操作的任务。

本文包括以下过程：

- 连接到 PowerShell 界面
- 启动支持会话
- 创建支持包
- 上传证书
- 将设备重置
- 查看设备信息
- 获取计算日志
- 监视和故障排除计算模块

> [!IMPORTANT]
> Azure 数据框边缘目前处于公共预览状态。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 界面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>启动支持会话

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>创建支持包

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>上传证书

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="view-device-information"></a>查看设备信息
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>重置设备

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>获取计算日志

如果你的设备上配置计算角色，则还可以通过 PowerShell 接口获取计算日志。

1. [连接到 PowerShell 界面](#connect-to-the-powershell-interface)。
2. 使用`Get-AzureDataBoxEdgeComputeRoleLogs`若要获取你的设备计算日志。

    下面的示例显示了此 cmdlet 的用法：

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    下面是使用该 cmdlet 的参数的说明： 
    - `Path`：提供你想要创建计算日志包的共享网络路径。
    - `Credential`：为网络共享中提供的用户名和密码。
    - `RoleInstanceName`：提供此字符串`IotRole`为此参数。
    - `FullLogCollection`：此参数可确保日志包将包含计算的所有日志。 默认情况下，日志包仅包含一部分的日志。


## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Data Box Edge](data-box-edge-deploy-prep.md)。
