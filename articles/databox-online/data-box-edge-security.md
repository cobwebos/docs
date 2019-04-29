---
title: Azure 数据框边缘安全 |Microsoft Docs
description: 介绍保护在 Azure 数据框边缘设备、 服务和数据的本地安全和隐私功能和在云中。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 8823aebe17a5446b3c507878833c2525c338dde1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60756156"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure 数据框边缘安全和数据保护

安全性是主要考虑因素时要采用新技术，尤其是当该技术使用包含机密或专有数据。 Azure 数据框边缘可帮助您确保只有经过授权的实体可以查看、 修改或删除你的数据。

这篇文章介绍帮助保护的每个解决方案组件和在其中存储的数据的数据框边缘安全功能。

Azure 数据框边缘包含四个彼此交互的主要组件：

- **在 Azure 中托管的数据框边缘服务**。 管理资源的使用来创建设备顺序，配置设备，，然后完成到跟踪的顺序。
- **数据框边缘设备**。 传输设备，以便可以在本地数据导入 Azure 运送给你。
- **连接到设备的客户端/主机**。 中的客户端基础结构连接到数据框边缘设备，并且包含需要保护的数据。
- **云存储**。 Azure 云平台中存储数据的位置。 此位置通常是存储帐户链接到你创建的数据框边缘资源。

## <a name="data-box-edge-service-protection"></a>数据框边缘服务保护

数据框边缘服务是在 Azure 中托管的管理服务。 该服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>数据框边缘设备保护

数据框边缘设备是在本地设备，可帮助将数据转换通过本地处理，然后将其发送到 Azure。 你的设备：

- 需要激活密钥来访问数据框边缘服务。
- 是始终受到保护的设备密码。
- 是锁定的设备。 设备 BMC 和 BIOS 受密码保护。 BIOS 受限制的用户访问权限。
- 已启用安全启动。
- 在运行 Windows Defender Device Guard。 Device Guard 可以运行仅受信任的应用程序在你的代码完整性策略中定义。

### <a name="protect-the-device-via-activation-key"></a>保护设备通过激活密钥

仅授权的数据框边缘设备允许加入你的 Azure 订阅中创建的数据框边缘服务。 若要授权某个设备，需要使用激活密钥来激活设备的数据框边缘服务。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](data-box-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>保护通过密码的设备

密码确保只有授权的用户可以访问你的数据。 数据框边缘设备中启动，锁定状态。

可以：

- 连接到本地 web UI 中通过浏览器设备，然后提供密码以登录到设备。
- 远程连接到设备的 PowerShell 接口通过 HTTP。 默认情况下，远程管理启用。 然后可以提供用于登录到设备的设备密码。 有关详细信息，请参阅[远程连接到数据框边缘设备](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 web UI[更改密码](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果你更改密码，请务必通知所有远程访问用户，因此它们不存在登录出现的问题。

## <a name="protect-your-data"></a>保护数据

本部分介绍保护传输中和存储数据的数据框边缘安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>正传输的数据保护

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 旋转，然后[同步存储帐户密钥](data-box-edge-manage-shares.md#sync-storage-keys)定期以帮助防止未经授权的用户的存储帐户。

## <a name="manage-personal-information"></a>管理个人信息

数据框边缘服务收集以下方案中的个人信息：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看的用户可以访问或删除共享的列表，请按照中的步骤[管理数据框边缘上的共享](data-box-edge-manage-shares.md)。

有关详细信息，查看 Microsoft 隐私策略上[信任中心](https://www.microsoft.com/trustcenter)。

## <a name="next-steps"></a>后续步骤

[部署数据框边缘设备](data-box-edge-deploy-prep.md)
