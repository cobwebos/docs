---
title: 了解安全性最佳做法-Azure 数字孪生 |Microsoft Docs
description: 了解 Azure 数字孪生和物联网的最佳安全方案。
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: ba10a6105972eddc8474841b234d2eeddc994f5d
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948920"
---
# <a name="security-best-practices"></a>安全最佳实践

Azure 数字孪生安全性支持对 IoT 图中特定资源和操作的精确访问。 它通过精细的角色和权限管理（称为[基于角色的访问控制](./security-role-based-access-control.md)）实现这一点。

Azure 数字孪生还使用 Azure IoT 中存在的其他安全功能，包括 Azure Active Directory (Azure AD)。 为此，配置和保护基于 Azure 数字孪生生成的应用程序涉及使用当前建议的多种相同的 [Azure IoT 安全做法](../iot-fundamentals/iot-security-best-practices.md)。

本文汇总了要遵循的关键最佳做法。

> [!IMPORTANT]
> 为确保 IoT 空间的最大安全性，请查看其他安全资源。 请确保包括你的设备供应商。

> [!TIP]
> 使用[适用于 iot 的 Azure 安全中心](https://docs.microsoft.com/azure/asc-for-iot/)来帮助检测 iot 安全威胁和漏洞。

## <a name="iot-security-best-practices"></a>IoT 安全最佳实践

安全地保护 IoT 设备的一些关键做法包括：

> [!div class="checklist"]
> * 以防篡改的方式保护连接到 IoT 空间的每个设备。
> * 限制 IoT 空间中每个设备、传感器和人员的角色。 如果遭到入侵，影响将降到最低。
> * 考虑设备 IP 地址筛选和端口限制的可能使用。
> * 限制 I/O 和设备带宽，以提高性能。 通过阻止拒绝服务攻击，速率限制可增强安全性。
> * 使设备固件保持最新。
> * 定期审核并查看设备、网络和网关安全最佳做法，因为它们会不断改进和发展。

安全地保护 IoT 空间的一些关键做法包括：

> [!div class="checklist"]
> * 加密已保存、已存储或持久性数据。
> * 要求定期更改或刷新密码或密钥。
> * 仔细按角色限制访问权限和权限。 请参阅以下[基于角色的访问控制最佳做法](#role-based-access-control-best-practices)部分。
> * 请考虑使用分段网络拓扑，使每个网络上的设备彼此隔离。
> * 使用功能强大的加密。 要求使用长密码，并使用安全的协议以及双重身份验证。

[监视](./how-to-configure-monitoring.md) IoT 资源来查看处于常见操作范围之外的离群值、威胁或资源参数。 使用 Azure Analytics 进行监视管理。

> [!NOTE]
> 有关事件处理和监视的详细信息，请参阅[使用 Azure 数字孪生路由事件和消息](./concepts-events-routing.md)。

## <a name="azure-active-directory-best-practices"></a>Azure Active Directory 最佳做法

Azure 数字孪生使用 Azure Active Directory 对用户进行身份验证和保护应用程序。 Azure Active Directory 支持各种新式体系结构的身份验证。 所有这些体系结构都以行业标准协议（例如 OAuth 2.0 或 OpenID Connect）。 保护 Azure Active Directory 的 IoT 空间的几个关键做法包括：

> [!div class="checklist"]
> * 将 Azure Active Directory 应用机密和密钥存储在安全位置，例如 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。
> * 使用由受信任的[证书颁发机构](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)颁发的证书（而非应用机密）进行身份验证。
> * 为令牌限制 OAuth 2.0 的访问范围。
> * 验证令牌有效的时间长度以及它是否依然有效。
> * 设置令牌保持有效的适当时间长度。
> * 刷新过期的令牌。

## <a name="role-based-access-control-best-practices"></a>基于角色的访问控制最佳做法

[!INCLUDE [digital-twins-rbac-best-practices](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="next-steps"></a>后续步骤

* 要了解有关 Azure IoT 最佳做法的详细信息，请参阅[物联网安全最佳实践](../iot-fundamentals/iot-security-best-practices.md)。

* 若要了解基于角色的访问控制，请阅读[基于角色的访问控制](./security-role-based-access-control.md)。

* 若要了解身份验证，请阅读[通过 API 进行身份验证](./security-authenticating-apis.md)。