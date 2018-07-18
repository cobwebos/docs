---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 24a5baade2168df603f5505baeaaf8e1c3ea2411
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2018
ms.locfileid: "36205221"
---
## <a name="setting-up-your-web-server-or-project"></a>设置 Web 服务器或项目

> 想要改为下载此示例的项目？ 
> - [下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/VisualStudio.zip)
>
> 或
> - [下载项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/core.zip)（对于本地 Web 服务器，例如 Python）
>
> 然后跳到[配置步骤](#register-your-application)，在执行前配置代码示例。

## <a name="prerequisites"></a>先决条件
运行此指导式设置需要本地 Web 服务器，如 [Python http.server](https://www.python.org/downloads/)、[http-server](https://www.npmjs.com/package/http-server/)、[.NET Core](https://www.microsoft.com/net/core) 或与 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 集成的 IIS Express。 

本指南中的说明是基于 Python 和 Visual Studio 2017 的，但可任意使用其他开发环境或 Web 服务器。

## <a name="create-your-project"></a>创建项目 

> ### <a name="option-1-visual-studio"></a>选项 1：Visual Studio 
> 如果正在使用 Visual Studio 创建新项目，请按照以下步骤创建新的 Visual Studio 解决方案：
> 1.    在 Visual Studio 中：`File` > `New` > `Project`
> 2.    在 `Visual C#\Web` 下选择 `ASP.NET Web Application (.NET Framework)`
> 3.    命名应用程序，并单击“确定”
> 4.    在 `New ASP.NET Web Application` 下选择 `Empty`

<p/><!-- -->

> ### <a name="option-2-python-other-web-servers"></a>选项 2：Python/其他 Web 服务器
> 请确保已安装 [Python](https://www.python.org/downloads/)，然后执行以下步骤：
> - 创建一个文件夹以托管应用程序。


## <a name="create-your-single-page-applications-ui"></a>创建单页应用程序的 UI
1.  为 JavaScript SPA 创建 *index.html* 文件。 如果使用的是 Visual Studio，请选择项目（项目根文件夹），右键单击并选择：`Add` > `New Item` > `HTML page`，然后将其命名为 index.html
2.  将以下代码添加到页面：
```html
<!DOCTYPE html>
<html>
<head>
    <!-- bootstrap reference used for styling the page -->
    <link href="//maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
    <title>JavaScript SPA Guided Setup</title>
</head>
<body style="margin: 40px">
    <button id="callGraphButton" type="button" class="btn btn-primary" onclick="callGraphApi()">Call Microsoft Graph API</button>
    <div id="errorMessage" class="text-danger"></div>
    <div class="hidden">
        <h3>Graph API Call Response</h3>
        <pre class="well" id="graphResponse"></pre>
    </div>
    <div class="hidden">
        <h3>Access Token</h3>
        <pre class="well" id="accessToken"></pre>
    </div>
    <div class="hidden">
        <h3>ID Token Claims</h3>
        <pre class="well" id="userInfo"></pre>
    </div>
    <button id="signOutButton" type="button" class="btn btn-primary hidden" onclick="signOut()">Sign out</button>

    <!-- This app uses cdn to reference msal.js (recommended). 
         You can also download it from: https://github.com/AzureAD/microsoft-authentication-library-for-js -->
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.1.3/js/msal.min.js"></script>

    <!-- The 'bluebird' and 'fetch' references below are required if you need to run this application on Internet Explorer -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/fetch/2.0.3/fetch.min.js"></script>

    <script type="text/javascript" src="msalconfig.js"></script>
    <script type="text/javascript" src="app.js"></script>
</body>
</html>
````
