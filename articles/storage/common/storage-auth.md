---
title: 授权访问 Azure 存储 | Microsoft Docs
description: 了解授权访问 Azure 存储的各种方式，包括 Azure Active Directory、共享密钥身份验证或共享访问签名。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: a0717785f4f9c1c21a18d081d157a6cdc8c12f18
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371185"
---
# <a name="authorizing-access-to-azure-storage"></a>授权访问 Azure 存储

每次访问存储帐户中的数据时，客户端都会通过 HTTP/HTTPS 向 Azure 存储发出请求。 每个对安全资源的请求都必须经过授权，以便服务确保客户端具有访问数据所需的权限。

下表介绍了 Azure 存储提供的用于授权访问资源的选项:

|  |共享密钥 (存储帐户密钥)  |共享访问签名 (SAS)  |Azure Active Directory (Azure AD)  |匿名公共读取访问  |
|---------|---------|---------|---------|---------|
|Azure Blob     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-dotnet-shared-access-signature-part-1.md)         |[支持](storage-auth-aad.md)         |[支持](../blobs/storage-manage-access-to-resources.md)         |
|Azure 文件 (SMB)     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |不支持         |[支持, 仅适用于 AAD 域服务](../files/storage-files-active-directory-overview.md)         |不支持         |
|Azure 文件 (REST)     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-dotnet-shared-access-signature-part-1.md)         |不支持         |不支持         |
|Azure 队列     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-dotnet-shared-access-signature-part-1.md)         |[支持](storage-auth-aad.md)         |不支持         |
|Azure 表     |[支持](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)         |[支持](storage-dotnet-shared-access-signature-part-1.md)         |不支持         |不支持         |

下面简要介绍了每个授权选项:

- Blob 和队列的**Azure Active Directory (Azure AD) 集成**。 Azure AD 提供基于角色的访问控制 (RBAC)，用于精细地控制客户端对存储帐户中资源的访问权限。 有关 blob 和队列 Azure AD 集成的详细信息, 请参阅[使用 Azure Active Directory 对 Azure 存储的请求进行身份验证](storage-auth-aad.md)。

- **Azure AD 文件的域服务 (DS) 集成 (预览版)** 。 Azure 文件通过 Azure AD DS 支持通过服务器消息块 (SMB) 进行基于身份的身份验证。 这为对客户端对存储帐户中的资源的访问权限进行精细控制提供了 RBAC。 有关使用域服务 Azure AD 集成文件的详细信息, 请参阅[Azure 文件概述 Azure Active Directory 域服务 (AAD DS) 身份验证支持 SMB 访问 (预览)](../files/storage-files-active-directory-overview.md)。

- 用于 blob、文件、队列和表的**共享密钥授权**。 使用共享密钥的客户端会随使用存储帐户访问密钥签名的每个请求传递一个标头。 有关详细信息，请参阅[通过共享密钥进行授权](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-shared-key/)。
- 用于 blob、文件、队列和表的**共享访问签名**。 共享访问签名 (SAS) 针对存储帐户中的资源提供有限的委托访问权限。 通过对签名的有效时间间隔或对它授予的权限添加约束，可灵活地管理访问权限。 有关详细信息，请参阅[使用共享访问签名 (SAS)](storage-dotnet-shared-access-signature-part-1.md)。
- 用于容器和 blob 的**匿名公共读取访问**。 无需授权。 有关详细信息，请参阅[管理对容器和 Blob 的匿名读取访问](../blobs/storage-manage-access-to-resources.md)。  

默认情况下，Azure 存储中的所有资源都受到保护，并且只能由帐户所有者使用。 虽然你可以使用上述任何授权策略为客户端授予访问存储帐户资源的权限，但 Microsoft 建议尽可能使用 Azure AD，以获得最大的安全性和易用性。 
