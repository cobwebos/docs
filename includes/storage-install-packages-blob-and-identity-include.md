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
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806570"
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
