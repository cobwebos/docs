---
title: Azure 数据框边缘安全 |Microsoft Docs
description: 介绍保护 Azure 数据框边缘设备、 服务和数据在本地和云中的安全和隐私功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 5316ddf9d456731f2789241434926366f732993a
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682094"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure 数据框边缘安全和数据保护

安全性是主要考虑因素时采用新技术，尤其是当该技术使用包含机密或专有数据。 Microsoft Azure 数据框边缘解决方案可帮助确保只有经过授权的实体可以查看、 修改或删除你的数据。

这篇文章介绍帮助保护的每个解决方案组件和数据存储在其上的数据框边缘安全功能。

Azure 数据框边缘解决方案包含四个彼此交互的主要组件：

- **在 Azure 中托管的数据框边缘服务**– 管理资源的使用来创建的设备顺序，配置设备，然后完成到跟踪的顺序。
- **数据框边缘设备**– 已寄送给你将在本地数据导入到 Azure 的传输设备。
- **连接到设备的客户端/主机**– 基础结构中，连接到数据框边缘设备，并且包含需要保护的数据的客户端。
- **云存储** – Azure 云中存储数据的位置。 此位置通常是存储帐户链接到你创建的数据框边缘资源。

## <a name="data-box-edge-service-protection"></a>数据框边缘服务保护

数据框边缘服务是托管在 Microsoft Azure 中的管理服务。 该服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>数据框边缘设备保护

数据框边缘设备是在本地设备，可帮助通过本地处理，然后将它发送到 Azure 来转换数据。 你的设备：

- 需要激活密钥来访问数据框边缘服务。
- 是始终受到保护的设备密码。
- 是锁定的设备。 设备 BMC 和 BIOS 是有关 BIOS 的有限用户访问受密码保护。
- 已启用安全启动。
- 在运行 Windows Defender Device Guard。 Device Guard，可运行仅受信任的应用程序在你的代码完整性策略中定义。

### <a name="protect-the-device-via-activation-key"></a>保护设备通过激活密钥

仅授权的数据框边缘设备允许加入你的 Azure 订阅中创建的数据框边缘服务。 若要授权某个设备，必须使用激活密钥来激活设备的数据框边缘服务。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请转到[获取激活密钥](data-box-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>保护通过密码的设备

密码确保你的数据仅限于授权用户可访问。 数据框边缘设备中启动，锁定状态。

可以：

- 连接到本地 web UI 中通过浏览器设备，然后提供密码以登录到该设备。
- 远程连接到设备的 PowerShell 接口通过 HTTP。 默认情况下，远程管理启用。 然后可以提供用于登录到该设备的设备密码。 有关详细信息，请转到[远程连接到数据框边缘设备](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 web UI[更改密码](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果你更改密码，请务必通知所有远程访问用户，以便它们不会遇到登录失败。

## <a name="protect-the-data"></a>保护数据

本部分介绍保护传输中的数据和存储的数据的数据框边缘安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>正传输的数据保护

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 旋转，然后[同步存储帐户密钥](data-box-edge-manage-shares.md#sync-storage-keys)定期以帮助确保未经授权的用户没有访问你的存储帐户。

## <a name="manage-personal-information"></a>管理个人信息

数据框边缘服务收集个人信息在以下情况下密钥：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看的用户可以访问或删除共享的列表，请按照中的步骤[管理数据框边缘上的共享](data-box-edge-manage-shares.md)。

有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署数据框边缘设备](data-box-edge-deploy-prep.md)。
