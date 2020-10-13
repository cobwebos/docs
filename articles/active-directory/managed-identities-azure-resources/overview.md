---
title: Azure 资源的托管标识
description: Azure 资源托管标识概述。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803104"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>什么是 Azure 资源的托管标识？

开发人员面临的一个共同挑战是如何管理密码和凭据，以确保不同服务之间的通信安全。 在 Azure 上，托管标识可为 Azure AD 中的 Azure 资源提供标识并使用它来获取 Azure Active Directory (Azure AD) 令牌，从而使开发人员无需管理凭据。 这也有助于访问 [Azure Key Vault](../../key-vault/general/overview.md)，开发人员可在其中安全地存储凭据。 Azure 资源的托管标识通过在 Azure AD 中为 Azure 服务提供一个自动托管标识来解决此问题。

托管标识有哪些用途？

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

下面是使用托管标识的一些好处：

- 你无需管理凭据， 而且你甚至可能都无法访问凭据。
- 可以使用托管标识向支持 Azure AD 身份验证的任何 Azure 服务（包括 Azure Key Vault）进行身份验证。
- 无需额外付费也可使用托管标识。

> [!NOTE]
> Azure 资源托管标识是以前称为托管服务标识 (MSI) 的服务的新名称。

## <a name="managed-identity-types"></a>托管标识类型

托管标识分为两种类型：

- **系统分配**：某些 Azure 服务允许你直接在服务实例上启用托管标识。 启用系统分配的托管标识后，系统会在 Azure AD 中创建一个与该服务实例的生命周期相关联的标识。 因此，资源被删除时，Azure 会自动删除标识。 按照设计，只有该 Azure 资源可以使用此标识从 Azure AD 请求令牌。
- **用户分配**：你也可以将托管标识创建为独立的 Azure 资源。 你可以[创建用户分配的托管标识](how-to-manage-ua-identity-portal.md)，并将其分配给一个或多个 Azure 服务实例。 对于用户分配的托管标识，标识与使用它的资源分开管理。 </br></br>

  > [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

下表显示了这两种托管标识之间的差异。

|  属性    | 系统分配的托管标识 | 用户分配的托管标识 |
|------|----------------------------------|--------------------------------|
| 创建 |  作为 Azure 资源（例如 Azure 虚拟机或 Azure 应用服务）的一部分创建 | 作为独立 Azure 资源创建 |
| 生命周期 | 与用于创建托管标识的 Azure 资源共享生命周期。 <br/> 删除父资源时，也会删除托管标识。 | 独立生命周期。 <br/> 必须显式删除。 |
| 在 Azure 资源之间共享 | 无法共享。 <br/> 只能与单个 Azure 资源相关联。 | 可以共享 <br/> 用户分配的同一个托管标识可以关联到多个 Azure 资源。 |
| 常见用例 | 包含在单个 Azure 资源中的工作负荷 <br/> 需要独立标识的工作负荷。 <br/> 例如，在单个虚拟机上运行的应用程序 | 在多个资源上运行的并可以共享单个标识的工作负荷。 <br/> 需要在预配流程中预先对安全资源授权的工作负荷。 <br/> 其资源经常回收，但权限应保持一致的工作负荷。 <br/> 例如，其中的多个虚拟机需要访问同一资源的工作负荷 |

>[!IMPORTANT]
>无论选择哪种标识，托管标识都是一种只能用于 Azure 资源的特殊类型的服务主体。 删除托管标识时，相应的服务主体也会自动删除。

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>如何使用 Azure 资源的托管标识？

![开发人员如何使用托管标识从其代码访问资源而不管理身份验证信息的一些示例](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>哪些 Azure 服务支持此功能？<a name="which-azure-services-support-managed-identity"></a>

Azure 资源的托管标识可以用来向支持 Azure AD 身份验证的服务证明身份。 如需支持 Azure 资源托管标识功能的 Azure 服务的列表，请参阅[支持 Azure 资源托管标识的服务](./services-support-managed-identities.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Windows VM 系统分配托管标识访问资源管理器](tutorial-windows-vm-access-arm.md)
* [使用 Linux VM 系统分配托管标识访问资源管理器](tutorial-linux-vm-access-arm.md)
* [如何使用应用服务和 Azure Functions 的托管标识](../../app-service/overview-managed-identity.md)
* [如何将托管标识与 Azure 容器实例结合使用](../../container-instances/container-instances-managed-identity.md)
* [实施 Microsoft Azure 资源的托管标识](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)。