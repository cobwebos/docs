---
title: 排查身份验证和授权问题-Azure 事件中心
description: 本文提供有关排查 Azure 事件中心的身份验证和授权问题的信息。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1c053f60c877cdd26655948c37ab81a5e4d61cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85322414"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>排查身份验证和授权问题-Azure 事件中心
[排查连接问题](troubleshooting-guide.md)一文提供了有关排查 Azure 事件中心连接问题的提示。 本文提供有关排查 Azure 事件中心的身份验证和授权问题的提示和建议。 

## <a name="if-you-are-using-azure-active-directory"></a>如果使用 Azure Active Directory
如果使用 Azure Active Directory （Azure AD）通过 Azure 事件中心进行身份验证和授权，请确认访问事件中心的标识是正确的**资源范围**（使用者组、事件中心、命名空间、资源组或订阅）中适当的、**基于角色的访问控制（RBAC）角色**的成员。

### <a name="rbac-roles"></a>RBAC 角色
- 用于完全访问事件中心资源的[Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)。
- 用于发送访问的[Azure 事件中心数据发送者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)。
- 用于接收访问的[Azure 事件中心数据接收器](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)。

### <a name="resource-scopes"></a>资源范围
- **使用者组**：在此范围，角色分配仅应用到此实体。 目前，Azure 门户不支持在此级别向安全主体分配 RBAC 角色。 
- **事件中心**：角色分配将应用到事件中心实体及其下面的使用者组。
- **命名空间**：角色分配横跨命名空间下事件中心的整个拓扑，并延伸至与之关联的使用者组。
- **资源组**：角色分配将应用到资源组下的所有事件中心资源。
- **订阅**：角色分配将应用到订阅的所有资源组中的所有事件中心资源。

有关详细信息，请参阅以下文章：

- [对使用 Azure Active Directory 访问事件中心资源的应用程序进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>如果使用共享访问签名（SAS）
如果使用的是[SAS](authenticate-shared-access-signature.md)，请遵循以下步骤： 

- 确保你使用的 SAS 密钥是正确的。 如果没有，请使用正确的 SAS 密钥。
- 验证该注册表项是否具有适当的权限（发送、接收或管理）。 如果没有，请使用具有所需权限的密钥。 
- 检查密钥是否已过期。 建议你在过期之前续订 SAS。 如果客户端与事件中心服务节点之间存在时钟偏差，则身份验证令牌可能会在客户端意识到之前过期。 当前实现帐户时钟最多为5分钟，即客户端在令牌过期前的5分钟内续订。 因此，如果时钟偏差大于5分钟，则客户端可能会观察到间歇身份验证失败。
- 如果将 " **SAS 开始时间**" 设置为 "**现在**"，则可能会由于时钟偏差（不同计算机上当前时间的差异）而导致前几分钟出现间歇性故障。 将开始时间设置为至少15分钟，或者根本不设置。 同样的原则也适用于过期时间。 

有关详细信息，请参阅以下文章： 

- [使用共享访问签名（SAS）进行身份验证](authenticate-shared-access-signature.md)。 
- [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

* [解决连接问题](troubleshooting-guide.md)
