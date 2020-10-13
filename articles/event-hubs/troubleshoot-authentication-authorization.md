---
title: 排查身份验证和授权问题 - Azure 事件中心
description: 本文介绍了如何排查 Azure 事件中心的身份验证和授权问题。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cd5f48dfb146a027f0b95b4ddea3dc054a315c6e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91566221"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>排查身份验证和授权问题 - Azure 事件中心
[排查连接问题](troubleshooting-guide.md)一文提供了有关排查 Azure 事件中心连接问题的技巧。 本文提供的技巧和建议适用于排查 Azure 事件中心的身份验证和授权问题。 

## <a name="if-you-are-using-azure-active-directory"></a>如果使用 Azure Active Directory
如果使用 Azure Active Directory (Azure AD) 通过 Azure 事件中心进行身份验证和授权，请确认访问事件中心的标识是合适的“资源范围”（使用者组、事件中心、命名空间、资源组或订阅）中合适的“Azure 角色”的成员 。

### <a name="azure-roles"></a>Azure 角色
- [Azure 事件中心数据所有者](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner)，具有对事件中心资源的完全访问权限。
- [Azure 事件中心数据发送方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver)，具有发送访问权限。
- [Azure 事件中心数据接收方](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender)，具有接收访问权限。

有关架构注册表内置角色，请参阅 [架构注册表角色](schema-registry-overview.md#role-based-access-control)。

### <a name="resource-scopes"></a>资源范围
- **使用者组**：在此范围，角色分配仅应用到此实体。 目前，Azure 门户不支持在此级别向安全主体分配 Azure 角色。 
- **事件中心**：角色分配将应用到事件中心实体及其下面的使用者组。
- **命名空间**：角色分配横跨命名空间下事件中心的整个拓扑，并延伸至与之关联的使用者组。
- **资源组**：角色分配将应用到资源组下的所有事件中心资源。
- **订阅**：角色分配将应用到订阅的所有资源组中的所有事件中心资源。

有关详细信息，请参阅以下文章：

- [使用 Azure Active Directory 对访问事件中心资源的应用程序进行身份验证](authenticate-application.md)
- [使用 Azure Active Directory 授权访问事件中心资源](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>如果使用共享访问签名 (SAS)
如果使用 [SAS](authenticate-shared-access-signature.md)，请执行以下步骤： 

- 确保使用的 SAS 密钥是正确的。 否则，请使用正确的 SAS 密钥。
- 确认该密钥具有适当的权限（发送、接收或管理权限）。 否则，请使用具有所需权限的密钥。 
- 检查密钥是否已过期。 建议你在过期之前续订 SAS。 如果客户端与事件中心服务节点之间存在时钟偏差，则身份验证令牌可能会在客户端认出它之前过期。 当前实现考虑到的时钟偏差长达 5 分钟，也就是说，客户端在令牌过期前 5 分钟内续订令牌。 因此，如果时钟偏差大于 5 分钟，则客户端可能会出现间歇性的身份验证失败。
- 如果将“SAS 开始时间”设置为“立即”，由于时钟偏差（不同计算机上的当前时间存在差异），可能会在前几分钟出现间歇性故障。 请将开始时间至少设置为 15 分钟前，或者根本不设置它。 同样的原则也适用于过期时间。 

有关详细信息，请参阅以下文章： 

- [使用共享访问签名 (SAS) 进行身份验证](authenticate-shared-access-signature.md)。 
- [使用共享访问签名授权访问事件中心资源](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>后续步骤

请参阅以下文章：

* [排查连接问题](troubleshooting-guide.md)
