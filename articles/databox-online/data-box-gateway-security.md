---
title: Azure 数据盒网关安全性 |微软文档
description: 描述保护 Azure 数据盒网关虚拟设备、服务和数据、本地和云中的安全和隐私功能。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 89f981fcda8f40daff49ebdf796b896d90ce1754
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69900597"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure 数据盒网关安全和数据保护

当您采用新技术时，安全性是一个主要问题，尤其是在该技术与机密或专有数据一起使用时。 Azure 数据框网关可帮助您确保只有经过授权的实体才能查看、修改或删除数据。

本文介绍了 Azure 数据盒网关安全功能，可帮助保护每个解决方案组件及其中存储的数据。

数据盒网关解决方案由四个主要组件组成，这些组件相互交互：

- **数据框网关服务，托管在 Azure 中**。 用于创建设备订单、配置设备，然后跟踪订单完成的管理资源。
- **数据盒网关设备**。 在提供的系统的虚拟机管理程序中预配的虚拟设备。 此虚拟设备用于将本地数据导入 Azure。
- **连接到设备的客户端/主机**。 基础结构中连接到数据盒网关设备的客户端，其中包含需要保护的数据。
- **云存储**。 存储数据的 Azure 云平台中的位置。 此位置通常是链接到您创建的数据框网关资源的存储帐户。


## <a name="data-box-gateway-service-protection"></a>数据盒网关服务保护

数据盒网关服务是在 Azure 中托管的管理服务。 该服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-service-protection](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>数据盒网关设备保护

数据盒网关设备是一种虚拟设备，在您提供的本地系统的虚拟机管理程序中预配。 该设备有助于将数据发送到 Azure。 您的设备：

- 需要激活密钥才能访问数据框边缘/数据盒网关服务。
- 随时受设备密码保护。
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

只有经过授权的数据盒网关设备才能加入您在 Azure 订阅中创建的数据盒网关服务。 要授权设备，您需要使用激活密钥使用数据盒网关服务激活设备。

[!INCLUDE [data-box-edge-gateway-activation-key](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](data-box-gateway-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码确保只有经过授权的用户才能访问您的数据。 数据框网关设备处于锁定状态启动。

可以：

- 通过浏览器连接到设备的本地 Web UI，然后提供密码以登录到设备。
- 通过 HTTP 远程连接到设备的 PowerShell 接口。 默认情况下，远程管理处于打开状态。 然后，您可以提供设备密码以登录到设备。 有关详细信息，请参阅[远程连接到数据盒网关设备](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-password-best-practices](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 Web UI[更改密码](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改密码，请确保通知所有远程访问用户，以便他们在登录时没有问题。


## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中和存储数据的数据盒网关安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>保护飞行中的数据

[!INCLUDE [data-box-edge-gateway-data-flight](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-storage-accounts](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期旋转并[同步存储帐户密钥](data-box-gateway-manage-shares.md#sync-storage-keys)，以帮助保护您的存储帐户免受未经授权的用户的攻击。

## <a name="manage-personal-information"></a>管理个人信息

数据盒网关服务在以下情况下收集个人信息：

[!INCLUDE [data-box-edge-gateway-manage-personal-data](../../includes/data-box-edge-gateway-manage-personal-data.md)]

要查看可以访问或删除共享的用户列表，请按照["数据框网关"上的管理共享](data-box-gateway-manage-shares.md)中的步骤操作。

有关详细信息，请查看[信任中心的](https://www.microsoft.com/trustcenter)Microsoft 隐私政策。

## <a name="next-steps"></a>后续步骤

[部署数据盒网关设备](data-box-gateway-deploy-prep.md)
