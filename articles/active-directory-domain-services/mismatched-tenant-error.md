---
title: 解决 Azure AD 域服务中目录不匹配的错误 | Microsoft Docs
description: 了解“目录不匹配”错误的含义以及如何在 Azure AD 域服务中解决此错误
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 40eb75b7-827e-4d30-af6c-ca3c2af915c7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: 0c2a6ee4ee5c92122e896e62485479d50c660c7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91968066"
---
# <a name="resolve-mismatched-directory-errors-for-existing-azure-active-directory-domain-services-managed-domains"></a>解决现有 Azure Active Directory 域服务托管域的目录不匹配错误

如果 Azure Active Directory 域服务 (Azure AD DS) 托管域显示“租户不匹配”错误，如果不解决，就无法管理托管域。 如果将基础 Azure 虚拟网络移动到其他 Azure Active 目录，则会发生此错误。

本文介绍错误发生的原因以及其解决方法。

## <a name="what-causes-this-error"></a>导致此错误的原因是什么？

当 Azure AD DS 托管域和虚拟网络属于两个不同的 Azure AD 租户时，会发生目录不匹配错误。 例如，你可能有一个名为 aaddscontoso.com 的托管域，该域在 Contoso 的 Azure AD 租户中运行。 但是，托管域的 Azure 虚拟网络是 Fabrikam Azure AD 租户的一部分。

使用 azure RBAC)  (azure 基于角色的访问控制来限制对资源的访问。 在 Azure AD 租户中启用 Azure AD DS 时，凭据哈希将同步到托管域。 此操作要求你是 Azure AD 目录的租户管理员，并且必须控制对凭据的访问。

要将资源部署到 Azure 虚拟网络并控制流量，必须对你将托管域部署到的虚拟网络具有管理权限。

为了使 Azure RBAC 始终能够安全地访问 Azure AD DS 使用的所有资源，托管域和虚拟网络必须属于同一 Azure AD 租户。

以下规则适用于部署：

- 一个 Azure AD 目录可以包含多个 Azure 订阅。
- 一个 Azure 订阅可以包含虚拟网络等多个资源。
- 为 Azure AD 目录启用单个托管域。
- 可以在属于相同 Azure AD 租户中任意 Azure 订阅的虚拟网络上启用托管域。

### <a name="valid-configuration"></a>有效配置

在以下示例部署方案中，将在 Contoso Azure AD 租户中启用 Contoso 托管域。 托管域部署在属于 Contoso Azure AD 租户所有的 Azure 订阅的虚拟网络中。

托管域和虚拟网络都属于同一 Azure AD 租户。 此示例配置有效，且完全受支持。

![作为同一 Azure AD 租户一部分的托管域和虚拟网络的有效 Azure AD DS 租户配置](./media/getting-started/valid-tenant-config.png)

### <a name="mismatched-tenant-configuration"></a>不匹配的租户配置

在此示例部署方案中，将在 Contoso Azure AD 租户中启用 Contoso 托管域。 但是，托管域部署在属于 Fabrikam Azure AD 租户所有的 Azure 订阅的虚拟网络中。

托管域以及虚拟网络属于两个不同的 Azure AD 租户。 此示例配置显示的是不匹配的租户，所以配置不受支持。 必须将虚拟网络移到与托管域相同的 Azure AD 租户。

![不匹配的租户配置](./media/getting-started/mismatched-tenant-config.png)

## <a name="resolve-mismatched-tenant-error"></a>解决“不匹配的租户”错误

以下两个选项可解决不匹配的目录错误：

* 首先，从现有 Azure AD 目录中[删除托管域](delete-aadds.md)。 然后，在与想要使用的虚拟网络相同的 Azure AD 目录中[创建替换托管域](tutorial-create-instance.md)。 准备就绪后，将以前加入已删除域的所有计算机加入重新创建的托管域。
* 将包含虚拟网络的[Azure 订阅移](../cost-management-billing/manage/billing-subscription-transfer.md)到与托管域相同的 Azure AD 目录中。

## <a name="next-steps"></a>后续步骤

有关 Azure AD DS 问题故障排除的详细信息，请参阅[故障排除指南](troubleshoot.md)。
