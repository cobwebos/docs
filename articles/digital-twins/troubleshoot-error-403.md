---
title: 'Azure 数字孪生请求失败，状态为： 403 (禁用) '
description: "\"服务请求失败\" 的原因和解决方法。 状态： 403 (禁用 Azure 数字孪生上的) \"。"
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: bc4fbbc265bef00be27c890c3f090a49591dc415
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90562734"
---
# <a name="service-request-failed-status-403-forbidden"></a>服务请求失败。 状态： 403 (禁止访问) 

本文介绍从服务请求到 Azure 数字孪生收到403错误的原因和解决步骤。 

## <a name="symptoms"></a>症状

需要身份验证的多个服务请求类型可能发生此错误。 其结果是 API 请求失败，并返回错误状态 `403 (Forbidden)` 。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 #1

最常见的情况是，此错误表明，基于角色的访问控制对服务 (RBAC) 权限的设置不正确。 对于 Azure 数字孪生实例，许多操作都需要你要**尝试管理的实例上**的*Azure 数字孪生所有者 (预览) *角色。 

### <a name="cause-2"></a>原因 #2

如果你使用客户端应用来与 Azure 数字孪生通信，则可能会发生此错误，因为 Azure AD) 应用注册的 [Azure Active Directory (](../active-directory/fundamentals/active-directory-whatis.md) 没有为 Azure 数字孪生服务设置权限。

应用注册需要为 Azure 数字孪生 Api 配置访问权限。 然后，在客户端应用对应用注册进行身份验证时，会向其授予应用注册已配置的权限。

## <a name="solutions"></a>解决方案

### <a name="solution-1"></a>解决方案 #1

第一种解决方案是验证 Azure 用户在尝试管理的实例上是否有 _**Azure 数字孪生所有者 (预览版) **_ 角色。 如果没有此角色，请对其进行设置。

请注意，此角色不同于 .。。
* 整个 Azure 订阅的 *所有者* 角色。 *Azure 数字孪生所有者 (预览版) * 是 Azure 数字孪生中的一个角色，其作用域为此单个 Azure 数字孪生实例。
* Azure 数字孪生中的 *所有者* 角色。 它们是两个不同的 Azure 数字孪生管理角色， *Azure 数字孪生所有者 (预览) * 是在预览期间用于管理的角色。

#### <a name="check-current-setup"></a>检查当前设置

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>解决问题 

如果你没有此角色分配，则在 **azure 订阅** 中具有所有者角色的用户应运行以下命令，为 azure 用户提供 Azure 数字 *孪生所有者 (预览 * **azure 数字孪生实例**上的) 角色。 

如果你是订阅的所有者，则可以自己运行此命令。 否则，请联系所有者以代表你运行此命令。

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Owner (Preview)"
```

有关此角色要求和分配过程的更多详细信息，请参阅如何：设置 [*用户的用户访问权限* 部分](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) 中的 *操作方法：设置实例和身份验证 (CLI 或门户) *。

如果此角色分配已存在并且仍遇到403问题，请继续下一解决方案。

### <a name="solution-2"></a>解决方案 #2

第二个解决方案是验证 Azure AD 应用注册是否具有为 Azure 数字孪生服务配置的权限。 如果未配置此设置，请对其进行设置。

#### <a name="check-current-setup"></a>检查当前设置

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

首先，验证是否已正确设置注册上的 Azure 数字孪生权限设置。 为此，请从菜单栏中选择 " *清单* "，以查看应用注册的清单代码。 滚动到代码窗口的底部，在下查找这些字段 `requiredResourceAccess` 。 值应与以下屏幕截图中的值匹配：

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

#### <a name="fix-issues"></a>解决问题

如果任何此项的显示方式与所述有所不同，请按照*如何：设置实例和身份验证 (CLI 或门户) *中的 "设置[*客户端应用程序的访问权限*" 部分](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications)中的说明设置应用注册的说明进行操作。

## <a name="next-steps"></a>后续步骤

阅读创建和验证新的 Azure 数字孪生实例的设置步骤：
* [*操作说明：设置实例和身份验证 (CLI) *](how-to-set-up-instance-cli.md)

阅读有关 Azure 数字孪生的安全和权限的详细信息：
* [*概念： Azure 数字孪生解决方案的安全性*](concepts-security.md)