---
title: Azure 资源的托管服务标识
description: 概述 Azure 资源的托管服务标识功能。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 1edd6f846d539b1f263877a5e0af107148513c6e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398379"
---
# <a name="what-is-managed-service-identity-for-azure-resources"></a>什么是 Azure 资源的托管服务标识？

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

生成云应用程序时需要应对的常见挑战是，如何管理代码中用于云服务身份验证的凭据。 保护这些凭据是一项重要任务。 理想情况下，这些凭据永远不会出现在开发者工作站上，也不会被签入源代码管理系统中。 虽然 Azure Key Vault 可用于安全存储凭据、机密以及其他密钥，但代码需要通过 Key Vault 的身份验证才能检索它们。 

Azure Active Directory (Azure AD) 中的托管服务标识功能可以解决此问题。 此功能为 Azure 服务提供了 Azure AD 中的自动托管标识。 可以使用此标识向支持 Azure AD 身份验证的任何服务（包括 Key Vault）证明身份，无需在代码中放入任何凭据。

如果有 Azure 订阅，Azure AD 中的托管服务标识功能是免费的。 不需额外付费。

## 如何使用此功能？<a name="how-does-it-work"></a>

托管标识分为两种类型：

- **系统分配的标识**直接在 Azure 服务实例上启用。 启用标识后，Azure 将在实例的订阅信任的 Azure AD 租户中创建实例的标识。 创建标识后，系统会将凭据预配到实例。 系统分配的标识的生命周期直接绑定到启用它的 Azure 服务实例。 如果实例遭删除，Azure 会自动清理 Azure AD 中的凭据和标识。
- **用户分配的标识**是作为独立的 Azure 资源创建的。 在创建过程中，Azure 会在由所用订阅信任的 Azure AD 租户中创建一个标识。 在创建标识后，可以将标识分配到一个或多个 Azure 服务实例。 用户分配的标识的生命周期与它所分配到的 Azure 服务实例的生命周期是分开管理的。

代码可以使用托管服务标识来请求支持 Azure AD 身份验证的服务的访问令牌。 Azure 负责滚动更新服务实例使用的凭据。

下图演示了托管服务标识如何与 Azure 虚拟机 (VM) 协同工作：

