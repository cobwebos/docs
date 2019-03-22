---
title: 连接到并管理通过 Windows PowerShell 接口的 Microsoft Azure 数据框网关设备 |Microsoft Docs
description: 介绍如何连接到并通过 Windows PowerShell 接口管理数据框网关。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 0ca570235ac2a87b62c5d0fcebbd24dc5186e37d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57556509"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>管理通过 Windows PowerShell 的 Azure 数据框网关设备

Azure 数据框网关解决方案，可通过网络将数据发送到 Azure。 本文介绍了一些数据框网关设备的配置和管理任务。 可以使用 Azure 门户、 本地 web UI 或 Windows PowerShell 界面来管理你的设备。

本文重点介绍使用 PowerShell 接口执行操作的任务。

本文包括以下过程：

- 连接到 PowerShell 界面
- 启动支持会话
- 创建支持包
- 上传证书
- 在非 DHCP 环境中启动
- 查看设备信息

> [!IMPORTANT]
> Azure 数据框网关当前处于公共预览状态。
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

## <a name="boot-up-in-non-dhcp-environment"></a>在非 DHCP 环境中启动

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>查看设备信息

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>后续步骤

- 在 Azure 门户中部署 [Azure Data Box Gateway](data-box-gateway-deploy-prep.md)。
