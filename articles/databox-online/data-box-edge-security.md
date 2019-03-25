---
title: 数据框边缘安全 |Microsoft Docs
description: 介绍保护数据框边缘设备、 服务和数据在本地和云中的安全和隐私功能。
services: Data Box Edge
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: 43de22f7e56178559df4fc45980d064962580d2b
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403385"
---
# <a name="data-box-edge-security-and-data-protection"></a>数据框边缘安全和数据保护

安全性是主要考虑因素时采用新技术，尤其是当该技术使用包含机密或专有数据。 Microsoft Azure 数据框边缘解决方案可帮助确保只有经过授权的实体可以查看、 修改或删除你的数据。

这篇文章介绍帮助保护的每个解决方案组件和数据存储在其上的数据框边缘安全功能。

Azure 数据框边缘解决方案包含四个彼此交互的主要组件：

- **数据框边缘 / 数据框网关服务在 Azure 中托管**– 管理资源的使用来创建的设备顺序，配置设备，然后完成到跟踪的顺序。
- **数据框边缘设备**– 已寄送给你将在本地数据导入到 Azure 的传输设备。
- **连接到设备的客户端/主机**– 基础结构中，连接到数据框边缘设备，并且包含需要保护的数据的客户端。
- **云存储** – Azure 云中存储数据的位置。 此位置通常是存储帐户链接到你创建的数据框边缘资源。


## <a name="data-box-edgedata-box-gateway-service-protection"></a>数据框边缘/数据框网关服务保护

数据框边缘/数据框网关服务是托管在 Microsoft Azure 中的管理服务。 该服务用于配置和管理设备。

