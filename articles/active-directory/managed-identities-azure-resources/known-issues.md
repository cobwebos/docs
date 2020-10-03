---
title: 托管标识的 FAQ 和已知问题 - Azure AD
description: Azure 资源托管标识的已知问题。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 08/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: cf9f484a3f9285d1be06443b39bd50ec73ccf632
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665287"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Azure 资源托管标识的 FAQ 和已知问题

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>常见问题解答 (FAQ)

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>如何查找具有托管标识的资源？

可通过使用以下 Azure CLI 命令来查找具有系统分配的托管标识的资源列表： 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>托管标识是否具有后备应用对象？

不能。 托管标识和 Azure AD 应用注册在目录中是不同的。 

应用注册有两个组件：应用程序对象和服务主体对象。 Azure 资源的托管标识仅包含以下组件之一：一个服务主体对象。 

托管标识在目录中没有通常用于授予 MS Graph 应用权限的应用程序对象。 需要直接将托管标识的 MS Graph 权限授予服务主体。  

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 资源托管标识可以用于 Azure 云服务吗？

否，Azure 云服务中没有支持 Azure 资源托管标识的计划。

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>与托管标识关联的凭据是什么？ 它的有效时间有多长？其轮换频率有多高？

> [!NOTE]
> 托管标识的身份验证方式属于在内部具体实施的措施，可能会在不通知的情况下更改。

托管标识使用基于证书的身份验证。 每个托管标识的凭据的有效期为 90 天，在 45 天后轮换。

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>什么是 Azure 资源托管标识的安全边界？

标识的安全边界是标识所附加到的资源。 例如，启用了 Azure 资源托管标识的虚拟机的安全边界是虚拟机。 在该 VM 上运行的任何代码都可以调用 Azure 资源托管标识终结点和请求令牌。 使用支持 Azure 资源托管标识的其他资源也具有类似体验。

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>如果没有在请求中指定标识，IMDS 将默认采用什么标识？

- 如果启用了系统分配的托管标识并且没有在请求中指定标识，则 IMDS 将默认采用系统分配的托管标识。
- 如果未启用系统分配的托管标识并且仅存在一个用户分配的托管标识，则 IMDS 将默认采用该单一用户分配的托管标识。 
- 如果未启用系统分配的托管的标识，并且存在多个用户分配的托管标识，则必须在请求中指定一个托管标识。

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>如果我将订阅移动到另一个目录中，是否会自动重新创建托管标识？

否。 如果将订阅移到另一个目录中，则必须手动重新创建托管标识并重新向它们授予 Azure 角色分配。
- 对于系统分配的托管标识：禁用并重新启用。 
- 对于用户分配的托管标识：删除、重新创建并重新将其附加到所需的资源（例如虚拟机）

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>是否可以使用托管标识来访问不同目录/租户中的资源？

否。 托管标识当前不支持跨目录方案。 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>在资源上进行标识托管需要什么 Azure RBAC 权限？ 

- 系统分配的托管标识：需要针对资源的写入权限。 例如，对于虚拟机，你需要 Microsoft.Compute/virtualMachines/write 权限。 此操作包含在特定于资源的内置角色（如[虚拟机参与者](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)）中。
- 用户分配的托管标识：需要对资源的写入权限。 例如，对于虚拟机，你需要 Microsoft.Compute/virtualMachines/write 权限。 除了针对托管标识分配的[托管标识操作员](../../role-based-access-control/built-in-roles.md#managed-identity-operator)角色外。


## <a name="known-issues"></a>已知问题

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>尝试 Azure 资源托管标识扩展的架构导出功能时，“自动化脚本”失败

如果在 VM 上启用了 Azure 资源托管标识，当尝试将“自动化脚本”功能用于 VM 或其资源组时，将显示以下错误：

![Azure 资源托管标识自动化脚本导出错误](./media/msi-known-issues/automation-script-export-error.png)

Azure 资源托管标识 VM 扩展（计划在 2019 年 1 月弃用）当前不支持将其架构导出到资源组模板的功能。 因此，生成的模板不显示用于在资源上启用 Azure 资源托管标识的配置参数。 按照[使用模板在 Azure VM 上配置 Azure 资源托管标识](qs-configure-template-windows-vm.md)中的示例，可以手动添加这些部分。

当架构导出功能可用于 Azure 资源托管标识 VM 扩展（计划在 2019 年 1 月弃用）时，它将在[导出包含 VM 扩展的资源组](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)中列出。

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>从资源组或订阅迁移后无法启动 VM

如果迁移处于运行状态的 VM，它将在迁移期间继续运行。 不过，如果在迁移后停止并重启 VM，那么它将无法启动。 导致此问题发生的原因是，VM 未更新对 Azure 资源托管标识的标识的引用，仍然继续指向旧资源组中的标识。

**解决方法** 
 
在 VM 上触发更新，以便它可以获取正确的 Azure 资源托管标识的值。 可以更改 VM 属性，从而更新对 Azure 资源托管标识的标识的引用。 例如，可以运行下列命令，在 VM 上设置新的标记值：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
此命令在 VM 上设置值为 1 的新标记“fixVM”。 
 
通过设置此属性，VM 可以更新包含正确的 Azure 资源托管标识资源 URI，然后就应该能启动 VM 了。 
 
在 VM 启动后，便可以运行下列命令，从而删除此标记：

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>在 Azure AD 目录之间转移订阅

将订阅移动/转移到另一个目录时，托管标识不会更新。 因此，任何现存的系统分配的或用户分配的托管标识将被破坏。 

对于已移到另一目录的订阅中的托管标识，解决方法是：

 - 对于系统分配的托管标识：禁用并重新启用。 
 - 对于用户分配的托管标识：删除、重新创建并重新将其附加到所需的资源（例如虚拟机）

有关详细信息，请参阅将 [Azure 订阅转移到其他 Azure AD 目录](../../role-based-access-control/transfer-subscription.md)。

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>将用户分配的托管标识移动到其他资源组/订阅

不支持将用户分配的托管标识移动到其他资源组。
