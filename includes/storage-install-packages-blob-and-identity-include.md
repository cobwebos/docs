---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666150"
---
## <a name="install-client-library-packages"></a>安装客户端库包

> [!NOTE]
> 此处显示的示例使用 Azure 存储客户端库版本12。 版本12的客户端库是 Azure SDK 的一部分。 有关 Azure SDK 的详细信息，请参阅[GitHub](https://github.com/Azure/azure-sdk)上的 azure sdk 存储库。

若要安装 Blob 存储包，请从 NuGet 包管理器控制台运行以下命令：

```powershell
Install-Package Azure.Storage.Blobs
```

此处所示的示例还使用最新版本的[适用于 .net 的 Azure 标识客户端库](https://www.nuget.org/packages/Azure.Identity/)通过 Azure AD 凭据进行身份验证。 若要安装该包，请从 NuGet 包管理器控制台运行以下命令：

```powershell
Install-Package Azure.Identity
```

若要详细了解如何使用 azure 存储空间中的 Azure 标识客户端库进行身份验证，请**参阅在**[使用 azure 资源的 Azure Active Directory 和托管标识授予对 blob 和队列的访问权限](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)。