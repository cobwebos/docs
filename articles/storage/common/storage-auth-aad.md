---
title: 使用 Azure Active Directory 验证对 Azure 存储的访问权限（预览版）| Microsoft Docs
description: 使用 Azure Active Directory 验证对 Azure 存储的访问权限（预览版）。
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 06/01/2018
ms.author: tamram
ms.openlocfilehash: 9a0782b96b45d27c9b7e603959ecadf5b2632064
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737640"
---
# <a name="authenticate-access-to-azure-storage-using-azure-active-directory-preview"></a>使用 Azure Active Directory 验证对 Azure 存储的访问权限（预览版）

Azure 存储支持为 Blob 和队列服务使用 Azure Active Directory (AD) 进行身份验证和授权。 通过 Azure AD，可以使用基于角色的访问控制 (RBAC) 向用户、组或应用程序服务主体授予访问权限。 

与其他授权选项相比，使用 Azure AD 为访问 Azure 存储的应用程序授权更为安全且易于使用。 虽然可以继续为应用程序使用共享密钥授权，但是，使用 Azure AD 不需要将帐户访问密钥与代码存储在一起。 同样，你可以继续使用共享访问签名 (SAS) 授予对存储帐户中的资源的精细访问权限，但 Azure AD 提供了类似的功能，并且不需要管理 SAS 令牌，也不需要担心吊销已泄露的 SAS。

## <a name="about-the-preview"></a>关于此预览版

对于此预览版，请注意以下几点：

- 在此预览版中，Azure AD 集成仅可用于 Blob 和队列服务。
- Azure AD 集成可用于所有公共区域中的 GPv1、GPv2 和 Blob 存储帐户。 
- 仅支持使用资源管理器部署模型创建的存储帐户。 
- 不久将支持在 Azure 存储分析日志中记录调用方标识信息。
- 当前支持通过 Azure AD 授权访问标准存储帐户中的资源。 不久将支持授权访问高级存储帐户中的页 blob。
- Azure 存储同时支持内置的和自定义 RBAC 角色。 可以分配作用域为订阅、资源组、存储帐户或单个容器或队列的角色。
- 目前支持 Azure AD 集成的 Azure 存储客户端库包括：
    - [.NET](https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0)
    - [Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)（使用 7.1.x-Preview）
    - Python
        - [Blob](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-blob)
        - [队列](https://github.com/Azure/azure-storage-python/releases/tag/v1.2.0rc1-queue)
    - [Node.js](https://www.npmjs.com/package/azure-storage)
    - [JavaScript](https://aka.ms/downloadazurestoragejs)

> [!IMPORTANT]
> 此预览版仅用于非生产用途。 适用于 Azure 存储的 Azure AD 集成正式发布后，生产服务级别协议 (SLA) 方可使用。 如果你的方案尚不支持 Azure AD 集成，请继续使用应用程序中的共享密钥授权或 SAS 令牌。
>
> 在预览版期间，RBAC 角色分配可能需要长达五分钟的时间进行传播。
>
> Azure AD 与 Azure 存储的集成要求使用 HTTPS 进行 Azure 存储操作。

## <a name="get-started-with-azure-ad-for-storage"></a>开始将 Azure AD 用于存储

将 Azure AD 与 Azure 存储集成的第一步是为服务主体（用户、组或应用程序服务主体）或托管服务标识 (MSI) 分配对存储数据的 RBAC 角色。 RBAC 角色包含针对容器和队列的共有权限集。 若要详细了解 Azure 存储中的 RBAC 角色，请参阅[通过 RBAC 管理对存储数据的访问权限（预览版）](storage-auth-aad-rbac.md)。

若要使用 Azure AD 来授权访问应用程序中的存储资源，则需要通过代码请求一个 OAuth 2.0 访问令牌。 若要了解如何请求访问令牌并使用它来授权允许对 Azure 存储的请求，请参阅[使用 Azure AD 通过 Azure 存储应用程序进行身份验证（预览版）](storage-auth-aad-app.md)。 如果使用 Azure 托管服务标识 (MSI)，请参阅[使用 Azure AD 通过 Azure VM 托管服务标识进行身份验证（预览版）](storage-auth-aad-msi.md)。

Azure CLI 和 PowerShell 现在支持使用 Azure AD 标识创建日志记录。 使用 Azure AD 标识登录后，会话将以该标识的名义运行。 若要了解详细信息，请参阅[使用 Azure AD 标识通过 CLI 或 PowerShell 访问 Azure 存储（预览版）](storage-auth-aad-script.md)。

## <a name="next-steps"></a>后续步骤

有关适用于 Azure Blob 和队列的 Azure AD 集成的其他信息，请参阅 Azure 存储团队博客文章[宣布推出适用于 Azure 存储的 Azure AD 身份验证预览版](https://azure.microsoft.com/blog/announcing-the-preview-of-aad-authentication-for-storage/)。
