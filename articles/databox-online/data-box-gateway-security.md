---
title: Azure Data Box Gateway 安全性 |Microsoft Docs
description: 描述在本地和云中保护 Azure Data Box Gateway 虚拟设备、服务和数据的安全和隐私功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 2476cf360909374f711564fb5fad5c9e0706083d
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562468"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway 安全和数据保护

在采用新技术时，安全性是一个主要考虑因素，尤其是在该技术用于机密或专有数据时。 Azure Data Box Gateway 可帮助你确保只有经过授权的实体可以查看、修改或删除你的数据。

本文介绍了 Azure Data Box Gateway 的安全功能，这些功能可帮助保护每个解决方案组件和其中存储的数据。

Data Box Gateway 解决方案包含四个主要组件互相交互：

- **Data Box Gateway 服务，托管在 Azure 中**。 用于创建设备顺序、配置设备，然后跟踪订单完成的管理资源。
- **Data Box Gateway 设备**。 在你提供的系统的虚拟机监控程序中预配的虚拟设备。 此虚拟设备用于将本地数据导入到 Azure。
- **连接到设备的客户端/主机**。 基础结构中连接到 Data Box Gateway 设备并包含需要保护的数据的客户端。
- **云存储**。 Azure 云平台中存储数据的位置。 此位置通常是链接到所创建的 Data Box Gateway 资源的存储帐户。


## <a name="data-box-gateway-service-protection"></a>Data Box Gateway 服务保护

Data Box Gateway 服务是托管在 Azure 中的一项管理服务。 服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Data Box Gateway 设备保护

Data Box Gateway 设备是在你提供的本地系统的虚拟机监控程序中预配的虚拟设备。 设备可帮助将数据发送到 Azure。 你的设备：

- 需要激活密钥才能访问 Azure Stack Edge/Data Box Gateway 服务。
- 由设备密码始终受保护。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

仅允许授权的 Data Box Gateway 设备加入你在 Azure 订阅中创建的 Data Box Gateway 服务。 若要授权设备，你需要使用激活密钥来通过 Data Box Gateway 服务激活设备。

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码确保只有经过授权的用户才能访问数据。 Data Box Gateway 设备启动时处于锁定状态。

你可以：

- 通过浏览器连接到设备的本地 web UI，然后提供用于登录设备的密码。
- 通过 HTTP 远程连接到设备的 PowerShell 接口。 默认情况下启用远程管理。 然后，你可以提供用于登录设备的设备密码。 有关详细信息，请参阅[远程连接到 Data Box Gateway 设备](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 web UI[更改密码](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改了密码，请务必通知所有远程访问用户，使其在登录时不会出现问题。


## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中数据和存储数据的 Data Box Gateway 安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保护传输中的数据

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期轮换[存储帐户密钥](data-box-gateway-manage-shares.md#sync-storage-keys)并对其进行同步，以帮助保护你的存储帐户不受未经授权的用户的保护。

## <a name="manage-personal-information"></a>管理个人信息

Data Box Gateway 服务在以下情况下收集个人信息：

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

若要查看可访问或删除共享的用户的列表，请按照[管理 Data Box Gateway 上的共享](data-box-gateway-manage-shares.md)中的步骤操作。

有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署 Data Box Gateway 设备](data-box-gateway-deploy-prep.md)
