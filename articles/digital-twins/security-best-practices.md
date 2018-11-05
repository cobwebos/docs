---
title: 了解 Azure 数字孪生安全最佳做法 |Microsoft Docs
description: Azure 数字孪生安全最佳做法
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: adgera
ms.openlocfilehash: a17fe8ed47384ed248b339643be11269b8b9cdc0
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50092179"
---
# <a name="security-best-practices"></a>安全最佳做法

Azure 数字孪生安全性支持对 IoT 图中特定资源和操作的精确访问。 它通过精细的角色和权限管理（称为基于角色的访问控制）实现。

Azure 数字孪生还利用 Azure IoT 上存在的其他安全功能（包括 Azure Active Directory）。 为此，配置 Azure 数字孪生应用涉及使用当前建议的许多相同 [Azure IoT 安全实践](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)。

本文汇总了要遵循的关键最佳做法。

> [!IMPORTANT]
> 查看其他安全资源（包括设备供应商）以确保 IoT 空间的最大安全性。

## <a name="iot-security-best-practices"></a>IoT 安全最佳实践

安全地保护 IoT 设备的一些关键做法包括：

> [!div class="checklist"]
> * 以防篡改的方式保护连接到 IoT 空间的每个设备。
> * 限制 IoT 空间中每个设备、传感器和人员的角色。 如果遭到入侵，影响将降到最低。
> * 可能会用到设备 IP 地址筛选和端口限制。
> * 限制 I/O 和设备带宽，以提高性能。 通过阻止拒绝服务攻击，速率限制可增强安全性。
> * 使设备固件保持最新。

安全地保护 IoT 空间的一些关键做法包括：

> [!div class="checklist"]
> * 加密已保存、已存储或持久性数据。
> * 要求定期更改或刷新密码或密钥。
> * 按角色谨慎地限制访问和权限（参阅后文“基于角色的访问控制最佳做法”部分）。
> * 使用功能强大的加密。 这意味着需要较长密码、使用安全协议以及双重身份验证。

通过 Azure 分析管理对 IoT 资源的监视，以便查看常见操作范围之外的离群值、威胁或资源参数。

> [!NOTE]
> 有关事件处理和监视的详细信息，请参阅[事件 > 路由](./concepts-events-routing.md)上的文章。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 最佳做法

Azure 数字孪生使用 Azure Active Directory 对用户进行身份验证和保护应用程序。 Azure Active Directory 支持各种新型应用体系结构的身份验证，所有这些体系结构都基于 OAuth 2.0 或 OpenID Connect 等行业标准协议。 保护 Azure Active Directory 的 IoT 空间的几个关键做法包括：

> [!div class="checklist"]
> * 将 Azure Active Directory 应用机密和密钥存储在安全位置，例如 [Key Vault](https://azure.microsoft.com/services/key-vault/)。
> * 使用由受信任的[证书颁发机构](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-certificate-based-authentication-get-started)颁发的证书（而非应用机密）进行身份验证。
> * 为令牌限制 OAuth 2.0 的访问范围。
> * 验证令牌有效的时间长度以及它是否依然有效。
> * 设置令牌保持有效的适当时间长度。
> * 刷新过期的令牌。

## <a name="role-based-access-control-best-practices"></a>基于角色的访问控制最佳做法

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>后续步骤

要了解有关 Azure IoT 最佳做法的详细信息，请参阅[物联网安全最佳实践](https://docs.microsoft.com/azure/iot-fundamentals/iot-security-best-practices?context=azure/iot-hub/)。

要了解基于角色的访问控制的信息，请参阅[基于角色的访问控制](./security-role-based-access-control.md)。

有关身份验证的信息，请参阅[通过 API 进行身份验证](./security-authenticating-apis.md)。
