---
title: include 文件
description: include 文件
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 5ce0f18c1ec7a0fcb6465ab20e774976552687f1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133311"
---
## <a name="set-up-your-web-server-or-project"></a>设置 Web 服务器或项目

> 想要改为下载此示例的项目？ 执行下列操作之一：
> 
> - 若要使用本地 Web 服务器（如 Node.js）运行项目，请[下载项目文件](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)。
>
> - （可选）若要使用 IIS 服务器运行项目，请[下载 Visual Studio 项目](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)。
>
> 然后，若要在执行代码示例之前对其进行配置，请跳到[配置步骤](#register-your-application)。

## <a name="prerequisites"></a>先决条件

* 若要运行本教程，需要将本地 Web 服务器（如 [Node.js](https://nodejs.org/en/download/)、[.NET Core](https://www.microsoft.com/net/core) 或 IIS Express）与 [Visual Studio 2017](https://www.visualstudio.com/downloads/) 集成。

* 如果使用 Node.js 运行项目，请安装集成开发环境 (IDE)（如 [Visual Studio Code](https://code.visualstudio.com/download)）以编辑项目文件。

* 本指南中的说明基于 Node.js 和 Visual Studio 2017，但你可以使用任何其他开发环境或 Web 服务器。

## <a name="create-your-project"></a>创建项目

> ### <a name="option-1-nodejs-or-other-web-servers"></a>选项 1：Node.js 或其他 Web 服务器
> 请确保已安装 [Node.js](https://nodejs.org/en/download/)，然后执行以下步骤：
> - 创建一个文件夹以托管应用程序。
>
> ### <a name="option-2-visual-studio"></a>选项 2：Visual Studio
> 如果使用的是 Visual Studio 并要创建新项目，请执行以下操作：
> 1. 在 Visual Studio 中，选择“文件”   > “新建”   > “项目”  。
> 1. 在 Visual C#\Web 下，选择“ASP.NET Web 应用程序(.NET Framework)”   。
> 1. 输入应用程序的名称并选择“确定”  。
> 1. 在“新建 ASP.NET Web 应用程序”  下，选择“空”  。

## <a name="create-the-spa-ui"></a>创建 SPA UI
1. 为 JavaScript SPA 创建 *index.html* 文件。 如果使用的是 Visual Studio，请选择项目（项目根文件夹），右键单击并选择“添加”   > “新项”   > “HTML 页”  ，然后将文件命名为 index.html  。

1. 在 *index.html* 文件中，添加以下代码：

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > 可以将上述脚本中的 MSAL.js 版本替换为 [MSAL.js 版本](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)下的最新发布版本。
