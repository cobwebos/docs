---
title: 修复 Azure AD 域服务中不匹配的目录错误 |Microsoft Docs
description: 了解不匹配的目录错误的含义，以及如何在 Azure AD 域服务中解决此错误
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: iainfou
ms.openlocfilehash: 76dc964b7fe7f5e8acfcfb03b2e89bebb2caa176
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77613387"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-ad-domain-services-managed-domains"></a>解决现有 Azure AD 域服务托管域的不匹配目录错误

如果 Azure Active Directory 域服务（Azure AD DS）托管域显示不匹配的租户错误，则在解决之前无法管理托管域。 如果将基础 Azure 虚拟网络移到其他 Azure AD 目录，则会发生此错误。

本文介绍错误发生的原因以及解决方法。

## <a name="what-causes-this-error"></a>导致此错误的原因是什么？

当 Azure AD DS 托管域和虚拟网络属于两个不同的 Azure AD 租户时，会发生不匹配的目录错误。 例如，你可能有一个名为*aaddscontoso.com*的 Azure AD DS 托管域，该域在 Contoso 的 Azure AD 租户中运行。 但是，托管域的 Azure 虚拟网络属于 Fabrikam Azure AD 租户。

Azure 使用基于角色的访问控制（RBAC）来限制对资源的访问。 在 Azure AD 租户中启用 Azure AD DS 时，凭据哈希将同步到托管域。 此操作要求你是 Azure AD 目录的租户管理员，并且必须控制对凭据的访问。 若要将资源部署到 Azure 虚拟网络并控制流量，你必须对部署 Azure AD DS 的虚拟网络具有管理权限。

为了使 RBAC 始终有效并安全地访问 Azure AD DS 使用的所有资源，托管域和虚拟网络必须属于同一 Azure AD 租户。

以下规则在 Resource Manager 环境中适用：

- 一个 Azure AD 目录可以包含多个 Azure 订阅。
- 一个 Azure 订阅可以包含虚拟网络等多个资源。
- 为 Azure AD 目录启用单个 Azure AD 域服务托管域。
- 可以在属于相同 Azure AD 租户中的任意 Azure 订阅的虚拟网络上启用 Azure AD 域服务托管域。

### <a name="valid-configuration"></a>有效配置

在以下示例部署方案中，contoso Azure AD 租户中已启用 Contoso Azure AD DS 托管域。 托管域部署在属于 Contoso Azure AD 租户所拥有的 Azure 订阅的虚拟网络中。 托管域和虚拟网络都属于同一 Azure AD 租户。 此示例配置有效且完全受支持。

![具有同一 Azure AD 租户的托管域和虚拟网络部分的有效 Azure AD DS 租户配置](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>不匹配的租户配置

在此示例部署方案中，contoso Azure AD 租户中已启用 Contoso Azure AD DS 托管域。 但是，托管域部署在属于 Fabrikam Azure AD 租户所拥有的 Azure 订阅的虚拟网络中。 托管域和虚拟网络属于两个不同的 Azure AD 租户。 此示例配置是不匹配的租户，不受支持。 必须将虚拟网络移动到与托管域相同的 Azure AD 租户。

![不匹配的租户配置](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>解决不匹配的租户错误

以下两个选项解决了不匹配的目录错误：

* 从现有的 Azure AD 目录中[删除 AZURE AD DS 托管域](delete-aadds.md)。 在与要使用的虚拟网络相同的 Azure AD 目录中[创建替换 AZURE AD DS 托管域](tutorial-create-instance.md)。 准备就绪后，将之前加入到已删除域的所有计算机加入到重新创建的托管域。
* 将包含虚拟网络的[Azure 订阅移](../cost-management-billing/manage/billing-subscription-transfer.md)到与 Azure AD DS 托管域相同的 Azure AD 目录中。

## <a name="next-steps"></a>后续步骤

有关排查 Azure AD DS 问题的详细信息，请参阅[故障排除指南](troubleshoot.md)。