![托管服务标识和 Azure VM](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-identity-works-with-an-azure-vm"></a>系统分配标识如何与 Azure VM 协同工作

1. Azure 资源管理器收到请求，要求在 VM 上启用系统分配的标识。
2. Azure 资源管理器在 Azure AD 中创建与 VM 标识相对应的服务主体。 服务主体在此订阅信任的 Azure AD 租户中创建。
3. Azure 资源管理器在 VM 上配置标识：
    1. 使用服务主体客户端 ID 和证书更新 Azure 实例元数据服务标识终结点。
    1. 预配 VM 扩展并添加服务主体客户端 ID 和证书。 （根据计划，此步骤将弃用。）
4. VM 有了标识以后，请根据服务主体信息向 VM 授予对 Azure 资源的访问权限。 若要调用 Azure 资源管理器，请在 Azure AD 中使用基于角色的访问控制 (RBAC) 向 VM 服务主体分配相应的角色。 若要调用 Key Vault，请授予代码对 Key Vault 中特定机密或密钥的访问权限。
5. 在 VM 上运行的代码可以从只能从 VM 中访问的两个终结点请求令牌：

    - Azure 实例元数据服务标识终结点（推荐）：`http://169.254.169.254/metadata/identity/oauth2/token`
        - resource 参数指定了要向其发送令牌的服务。 若要向 Azure 资源管理器进行身份验证，请使用 `resource=https://management.azure.com/`。
        - API 版本参数指定 IMDS 版本，请使用 api-version=2018-02-01 或更高版本。
    - VM 扩展终结点（计划弃用）：`http://localhost:50342/oauth2/token` 
        - resource 参数指定了要向其发送令牌的服务。 若要向 Azure 资源管理器进行身份验证，请使用 `resource=https://management.azure.com/`。

6. 调用了 Azure AD，以便使用在步骤 3 中配置的客户端 ID 和证书请求访问令牌（在步骤 5 中指定）。 Azure AD 返回 JSON Web 令牌 (JWT) 访问令牌。
7. 代码在调用支持 Azure AD 身份验证的服务时发送访问令牌。

### <a name="how-a-user-assigned-identity-works-with-an-azure-vm"></a>用户分配标识如何与 Azure VM 协同工作

1. Azure 资源管理器收到请求，要求创建用户分配的标识。
2. Azure 资源管理器在 Azure AD 中创建与用户分配标识相对应的服务主体。 服务主体在此订阅信任的 Azure AD 租户中创建。
3. Azure 资源管理器收到请求，要求在 VM 上配置用户分配的标识：
    1. 使用用户分配的标识服务主体客户端 ID 和证书更新 Azure 实例元数据服务标识终结点。
    1. 预配 VM 扩展并添加用户分配的标识服务主体客户端 ID 和证书。 （根据计划，此步骤将弃用。）
4. 创建用户分配的标识以后，请根据服务主体信息向标识授予对 Azure 资源的访问权限。 若要调用 Azure 资源管理器，请在 Azure AD 中使用 RBAC 向用户分配标识的服务主体分配相应的角色。 若要调用 Key Vault，请授予代码对 Key Vault 中特定机密或密钥的访问权限。

   > [!Note]
   > 也可在步骤 3 之前执行此步骤。

5. 在 VM 上运行的代码可以从只能从 VM 中访问的两个终结点请求令牌：

    - Azure 实例元数据服务标识终结点（推荐）：`http://169.254.169.254/metadata/identity/oauth2/token`
        - resource 参数指定了要向其发送令牌的服务。 若要向 Azure 资源管理器进行身份验证，请使用 `resource=https://management.azure.com/`。
        - 客户端 ID 参数指定为其请求令牌的标识。 当单台 VM 上有多个用户分配的标识时，此值是消除歧义所必需的。
        - API 版本参数指定 Azure 实例元数据服务版本。 请使用 `api-version=2018-02-01` 或指定更高的版本。

    - VM 扩展终结点（计划弃用）：`http://localhost:50342/oauth2/token`
        - resource 参数指定了要向其发送令牌的服务。 若要向 Azure 资源管理器进行身份验证，请使用 `resource=https://management.azure.com/`。
        - 客户端 ID 参数指定为其请求令牌的标识。 当单台 VM 上有多个用户分配的标识时，此值是消除歧义所必需的。
6. 调用了 Azure AD，以便使用在步骤 3 中配置的客户端 ID 和证书请求访问令牌（在步骤 5 中指定）。 Azure AD 返回 JSON Web 令牌 (JWT) 访问令牌。
7. 代码在调用支持 Azure AD 身份验证的服务时发送访问令牌。

## <a name="how-can-i-use-managed-service-identities"></a>如何使用托管服务标识？

若要了解如何使用托管服务标识来访问不同的 Azure 资源，请尝试以下教程。

了解如何将托管服务标识与 Windows VM 配合使用：

* [访问 Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [访问 Azure 资源管理器](tutorial-windows-vm-access-arm.md)
* [访问 Azure SQL](tutorial-windows-vm-access-sql.md)
* [使用访问密钥访问 Azure 存储](tutorial-windows-vm-access-storage.md)
* [使用共享访问签名访问 Azure 存储](tutorial-windows-vm-access-storage-sas.md)
* [使用 Azure Key Vault 访问非 Azure AD 资源](tutorial-windows-vm-access-nonaad.md)

了解如何将托管服务标识与 Linux VM 配合使用：

* [访问 Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [访问 Azure 资源管理器](tutorial-linux-vm-access-arm.md)
* [使用访问密钥访问 Azure 存储](tutorial-linux-vm-access-storage.md)
* [使用共享访问签名访问 Azure 存储](tutorial-linux-vm-access-storage-sas.md)
* [使用 Azure Key Vault 访问非 Azure AD 资源](tutorial-linux-vm-access-nonaad.md)

了解如何将托管服务标识与其他 Azure 服务配合使用：

* [Azure 应用服务](/azure/app-service/app-service-managed-service-identity)
* [Azure Functions](/azure/app-service/app-service-managed-service-identity)
* [Azure 服务总线](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure 事件中心](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API 管理](../../api-management/api-management-howto-use-managed-service-identity.md)

## 哪些 Azure 服务支持此功能？<a name="which-azure-services-support-managed-service-identity"></a>

托管服务标识可以用来向支持 Azure AD 身份验证的服务证明身份。 有关支持托管服务标识功能的 Azure 服务的列表，请参阅[支持托管服务标识的服务](services-support-msi.md)。

## <a name="next-steps"></a>后续步骤

请参阅以下快速入门，开始使用托管服务标识功能：

* [使用 Windows VM 托管服务标识访问资源管理器](tutorial-windows-vm-access-arm.md)
* [使用 Linux VM 托管服务标识访问资源管理器](tutorial-linux-vm-access-arm.md)
