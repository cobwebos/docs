---
title: include 文件
description: include 文件
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011996"
---
适用于 .NET 的[Microsoft Azure 应用程序身份验证](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)客户端库 (预览版) 可简化从代码中获取和续订令牌的过程。 应用身份验证客户端库自动管理身份验证。 库使用开发人员的凭据在本地开发期间进行身份验证。 在本地开发期间使用开发人员凭据更安全，因为不需创建 Azure AD 凭据，或者不需在开发人员之间共享凭据。 稍后将解决方案部署到 Azure 后, 库会自动切换到使用应用程序凭据。

若要使用 Azure 存储应用程序中的应用身份验证库, 请从[Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)安装最新的预览版包, 并安装适用于 .Net 和 azure Blob 存储客户端库的最新[azure 存储空间客户端](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)库[适用于 .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)。 将以下**using**语句添加到代码中:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
