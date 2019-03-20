---
title: 使用 Azure Active Directory 验证对 Azure blob 和队列的访问权限（预览版） | Microsoft Docs
description: 使用 Azure Active Directory 验证对 Azure blob 和队列的访问权限（预览版）。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/13/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: dcf7d237c8cfbf52a804e428d84fff0bb328c7c8
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012107"
---
# <a name="authenticate-access-to-azure-blobs-and-queues-using-azure-active-directory-preview"></a>使用 Azure Active Directory 验证对 Azure blob 和队列的访问权限（预览版）

Azure 存储支持为 Blob 和队列服务使用 Azure Active Directory (AD) 进行身份验证和授权。 通过 Azure AD，可以使用基于角色的访问控制 (RBAC) 向用户、组或应用程序服务主体授予访问权限。 

与其他授权方式相比，使用 Azure AD 凭据对用户或应用程序进行身份验证可以提供优越的安全性和易用性。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 也可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。 Microsoft 建议尽量对 Azure 存储应用程序使用 Azure AD 身份验证。

[!INCLUDE [storage-auth-aad-note-include](../../../includes/storage-auth-aad-note-include.md)]

## <a name="about-the-preview"></a>关于此预览版

对于此预览版，请注意以下几点：

- 在此预览版中，Azure AD 集成仅可用于 Blob 和队列服务。
- Azure AD 集成可用于所有公共区域中的 GPv1、GPv2 和 Blob 存储帐户。 
- 仅支持使用资源管理器部署模型创建的存储帐户。 
- 不久将支持在 Azure 存储分析日志中记录调用方标识信息。
- 当前支持通过 Azure AD 授权访问标准存储帐户中的资源。 不久将支持授权访问高级存储帐户中的页 blob。
- Azure 存储同时支持内置的和自定义 RBAC 角色。 可以分配作用域为订阅、资源组、存储帐户或单个容器或队列的角色。
- 目前支持 Azure AD 集成的 Azure 存储客户端库包括：
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage)
    - [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)
    - Python
        - [Blob、队列和表](https://github.com/Azure/azure-storage-python)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

## <a name="overview-of-azure-ad-for-storage"></a>Azure AD 进行存储的概述

将 Azure AD 与 Azure 存储集成的第一步是为服务主体（用户、组或应用程序服务主体）或 Azure 资源的托管标识分配对存储数据的 RBAC 角色。 RBAC 角色包含针对容器和队列的共有权限集。 若要了解有关 Azure 存储的分配 RBAC 角色的详细信息，请参阅[对使用 RBAC （预览版） 存储数据的管理访问权限](storage-auth-aad-rbac.md)。

若要使用 Azure AD 来授权访问应用程序中的存储资源，则需要通过代码请求一个 OAuth 2.0 访问令牌。 若要了解如何请求访问令牌并使用它来授权允许对 Azure 存储的请求，请参阅[使用 Azure AD 通过 Azure 存储应用程序进行身份验证（预览版）](storage-auth-aad-app.md)。 如果正在使用托管标识，请参阅[使用 Azure 资源的 Azure 托管标识（预览版）验证对 Blob 和队列的访问权限](storage-auth-aad-msi.md)。

Azure CLI 和 PowerShell 现在支持使用 Azure AD 标识创建日志记录。 使用 Azure AD 标识登录后，会话将以该标识的名义运行。 若要了解详细信息，请参阅[使用 Azure AD 标识通过 CLI 或 PowerShell 访问 Azure 存储（预览版）](storage-auth-aad-script.md)。

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 和队列的 RBAC 角色

Azure Active Directory (Azure AD) 通过[基于角色的访问控制 (RBAC)](../../role-based-access-control/overview.md) 授权访问受保护的资源。 Azure 存储定义了一组内置的 RBAC 角色，它们包含用于访问容器或队列的通用权限集。 

RBAC 角色分配到 Azure AD 安全主体，Azure 授予访问这些资源时为该安全主体。 可以将访问权限限定于订阅、资源组、存储帐户、单个容器或队列级别。 Azure AD 安全主体可能是用户、 组、 应用程序服务主体，或[管理 Azure 资源的标识](../../active-directory/managed-identities-azure-resources/overview.md)。 

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

若要了解如何将在 Azure 门户中的内置角色分配，请参阅[授予对 Azure 容器和队列使用 RBAC （预览版） 在 Azure 门户中的访问](storage-auth-aad-rbac.md)。

### <a name="access-permissions-granted-by-rbac-roles"></a>通过 RBAC 角色授予访问权限 

下表总结了授予不同级别的作用域的内置角色的访问权限：

|范围|Blob 数据所有者|Blob 数据参与者|Blob 数据读取器|队列数据参与者|队列数据读取器|
|---|---|---|---|---|---|
|订阅级别|对订阅中的所有容器和 blob 具有读/写访问权限和 POSIX 访问控制管理权限|对订阅中的所有容器和 blob 具有读/写访问权限| 对订阅中的所有容器和 blob 具有读取访问权限|对订阅中的所有队列具有读/写访问权限|对订阅中的所有队列具有读取访问权限|
|资源组级别|对资源组中的所有容器和 blob 具有读/写访问权限和 POSIX 访问控制管理权限|对资源组中的所有容器和 blob 具有读/写访问权限|对资源组中的所有容器和 blob 具有读取访问权限|对资源组中的所有队列具有读/写访问权限|对资源组中的所有队列具有读取访问权限|
|存储帐户级别|对存储帐户中的所有容器和 blob 具有读/写访问权限和 POSIX 访问控制管理权限|对存储帐户中的所有容器和 blob 具有读/写访问权限|对存储帐户中的所有容器和 blob 具有读取访问权限|对存储帐户中的所有队列具有读/写访问权限|对存储帐户中的所有队列具有读取访问权限|
|容器/队列级别|对指定容器及其 blob 具有读/写访问权限和 POSIX 访问控制管理权限。|对指定容器及其 blob 具有读/写访问权限|对指定容器及其 blob 具有读取访问权限|对指定队列具有读/写访问权限|对指定队列具有读取访问权限|

有关调用 Azure 存储操作所需的权限的详细信息，请参阅[用于调用 REST 操作的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations)。

## <a name="next-steps"></a>后续步骤

有关适用于 Azure Blob 和队列的 Azure AD 集成的其他信息，请参阅 Azure 存储团队博客文章[宣布推出适用于 Azure 存储的 Azure AD 身份验证预览版](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
