---
title: Azure 托管 HSM 访问控制
description: 管理 Azure 托管 HSM 和密钥的访问权限。 介绍托管 HSM 的身份验证和授权模式，以及如何保护 Hsm。
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a21d0db383e8c563f0b187061a95ac818dd2a4f0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994279"
---
# <a name="managed-hsm-access-control"></a>托管 HSM 访问控制

> [!NOTE]
> Key Vault 资源提供程序支持两种资源类型： **保管库** 和 **托管的 hsm**。 本文中所述的访问控制仅适用于 **托管的 hsm**。 若要详细了解托管 HSM 的访问控制，请参阅 [使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../general/rbac-guide.md)。

Azure Key Vault 托管 HSM 是一项保护加密密钥的云服务。 因为此数据是敏感数据和业务关键数据，所以需要保护对托管 HSM 的访问，只允许得到授权的应用程序和用户进行访问。 本文简要介绍了托管 HSM 访问控制模型。 其中介绍了身份验证和授权，以及如何保护对托管 HSM 的访问。

## <a name="access-control-model"></a>访问控制模型

可以通过两个接口控制对托管 HSM 的访问： **管理平面** 和 **数据平面**。 管理平面是用于管理 HSM 本身的位置。 此平面中的操作包括创建和删除托管的 Hsm 和检索托管的 HSM 属性。 数据平面用于处理托管 HSM 中存储的数据（即 HSM 支持的加密密钥）。 可以添加、删除、修改和使用密钥来执行加密操作，管理角色分配以控制对密钥的访问权限，创建完整的 HSM 备份，还原完整备份，以及从数据平面接口管理安全域。

