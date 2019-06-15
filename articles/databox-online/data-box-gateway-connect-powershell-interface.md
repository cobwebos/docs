---
title: 连接到并管理通过 Windows PowerShell 接口的 Microsoft Azure 数据框网关设备 |Microsoft Docs
description: 介绍如何连接到并通过 Windows PowerShell 接口管理数据框网关。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64415167"
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

## <a name="connect-to-the-powershell-interface"></a>连接到 PowerShell 界面

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

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