- 访问数据框边缘/数据框网关服务需要你的组织拥有企业协议 (EA) 或云解决方案提供商 (CSP) 订阅。 有关详细信息，请转到[注册 Azure 订阅](https://azure.microsoft.com/resources/videos/sign-up-for-microsoft-azure/)！
- 在 Azure 中托管你的管理服务，因为它受 Azure 安全功能。 有关 Microsoft Azure 提供的安全功能的详细信息，请转到 [Microsoft Azure 信任中心](https://azure.microsoft.com/support/trust-center/security/)。

## <a name="data-box-edge-device-protection"></a>数据框边缘设备保护

数据框边缘设备是在本地设备，可帮助通过本地处理，然后将它发送到 Azure 来转换数据。 你的设备：

- 需要激活密钥来访问数据框边缘/数据框网关服务。
- 是始终受到保护的设备密码。
- 是锁定的设备。 设备 BMC 和 BIOS 是有关 BIOS 的有限用户访问受密码保护。
- 已启用安全启动。
- 在运行 Windows Defender Device Guard。 Device Guard，可运行仅受信任的应用程序在你的代码完整性策略中定义。 

### <a name="protect-the-device-via-activation-key"></a>保护设备通过激活密钥

仅授权的数据框边缘设备允许加入你的 Azure 订阅中创建的数据框边缘/数据框网关服务。 若要授权某个设备，必须使用激活密钥来激活设备的数据框边缘/数据框网关服务。 有关详细信息，请转到[获取激活密钥](data-box-edge-deploy-prep.md#get-the-activation-key)。

使用激活密钥：

- 是一个 Azure Active Directory (AAD) 基于的身份验证密钥。
- 在三天后过期。
- 在设备激活后不会使用。
 
激活设备后，它使用令牌与 Microsoft Azure 进行通信。

### <a name="protect-the-device-via-password"></a>保护通过密码的设备

密码确保你的数据仅限于授权用户可访问。 数据框边缘和数据框网关设备中启动，锁定状态。

可以：

- 连接到本地 web UI 中通过浏览器设备，然后提供密码以登录到该设备。
- 远程连接到设备的 PowerShell 接口通过 HTTP。 默认情况下，远程管理启用。 然后可以提供用于登录到该设备的设备密码。 有关详细信息，请转到[远程连接到数据框边缘设备](data-box-edge-connect-powershell-interface.md#connect-to-the-powershell-interface)。

牢记以下最佳实践：

- 数据框边缘服务无法检索现有密码： 它可以仅通过 Azure 门户重置密码。 我们建议将所有密码都存储在安全位置，以便在忘记密码时不必重置密码。 如果重置密码，请务必通知所有用户，然后再重置。
- 使用本地 web UI[更改密码](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access)。 如果你更改密码，请务必通知所有远程访问用户，以便它们不会遇到登录失败。
- 可以通过 HTTP 远程访问你的设备的 Windows PowerShell 界面。 作为安全性最佳实践，应仅在受信任的网络上使用 HTTP。
- 确保设备密码具有很高强度并受到严密保护。 请按照[密码最佳实践](https://docs.microsoft.com/azure/security/azure-security-identity-management-best-practices#enable-password-management)。

## <a name="protect-the-data"></a>保护数据

本部分介绍保护传输中的数据和存储的数据的数据框边缘安全功能。

### <a name="protect-data-at-rest"></a>保护静止的数据

对数据的静态：

- 有关数据的静态数据，数据框边缘使用 BitLocker XTS AES-256 加密来保护本地数据。
- 对于驻留在共享中的数据，对共享的访问权限受到限制。

    - 对于访问共享数据的 SMB 客户端，它们需要与共享相关联的用户凭据。 在共享创建时定义这些凭据。
    - 对于访问共享的 NFS 客户端，需要在创建共享时添加客户端的 IP 地址。


### <a name="protect-data-in-flight"></a>正传输的数据保护

数据-中-网络：

- 将设备与 Azure 之间的数据，使用标准的 TLS 1.2。 为 TLS 1.1 及更早版本没有回退。 如果不支持 TLS 1.2，则将阻止代理通信。 TLS 1.2，还需要为门户和 SDK 管理操作。
- 当客户端通过本地 web UI 在浏览器中访问你的设备时，标准 TLS 1.2 用作默认安全协议。

    - 最佳做法是在浏览器配置为使用 TLS 1.2。
    - 如果浏览器不支持 TLS 1.2，可以使用 TLS 1.1 或 TLS 1.0。
- 若要保护的数据从数据服务器复制时，我们建议你使用 SMB 3.0 加密。

### <a name="protect-data-via-storage-accounts"></a>通过存储帐户保护数据

你的设备都与存储帐户用作 Azure 中的数据目标相关联。 对存储帐户的访问控制按订阅和两个 512 位存储访问密钥与该存储帐户相关联。

当数据框边缘设备访问存储帐户时，其中一个密钥用于身份验证。 另外一个密钥保留，让你能够定期轮换密钥。

出于安全原因，很多数据中心要求密钥轮换。 我们建议遵循密钥轮换的以下最佳实践：

- 存储帐户密钥类似于存储帐户的根密码。 请小心保护你的帐户密钥。 不将密码分发给其他用户，硬编码，或将其保存任意位置以其他人可以访问的纯文本形式。
- [重新生成帐户密钥](../storage/common/storage-account-manage.md#regenerate-access-keys)使用 Azure 门户中，如果你认为可能已遭破坏。
- 旋转，然后[同步存储帐户密钥](data-box-gateway-manage-shares.md#sync-storage-keys)定期以帮助确保未经授权的用户没有访问你的存储帐户。
- Azure 管理员应该定期使用 Azure 门户的“存储”部分来直接访问存储帐户，从而更改或重新生成主密钥或辅助密钥。


## <a name="manage-personal-information"></a>管理个人信息

数据框边缘 / 数据框网关服务收集的个人信息在以下情况下密钥：

- **订单详细信息** – 创建订单后，用户的寄送地址、电子邮件和联系信息将存储在 Azure 门户中。 保存的信息包括：
  - 联系人姓名
  - 电话号码
  - 电子邮件
  - 街道地址
  - 城市
  - 邮政编码
  - 状态
  - 国家/地区/省/区域
  - 运输跟踪号

    加密和存储服务中的订单详细信息。 该服务将保留该信息，直到被显式删除的资源或顺序。 此外，删除资源和相应的订单阻止该设备附带设备返回到 Microsoft 之前的时间。

- **寄送地址**– Data Box 服务订单后，到 UPS 等第三方运营商提供的寄送地址。

- **共享用户**-在设备上的用户也可以访问驻留在共享上的数据。 会显示可访问共享数据的用户列表，并可供查看。 删除共享时，也会删除此列表。 若要查看的用户可以访问或删除共享的列表，请按照中的步骤[管理数据框边缘上的共享](data-box-gateway-manage-shares.md)。

有关详细信息，请在[信任中心](https://www.microsoft.com/trustcenter)查看 Microsoft 隐私策略。

## <a name="next-steps"></a>后续步骤

[部署数据框边缘设备](data-box-edge-deploy-prep.md)。

