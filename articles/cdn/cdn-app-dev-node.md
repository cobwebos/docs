---
title: "用于 Node.js 的 Azure CDN SDK 入门 | Microsoft Docs"
description: "了解如何编写 Node.js 应用程序以管理 Azure CDN。"
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: 
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 46ae8cd9775432d126cbde856c1fb06ea319297e
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN 开发入门
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

可以使用[适用于 Node.js 的 Azure CDN SDK](https://www.npmjs.com/package/azure-arm-cdn) 来自动创建和管理 CDN 配置文件和终结点。  本教程介绍一个简单的 Node.js 控制台应用程序的创建示例，演示几个可用的操作。  本教程不打算详细描述适用于 Node.js 的 Azure CDN SDK 的所有方面。

要完成本教程，应已安装并配置了 [Node.js](http://www.nodejs.org)**4.x.x** 或更高版本。  可以使用任何需要的文本编辑器创建 Node.js 应用程序。  为了编写本教程，我使用了 [Visual Studio Code](https://code.visualstudio.com)。  

> [!TIP]
> 可在 MSDN 上下载[本教程中已完成的项目](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)。
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>创建项目并添加 NPM 依赖项
现在我们已经为 CDN 配置文件创建了一个资源组，并已将管理该组中的 CDN 配置文件和终结点的权限授予 Azure AD 应用程序，我们可以开始创建我们的应用程序。

创建一个文件夹以存储应用程序。  在当前路径中带有 Node.js 工具的控制台中，将当前位置设置为此新文件夹，并通过执行以下命令来初始化项目：

    npm init

然后，会看到一系列问题，用于初始化项目。  对于**入口点**，本教程使用 *app.js*。  可以在下面的例子中看到我的其他选择。

![NPM init 输出](./media/cdn-app-dev-node/cdn-npm-init.png)

我们的项目现在使用 *packages.json* 文件进行初始化。  我们的项目将使用 NPM 包中包含的部分 Azure 库。  我们将使用适用于 Node.js 的 Azure 客户端运行时 (ms-rest-azure) 和适用于 Node.js 的 Azure CDN 客户端库 (azure-arm-cd)。  让我们将它们作为依赖项添加到项目。

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

当包安装完成后，*package.json* 文件应与此示例类似（版本号可能不同）：

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

最后，使用文本编辑器创建一个空白文本文件，并在项目文件夹的根目录中将其保存为 *app.js*。  我们已经准备好编写代码。

## <a name="requires-constants-authentication-and-structure"></a>要求、常量、身份验证和结构
在编辑器中打开 *app.js* 后，让我们来编写程序的基本结构。

1. 在顶部用以下指令为我们的 NPM 包添加“require”：
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. 我们需要定义我们的方法将使用的一些常量。  添加以下内容。  请务必根据需要使用值替换占位符，包括**&lt;尖括号&gt;**。
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. 接下来，我们将实例化 CDN 管理客户端并为其提供凭据。
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    如果使用的是个人用户身份验证，这两行看起来会稍有不同。
   
   > [!IMPORTANT]
   > 仅选择个人用户身份验证而不是服务主体身份验证时，才使用此代码示例。  小心保护个人用户凭据并将其保密。
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    请务必使用正确的信息替换**&lt;尖括号&gt;**中的项目。  对于 `<redirect URI>`，请使用在 Azure AD 中注册应用程序时输入的重定向 URI。
4. 我们的 Node.js 控制台应用程序会采用一些命令行参数。  让我们验证是否至少传递了一个参数。
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. 这使我们进入程序的主要部分，在那里我们会基于传递的参数转到其他函数分支。
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. 在程序的数个位置中，我们需要确保传入了正确数量的参数，如果它们看起来不正确，系统会显示一些帮助。  让我们创建函数以执行该操作。
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. 最后，我们会在 CDN 管理客户端上使用的函数是异步的，因此它们完成后需要一个回调方法。  让我们创建一个可以显示 CDN 管理客户端中输出（如有）并且能正常退出程序的函数。
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

既然已经编写了程序的基本结构，我们现在应该创建基于我们的参数调用的函数。

## <a name="list-cdn-profiles-and-endpoints"></a>列出 CDN 配置文件和终结点
让我们先从可列出现有配置文件和终结点的代码开始。  我的代码注释提供了预期的语法，以便我们了解每个参数的动向。

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>创建 CDN 配置文件和终结点
接下来，我们将编写用于创建配置文件和终结点的函数。

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>清除终结点
假设终结点已经创建，我们可能想在我们的程序中执行的一个常见任务是清除终结点中的内容。

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>删除 CDN 配置文件和终结点
我们将包括的最后一个函数将删除终结点和配置文件。

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>运行程序
现在可以使用我们最喜爱的调试器，或在控制台执行我们的 Node.js 程序。

> [!TIP]
> 如果使用 Visual Studio Code 作为调试器，则需要设置环境以传递命令行参数。  Visual Studio Code 在 **lanuch.json** 文件中执行此操作。  寻找名为 **args** 的属性，并为参数添加一个字符串值数组，以使其看起来类似于：`"args": ["list", "profiles"]`。
> 
> 

让我们首先列出我们的配置文件。

![列出配置文件](./media/cdn-app-dev-node/cdn-list-profiles.png)

返回了一个空数组。  因为我们的资源组中没有任何配置文件，所以这是预期的情况。  现在让我们创建一个配置文件。

![创建配置文件](./media/cdn-app-dev-node/cdn-create-profile.png)

现在，让我们添加一个终结点。

![创建终结点](./media/cdn-app-dev-node/cdn-create-endpoint.png)

最后，让我们删除配置文件。

![删除配置文件](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>后续步骤
要从本演练中查看完成的项目，请[下载示例](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74)。

要查看有关适用于 Node.js 的 Azure CDN SDK 的参考，请查看[引用](http://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/)。

要查找有关适用于 Node.js 的 Azure CDN SDK 的其他文档，请查看[完整引用](http://azure.github.io/azure-sdk-for-node/)。

使用 [PowerShell](cdn-manage-powershell.md) 管理 CDN 资源。