若要在任一平面中访问托管 HSM，所有调用方必须具有正确的身份验证和授权。 身份验证可确定调用方的身份。 授权可确定调用方能够执行的操作。 调用方可以是 Azure Active Directory 用户、组、服务主体或托管标识中定义的任何一种 [安全主体](../../role-based-access-control/overview.md#security-principal) 。

两个平面都使用 Azure Active Directory 进行身份验证。 对于授权，它们使用不同的系统，如下所示
- 管理平面使用 Azure 基于角色的访问控制-Azure RBAC-在 Azure Azure 上构建的授权系统资源管理器 
- 数据平面使用托管 hsm 级 RBAC (托管 HSM 本地 RBAC) -在托管 HSM 级别实施并强制实施授权系统。

创建托管 HSM 时，请求者还会提供数据平面管理员列表， (所有 [安全主体](../../role-based-access-control/overview.md#security-principal) 都) 支持。 只有这些管理员才能访问托管的 HSM 数据平面，以执行关键操作，并管理 (托管 HSM 本地 RBAC) 的数据平面角色分配。

这两个平面的权限模型使用相同的语法 (RBAC) ，但它们在不同的级别强制执行，角色分配使用不同的范围。 管理平面 RBAC 由 Azure 资源管理器强制执行，而数据平面 RBAC 由托管 HSM 本身强制执行。

> [!IMPORTANT]
> 如果授予安全主体管理平面对托管 HSM 的访问权限，则不会向其授予对数据平面的任何访问权限，以访问密钥或数据平面角色分配托管 HSM 本地 RBAC) 。 这种隔离是设计使然，以防止无意中扩展对托管 HSM 中存储的密钥的访问权限。

例如，订阅管理员 (因为订阅中的所有资源都有 "参与者" 权限) 可以在其订阅中删除托管 HSM，但如果他们没有通过托管 HSM 本地 RBAC 专门授予的数据平面访问权限，则他们将无法访问管理的 HSM 中的密钥或管理角色分配，以向自己或其他人授予对数据平面的访问权限。

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 身份验证

在 Azure 订阅中创建托管 HSM 时，它会自动与订阅的 Azure Active Directory 租户相关联。 这两个平面中的所有调用方都必须在此租户中注册，并进行身份验证才能访问托管的 HSM。

在调用任一平面之前，应用程序将用 Azure Active Directory 进行身份验证。 应用程序可以根据应用程序类型使用任何 [受支持的身份验证方法](../../active-directory/develop/authentication-scenarios.md) 。 应用程序获取平面中某个资源的令牌，以获取访问权限。 资源是管理平面或数据平面中基于 Azure 环境的终结点。 应用程序使用令牌并将 REST API 请求发送到托管 HSM 终结点。 若要了解详细信息，请查看[整个身份验证流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

对于这两个平面使用单个身份验证机制具有多项优势：

- 组织可以集中控制其组织中的所有托管 Hsm 的访问。
- 如果用户离开，他们会立即失去对组织中所有托管 Hsm 的访问权限。
- 组织可以使用 Azure Active Directory 中的选项自定义身份验证，例如启用多重身份验证以提高安全性。

## <a name="resource-endpoints"></a>资源终结点

安全主体通过终结点访问平面。 两个平面的访问控制独立运行。 若要授予应用程序访问权限以使用托管 HSM 中的密钥，请使用托管 HSM 本地 RBAC 授予数据平面访问权限。 若要授予用户对托管 HSM 资源的访问权限，以创建、读取、删除、移动托管 Hsm，并编辑使用 Azure RBAC 的其他属性和标记。

下表显示了用于管理平面和数据平面的终结点。

| 访问&nbsp;平面 | 访问终结点 | 操作 | 访问控制机制 |
| --- | --- | --- | --- |
| 管理平面 | **全球：**<br> management.azure.com:443<br> | 创建、读取、更新、删除和移动托管的 Hsm<br>设置托管 HSM 标记 | Azure RBAC |
| 数据平面 | **全球：**<br> &lt;hsm-name &gt; . vault.azure.net:443<br> | **密钥**：解密、加密、<br> 解包、打包、验证、签名、获取、列出、更新、创建、导入、删除、备份、还原、清除<br/><br/> **数据平面角色管理 (托管 HSM 本地 RBAC) * * *：列出角色定义、分配角色、删除角色分配、定义自定义角色 <br/> <br/> **备份/还原 **：备份、还原、检查状态备份/还原操作 <br/> <br/> **安全域 * *：下载和上传安全域 | 托管 HSM 本地 RBAC |
|||||
## <a name="management-plane-and-azure-rbac"></a>管理平面和 Azure RBAC

在管理平面中，使用 Azure RBAC 来授权调用方可以执行的操作。 在 RBAC 模型中，每个 Azure 订阅都有一个 Azure Active Directory 的实例。 可以从此目录向用户、组和应用程序授予访问权限。 授予访问权限以管理 Azure 订阅中使用 Azure 资源管理器部署模型的资源。 若要授予访问权限，请使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI](../../cli-install-nodejs.md)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure 资源管理器 REST API](https://msdn.microsoft.com/library/azure/dn906885.aspx)。

在资源组中创建密钥保管库，并使用 Azure Active Directory 来管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 通过分配适当的 RBAC 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 RBAC 角色：

- **管理组**：在订阅级别分配的 RBAC 角色适用于该管理组中的所有订阅。
- **订阅**：在订阅级别分配的 RBAC 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 RBAC 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 RBAC 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [RBAC：内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="data-plane-and-managed-hsm-local-rbac"></a>数据平面和托管 HSM 本地 RBAC

可以通过分配角色向安全主体授予执行特定密钥操作的访问权限。 对于每个角色分配，需指定应用分配的角色和作用域。 对于托管 HSM 本地 RBAC，提供两个范围。

- **"/" 或 "/keys"**： HSM 级别作用域。 在此作用域中分配了角色的安全主体可对托管 HSM 中 (密钥) 的所有对象执行角色定义的操作。
- **"/keys/ &lt; key-name &gt; "**：密钥级别作用域。 在此作用域内分配有角色的安全主体可以仅为指定密钥的所有版本执行此角色中定义的操作。

## <a name="next-steps"></a>后续步骤

- 有关面向管理员的入门教程，请参阅[什么是托管 HSM？](overview.md)。
- 有关角色管理教程，请参阅 [托管 HSM 本地 RBAC](role-management.md)
- 若要详细了解托管 HSM 日志记录的使用情况日志记录，请参阅[托管 HSM 日志记录](logging.md)。
