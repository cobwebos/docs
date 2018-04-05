---
title: 如何使用 Azure Active Directory 连接服务诊断错误
description: Active Directory 连接服务可检测到不兼容的身份验证类型
services: active-directory
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev
ms.openlocfilehash: c3494102703b68d6861e8755ca9dafe19c7fa6d6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2018
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>使用 Azure Active Directory 连接服务诊断错误

检测以前的身份验证代码时，Azure Active Directory 连接服务器检测到不兼容的身份验证类型。

若要正确检测某个项目中以前的身份验证代码，必须生成该项目。  如果遇到此错误，并且项目中不存在以前的身份验证代码，请重新生成项目并重试。

## <a name="project-types"></a>项目类型

连接服务会检查你正在开发的项目类型，以便可以将正确的身份验证逻辑注入到项目。 如果项目中有控制器派生自 `ApiController`，则该项目会被视为 WebAPI 项目。 如果项目中的控制器均派生自 `MVC.Controller`，则项目会被视为 MVC 项目。 连接服务不支持任何其他项目类型。

## <a name="compatible-authentication-code"></a>兼容的身份验证代码

连接服务还会检查是否存在以前配置的身份验证设置或与该服务兼容的身份验证设置。 如果所有这些设置都存在，则将视为可重入情况，连接服务将打开并显示这些设置。  如果只存在某些设置，则将视为错误情况。

在 MVC 项目中，连接服务会检查是否存在以下任何设置（这些设置是以前使用该服务生成的）：

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

此外，连接服务还会在 Web API 项目中检查是否存在以下任何设置（这些设置是以前使用该服务生成的）：

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
        <span style="background-color: yellow"><authentication mode="Windows" /></span>
    </system.web>
</configuration>
```

为了检测 Web API 项目中的 Windows 身份验证，连接服务将在项目的 `.csproj` 文件中查找 `IISExpressWindowsAuthentication` 元素：

```xml
<Project>
    <PropertyGroup>
        <span style="background-color: yellow"><IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication></span>
    </PropertyGroup>
</Project>
```

为了检测单个用户帐户身份验证，连接服务将在 `packages.config` 文件中查找 package 元素。

```xml
<packages>
    <span style="background-color: yellow"><package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" /></span>
</packages>
```

为了检测旧式组织帐户身份验证，连接服务将在 `web.config` 文件中查找以下元素：

```xml
<configuration>
    <appSettings>
        <span style="background-color: yellow"><add key="ida:Realm" value="***" /></span>
    </appSettings>
</configuration>
```

若要更改身份验证类型，请删除不兼容的身份验证类型，并尝试重新添加连接服务。

有关详细信息，请参阅 [Azure AD 的身份验证方案](active-directory-authentication-scenarios.md)。