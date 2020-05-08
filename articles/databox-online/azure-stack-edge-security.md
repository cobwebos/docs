---
title: Azure Stack 边缘安全性 |Microsoft Docs
description: 描述在本地和云中保护 Azure Stack Edge 设备、服务和数据的安全和隐私功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 3d567ec4d760be24fdbb79ff85bd6db0eb4a66c8
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82570065"
---
# <a name="azure-stack-edge-security-and-data-protection"></a>Azure Stack 边缘安全和数据保护

在采用新技术时，安全性是一个主要考虑因素，尤其是在该技术用于机密或专有数据时。 Azure Stack Edge 有助于确保只有授权的实体可以查看、修改或删除数据。

本文介绍可帮助保护每个解决方案组件和其中存储的数据的 Azure Stack 边缘安全功能。

Azure Stack 边缘由四个主要组件进行交互：

- **在 Azure 中托管的 Azure Stack Edge 服务**。 用于创建设备顺序、配置设备，然后跟踪订单完成的管理资源。
- **Azure Stack Edge 设备**。 发送给你的传输设备，你可以将本地数据导入到 Azure。
- **连接到设备的客户端/主机**。 基础结构中连接到 Azure Stack 边缘设备并包含需要保护的数据的客户端。
- **云存储**。 Azure 云平台中存储数据的位置。 此位置通常是链接到所创建的 Azure Stack Edge 资源的存储帐户。

## <a name="azure-stack-edge-service-protection"></a>Azure Stack Edge 服务保护

Azure Stack Edge 服务是 Azure 中托管的一项管理服务。 服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="azure-stack-edge-device-protection"></a>Azure Stack 边缘设备保护

Azure Stack 边缘设备是一个本地设备，它通过在本地处理数据，然后将数据发送到 Azure 来帮助转换数据。 你的设备：

- 需要激活密钥才能访问 Azure Stack Edge 服务。
- 由设备密码始终受保护。
- 是锁定的设备。 设备 BMC 和 BIOS 受密码保护。 BIOS 受受限用户访问保护。
- 已启用安全启动。
- 运行 Windows Defender 设备防护。 Device Guard 使你可以仅运行你在代码完整性策略中定义的受信任的应用程序。

### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

仅允许授权的 Azure Stack 边缘设备加入你在 Azure 订阅中创建的 Azure Stack Edge 服务。 若要授权设备，你需要使用激活密钥来激活设备与 Azure Stack Edge 服务。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](azure-stack-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码确保只有经过授权的用户才能访问数据。 Azure Stack 边缘设备在锁定状态中启动。

你可以：

- 通过浏览器连接到设备的本地 web UI，然后提供用于登录设备的密码。
- 通过 HTTP 远程连接到设备 PowerShell 接口。 默认情况下启用远程管理。 然后，你可以提供用于登录设备的设备密码。 有关详细信息，请参阅[远程连接到 Azure Stack Edge 设备](azure-stack-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 web UI[更改密码](azure-stack-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改了密码，请务必通知所有远程访问用户，以便在登录时不会出现问题。

## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中数据和存储数据的 Azure Stack 边缘安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 位加密用于保护本地数据。


### <a name="protect-data-in-flight"></a>保护传输中的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期轮换[存储帐户密钥](azure-stack-edge-manage-shares.md#sync-storage-keys)并对其进行同步，以帮助保护你的存储帐户不受未经授权的用户的保护。

## <a name="manage-personal-information"></a>管理个人信息

Azure Stack Edge 服务在以下情况下收集个人信息：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可访问或删除共享的用户的列表，请按照[管理 Azure Stack 边缘上的共享](azure-stack-edge-manage-shares.md)中的步骤操作。

有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署 Azure Stack Edge 设备](azure-stack-edge-deploy-prep.md)
