---
title: Azure 数据框网关安全 |Microsoft Docs
description: 介绍保护 Azure 数据框网关虚拟设备、 服务和数据，在本地安全和隐私功能和在云中。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 230d1a28ba15a8736e46c02cb08217a28fc18599
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "64695189"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 数据框网关安全和数据保护

安全性是主要考虑因素时要采用新技术，尤其是当该技术使用包含机密或专有数据。 Azure 数据框网关可帮助您确保只有经过授权的实体可以查看、 修改或删除你的数据。

本文介绍 Azure 数据框网关安全功能，可帮助保护的每个解决方案组件和在其中存储的数据。

数据框网关解决方案由四个彼此交互的主要组件组成：

- **在 Azure 中托管的数据框网关服务**。 管理资源的使用来创建设备顺序，配置设备，，然后完成到跟踪的顺序。
- **数据框网关设备**。 系统提供的虚拟机监控程序中预配虚拟设备。 此虚拟设备用于你的本地数据导入到 Azure。
- **连接到设备的客户端/主机**。 中的客户端基础结构连接到数据框网关设备，并且包含需要保护的数据。
- **云存储**。 Azure 云平台中存储数据的位置。 此位置通常是存储帐户链接到你创建的数据框网关资源。


## <a name="data-box-gateway-service-protection"></a>数据框网关服务保护

数据框网关服务是托管在 Azure 中管理服务。 该服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>数据框网关设备保护

数据框网关设备是在本地系统提供的虚拟机监控程序中预配的虚拟设备。 设备可帮助将数据发送到 Azure。 你的设备：

- 需要激活密钥来访问数据框边缘/数据框网关服务。
- 是始终受到保护的设备密码。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>保护设备通过激活密钥

允许仅授权的数据框网关设备加入 Azure 订阅中创建的数据框网关服务。 若要授权某个设备，需要使用激活密钥来激活设备的数据框网关服务。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>保护通过密码的设备

密码确保只有授权的用户可以访问你的数据。 数据框网关设备中启动，锁定状态。

可以：

- 连接到本地 web UI 中通过浏览器设备，然后提供密码以登录到设备。
- 远程连接到设备的 PowerShell 接口通过 HTTP。 默认情况下，远程管理启用。 然后可以提供用于登录到设备的设备密码。 有关详细信息，请参阅[远程连接到数据框网关设备](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 web UI[更改密码](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果你更改密码，请务必通知所有远程访问用户，使其不具有登录出现的问题。


## <a name="protect-your-data"></a>保护数据

本部分介绍保护传输中和存储数据的数据框网关安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>正传输的数据保护

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 旋转，然后[同步存储帐户密钥](data-box-gateway-manage-shares.md#sync-storage-keys)定期以帮助防止未经授权的用户的存储帐户。

## <a name="manage-personal-information"></a>管理个人信息

数据框网关服务会收集以下方案中的个人信息：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看的用户可以访问或删除共享的列表，请按照中的步骤[管理数据框网关上的共享](data-box-gateway-manage-shares.md)。

有关详细信息，查看 Microsoft 隐私策略上[信任中心](https://www.microsoft.com/trustcenter)。

## <a name="next-steps"></a>后续步骤

[部署数据框网关设备](data-box-gateway-deploy-prep.md)
