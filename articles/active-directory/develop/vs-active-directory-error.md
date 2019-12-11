---
title: 诊断 Azure AD 连接服务的错误
description: Active Directory 连接服务可检测到不兼容的身份验证类型
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a7528bafd9183ae917370a6de35d1d94c3783e5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74966446"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>使用 Azure Active Directory 连接服务诊断错误

在检测以前的身份验证代码时，Azure Active Directory 连接的服务检测到不兼容的身份验证类型。

若要正确检测项目中以前的身份验证代码，则必须重新生成该项目。 如果你看到此错误，并且项目中没有以前的身份验证代码，请重新生成并重试。

## <a name="project-types"></a>项目类型

连接服务会检查你正在开发的项目类型，以便可以将正确的身份验证逻辑注入到项目。 如果在项目中 `ApiController` 派生的任何控制器，则该项目将被视为 WebAPI 项目。 如果项目中的控制器均派生自 `MVC.Controller`，则项目会被视为 MVC 项目。 连接服务不支持任何其他项目类型。

## <a name="compatible-authentication-code"></a>兼容的身份验证代码

连接服务还会检查是否存在以前配置的身份验证设置或与该服务兼容的身份验证设置。 如果所有设置都存在，则会将其视为可重入大小写，并且连接的服务将打开显示这些设置。  如果只存在某些设置，则将其视为错误。

在 MVC 项目中，连接服务会检查是否存在以下任何设置（这些设置是以前使用该服务生成的）：

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

而且，连接服务会检查 Web API 项目中是否存在以下任何设置，这是以前使用该服务引起的：

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>不兼容的身份验证代码

最后，连接服务会尝试检测使用以前版本的 Visual Studio 配置的身份验证代码版本。 如果已收到此错误，它表示项目包含不兼容的身份验证类型。 连接服务将通过以前版本的 Visual Studio 检测以下身份验证类型：

* Windows 身份验证
* 单个用户帐户
* 组织帐户

为了检测 MVC 项目中的 Windows 身份验证，连接服务将在 `web.config` 文件中查找 `authentication` 元素。

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

为了检测 Web API 项目中的 Windows 身份验证，连接服务将在项目的 `.csproj` 文件中查找 `IISExpressWindowsAuthentication` 元素：

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

为了检测单个用户帐户身份验证，连接服务将在 `packages.config` 文件中查找 package 元素。

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

为了检测旧式组织帐户身份验证，连接服务将在 `web.config` 文件中查找以下元素：

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

若要更改身份验证类型，请删除不兼容的身份验证类型，并尝试重新添加连接服务。

有关详细信息，请参阅 [Azure AD 的身份验证方案](authentication-scenarios.md)。
