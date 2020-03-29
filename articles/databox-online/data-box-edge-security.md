---
title: Azure 数据框边缘安全性 |微软文档
description: 描述保护 Azure 数据框边缘设备、服务和本地和云中数据的安全和隐私功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 08/21/2019
ms.author: alkohli
ms.openlocfilehash: 375576dd4a7897c48474fd2af00a99084292d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69970887"
---
# <a name="azure-data-box-edge-security-and-data-protection"></a>Azure Data Box Edge 安全性和数据保护

当您采用新技术时，安全性是一个主要问题，尤其是在该技术与机密或专有数据一起使用时。 Azure 数据框边缘可帮助您确保只有经过授权的实体才能查看、修改或删除数据。

本文介绍了数据框边缘安全功能，可帮助保护每个解决方案组件及其中存储的数据。

Azure 数据框边缘由四个主要组件组成，这些组件彼此交互：

- **数据框边缘服务，托管在 Azure 中**。 用于创建设备订单、配置设备，然后跟踪订单完成的管理资源。
- **数据框边缘设备**。 已发运给您的传输设备，以便您可以将本地数据导入 Azure。
- **连接到设备的客户端/主机**。 基础结构中连接到数据框边缘设备的客户端，其中包含需要保护的数据。
- **云存储**。 存储数据的 Azure 云平台中的位置。 此位置通常是链接到您创建的数据框边缘资源的存储帐户。

## <a name="data-box-edge-service-protection"></a>数据框边缘服务保护

数据框边缘服务是在 Azure 中托管的管理服务。 该服务用于配置和管理设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-service-protection.md)]

## <a name="data-box-edge-device-protection"></a>数据框边缘设备保护

数据框边缘设备是一种本地设备，它通过在本地处理数据，然后将其发送到 Azure，帮助转换数据。 您的设备：

- 需要激活密钥才能访问数据框边缘服务。
- 随时受设备密码保护。
- 是锁定的设备。 设备 BMC 和 BIOS 受密码保护。 BIOS 受受限用户访问的保护。
- 已启用安全引导。
- 运行 Windows 防御器设备保护。 Device Guard 允许您仅运行在代码完整性策略中定义的受信任应用程序。

### <a name="protect-the-device-via-activation-key"></a>通过激活密钥保护设备

只有经过授权的数据框边缘设备才能加入您在 Azure 订阅中创建的数据框边缘服务。 要授权设备，您需要使用激活密钥使用数据盒边缘服务激活设备。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-activation-key.md)]

有关详细信息，请参阅[获取激活密钥](data-box-edge-deploy-prep.md#get-the-activation-key)。

### <a name="protect-the-device-via-password"></a>通过密码保护设备

密码确保只有经过授权的用户才能访问您的数据。 数据框边缘设备处于锁定状态启动。

可以：

- 通过浏览器连接到设备的本地 Web UI，然后提供密码以登录到设备。
- 通过 HTTP 远程连接到设备 PowerShell 接口。 默认情况下，远程管理处于打开状态。 然后，您可以提供设备密码以登录到设备。 有关详细信息，请参阅[远程连接到数据盒边缘设备](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-password-best-practices.md)]
- 使用本地 Web UI[更改密码](data-box-edge-manage-access-power-connectivity-mode.md#manage-device-access)。 如果更改密码，请确保通知所有远程访问用户，以便他们在登录时没有问题。

## <a name="protect-your-data"></a>保护数据

本节介绍保护传输中和存储数据的数据框边缘安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256 位加密用于保护本地数据。


### <a name="protect-data-in-flight"></a>保护飞行中的数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-data-flight.md)]

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-protect-data-storage-accounts.md)]
- 定期旋转并[同步存储帐户密钥](data-box-edge-manage-shares.md#sync-storage-keys)，以帮助保护您的存储帐户免受未经授权的用户的攻击。

## <a name="manage-personal-information"></a>管理个人信息

数据框边缘服务在以下情况下收集个人信息：

[!INCLUDE [data-box-edge-gateway-data-rest](../../includes/data-box-edge-gateway-manage-personal-data.md)]

要查看可以访问或删除共享的用户列表，请按照["数据框边缘"上的"管理共享"](data-box-edge-manage-shares.md)中的步骤操作。

有关详细信息，请查看[信任中心的](https://www.microsoft.com/trustcenter)Microsoft 隐私政策。

## <a name="next-steps"></a>后续步骤

[部署数据框边缘设备](data-box-edge-deploy-prep.md)
