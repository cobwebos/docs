---
title: "Azure AD Cordova 入门 | Microsoft Docs"
description: "如何生成一个与 Azure AD 集成以方便登录，并使用 OAuth 调用 Azure AD 保护 API 的 Cordova 应用程序。"
services: active-directory
documentationcenter: 
author: vibronet
manager: mbaldwin
editor: 
ms.assetid: b1a8d7bd-7ad6-44d5-8ccb-5255bb623345
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: vittorib
ms.custom: aaddev
ms.openlocfilehash: d9f53148787729d29a0a89cce1b8b2b83ba228f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-with-an-apache-cordova-app"></a>将 Azure AD Apache Cordova 应用程序集成
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

可使用 Apache Cordova 开发能够在移动设备上运行的完全成熟的 HTML5/JavaScript 本机应用程序。 使用 Azure Active Directory (Azure AD)，可以向 Cordova 应用程序添加企业级身份验证功能。

Cordova 插件在 iOS、Android、Windows 应用商店和 Windows Phone 上包装 Azure AD 本机 SDK。 通过该插件，可以增强应用程序，以支持使用用户的 Windows Server Active Directory 帐户登录、获取访问 Office 365 和 Azure API 的权限，甚至帮助保护对自己自定义的 Web API 的调用。

在本教程中，我们将使用适用于 Active Directory 身份验证库 (ADAL) 的 Apache Cordova 插件，通过添加以下功能改进简单应用程序：

* 只需几行代码，即可对用户进行身份验证并获取令牌。
* 然后，使用该令牌调用图形 API 以查询目录并显示结果。  
* 使用 ADAL 令牌缓存，最大程度地减少对用户显示身份验证提示。

若要进行这些改进，需要：

1. 将应用程序注册到 Azure AD。
2. 将代码添加到应用以请求令牌。
3. 添加代码以使用该令牌查询 Graph API 并显示结果。
4. 使用所需的所有目标平台创建 Cordova 部署项目，添加 Cordova ADAL 插件，并在模拟器中测试解决方案。

## <a name="prerequisites"></a>先决条件
要完成本教程，需要：

* 一个 Azure AD 租户，其中有一个具有应用开发权限的帐户。
* 一个配置为使用 Apache Cordova 的开发环境。  

如果已设置了这两项，则直接转到步骤 1。

如果没有 Azure AD 租户，可使用[有关如何获取租户的说明](active-directory-howto-tenant.md)。

如果没有在计算机上设置 Apache Cordova，请安装以下组件：

* [Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Node.js](https://nodejs.org/download/)
* [Cordova CLI](https://cordova.apache.org/)（可以通过 NPM 包管理器轻松安装：`npm install -g cordova`）

之前的安装应在电脑和 Mac 上均可运行。

每个目标平台都有不同的先决条件：

* 生成并运行用于 Windows 平板电脑/电脑或 Windows Phone 的应用：
  * 安装 [Visual Studio 2013 for Windows 更新 2 或更高版本](http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-windows-8)（Express 或其他版本）或 [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs#d-community)。

* 生成并运行用于 iOS 的应用：

  * 安装 Xcode 6.x 或更高版本。 从 [Apple 开发人员站点](http://developer.apple.com/downloads)或 [Mac 应用商店](http://itunes.apple.com/us/app/xcode/id497799835?mt=12)进行下载。
  * 安装 [ios-sim](https://www.npmjs.org/package/ios-sim)。 可将其用于从命令行启动 iOS 模拟器中的 iOS 应用。 （可通过该终端轻松安装它：`npm install -g ios-sim`。）
* 生成并运行用于 Android 的应用：

  * 安装 [Java 开发工具包 (JDK) 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) 或更高版本。 请确保根据 JDK 安装路径（例如 C:\Program Files\Java\jdk1.7.0_75）正确设置 `JAVA_HOME`（环境变量）。
  * 安装 [Android SDK](http://developer.android.com/sdk/installing/index.html?pkg=tools) 并将 `<android-sdk-location>\tools` 位置（例如 C:\tools\Android\android-sdk\tools）添加到 `PATH` 环境变量。
  * 打开 Android SDK 管理器（例如，通过终端：`android`）并安装：
    * *Android 5.0.1 (API 21)* 平台 SDK
    * *Android SDK 生成工具* 19.1.0 版或更高版本
    * *Android 支持存储库* (Extras)

  Android SDK 不提供任何默认模拟器实例。 如果要在仿真器上运行 Android 应用，请从终端运行 `android avd`，然后选择“创建”以创建实例。 建议使用 API 级别 19 或更高级别。 有关 Android 仿真器和创建选项的详细信息，请参阅 Android 站点上的 [AVD Manager](http://developer.android.com/tools/help/avd-manager.html)。

## <a name="step-1-register-an-application-with-azure-ad"></a>步骤 1：向 Azure AD 注册应用程序
此步骤是可选的。 此教程提供了预设置的值，使你无需在自己的租户中进行任何预配，就可使用该值查看示例的运行情况。 但我们建议你执行此步骤并熟悉相关的过程，因为在你创建自己的应用程序时，将需要执行该过程。

Azure AD 仅向已知应用程序颁发令牌。 在从应用程序使用 Azure AD 之前，需要在租户中为该应用程序创建一个条目。 在租户中注册新应用程序：

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶部栏上，单击帐户。 在“目录”列表中，选择要在其中注册应用程序的 Azure AD 租户。
3. 在左窗格中，单击“更多服务”，然后选择“Azure Active Directory”。
4. 单击“应用注册”，然后选择“添加”。
5. 根据提示创建“本机客户端应用程序”。 （尽管 Cordova 应用以 HTML 为基础，但我们要在此处创建本机客户端应用程序。 必须选择“本机客户端应用程序”选项，否则应用程序将无法运行。）
  * **名称**向用户描述应用程序。
  * “重定向 URI”是用于将令牌返回应用的 URI。 输入 **http://MyDirectorySearcherApp**。

完成注册后，Azure AD 将向应用分配唯一应用程序 ID。 在以下各节中将需要此值。 可以在新建应用的应用程序选项卡上找到它。

若要运行 `DirSearchClient Sample`，请向新建应用授予查询 Azure AD 图形 API 的权限：

1. 在“设置”页上，选择“所需权限”，然后选择“添加”。  
2. 对于 Azure Active Directory 应用程序，选择“Microsoft Graph”作为 API，并在“委派权限”下添加“以已登录用户的身份访问目录”权限。  这样，应用程序便可以在图形 API 中查询用户。

## <a name="step-2-clone-the-sample-app-repository"></a>步骤 2：克隆示例应用存储库
在 shell 或命令行中键入以下命令：

    git clone -b skeleton https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova.git

## <a name="step-3-create-the-cordova-app"></a>步骤 3：创建 Cordova 应用
可通过多种方式创建 Cordova 应用程序。 在本教程中，我们将使用 Cordova 命令行接口 (CLI)。

1. 在 shell 或命令行中键入以下命令：

        cordova create DirSearchClient

   该命令将为 Cordova 项目创建文件夹结构和基架。

2. 切换到新的 DirSearchClient 文件夹：

        cd .\DirSearchClient

3. 使用文件管理器或 shell 中的以下命令在 www 子文件夹中复制初学者项目的内容：

  * Windows：`xcopy ..\NativeClient-MultiTarget-Cordova\DirSearchClient www /E /Y`
  * Mac：`cp -r  ../NativeClient-MultiTarget-Cordova/DirSearchClient/* www`

4. 添加允许列表插件。 这是调用图形 API 必需的。

        cordova plugin add cordova-plugin-whitelist

5. 添加要支持的所有平台。 若要获得一个有效示例，至少需要执行以下命令之一。 请注意，无法在 Windows 上模拟 iOS，或者在 Mac 上模拟 Windows。

        cordova platform add android
        cordova platform add ios
        cordova platform add windows

6. 将 ADAL for Cordova 插件添加到项目：

        cordova plugin add cordova-plugin-ms-adal

## <a name="step-4-add-code-to-authenticate-users-and-obtain-tokens-from-azure-ad"></a>步骤 4：添加代码以便对用户进行身份验证并从 Azure AD 获取令牌
本教程中开发的应用程序将提供简单的目录搜索功能。 然后，用户可在目录中键入任何用户的别名，并可视化一些基本属性。 初学者项目包含应用程序基本用户界面的定义（在 www/index.html 中），以及用于串联基本应用程序事件周期、用户界面绑定和结果显示逻辑的基架（在 www/js/index.js 中）。 你唯一要做的事情就是添加实现标识任务的逻辑。

要在代码中做的第一件事是，引入 Azure AD 用来识别目标应用和资源的协议值。 稍后将要使用这些值来构造令牌请求。 在 index.js 文件的顶部插入以下代码片段：

```javascript
var authority = "https://login.microsoftonline.com/common",
    redirectUri = "http://MyDirectorySearcherApp",
    resourceUri = "https://graph.windows.net",
    clientId = "a5d92493-ae5a-4a9f-bcbf-9f1d354067d3",
    graphApiVersion = "2013-11-08";
```

`redirectUri` 和 `clientId` 值应与 Azure AD 中用于描述应用的值匹配。 如本教程前面步骤 1 中所述，可在 Azure 门户的“配置”选项卡中找到这些值。

> [!NOTE]
> 如果选择不在自己的租户中注册新的应用，只需按原样粘贴预配置的值。 然后可以看到示例运行，但是应始终为应用创建用于生产的你自己的条目。

接下来，添加令牌请求代码。 在 `search`和 `renderData` 定义之间插入以下代码片段：

```javascript
    // Shows the user authentication dialog box if required
    authenticate: function (authCompletedCallback) {

        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
        });

    },
```
让我们通过将它分解成两个主要部分来检查运行情况。
此示例设计用于任何租户，而不只是与某个特定租户相关。 它使用了“/common”终结点，这允许用户在身份验证时可以输入任何帐户，并可将请求定向到它所属的租户。

该方法的第一部分将检查 ADAL 缓存，查看是否已存储令牌。 如果已存储，该方法将使用令牌所属租户重新初始化 ADAL。 要避免额外的提示，必须执行此操作，因为使用“/common”总会导致要求用户输入新帐户。

```javascript
        app.context = new Microsoft.ADAL.AuthenticationContext(authority);
        app.context.tokenCache.readItems().then(function (items) {
            if (items.length > 0) {
                authority = items[0].authority;
                app.context = new Microsoft.ADAL.AuthenticationContext(authority);
            }
```
该方法的第二部分将执行适当的令牌请求。 `acquireTokenSilentAsync` 方法请求 ADAL 返回指定资源的令牌，且不显示任何 UX。 如果缓存中已经存储了一个适当的访问令牌，或者某个刷新令牌可用于获取新访问令牌且不显示任何提示，则可能会发生这种情况。 如果该尝试失败，我们将在 `acquireTokenAsync` 上回退 - 这会以可视方式提示用户进行身份验证。

```javascript
            // Attempt to authorize the user silently
            app.context.acquireTokenSilentAsync(resourceUri, clientId)
            .then(authCompletedCallback, function () {
                // We require user credentials, so this triggers the authentication dialog box
                app.context.acquireTokenAsync(resourceUri, clientId, redirectUri)
                .then(authCompletedCallback, function (err) {
                    app.error("Failed to authenticate: " + err);
                });
            });
```
现在，我们已经获得了令牌，最后，我们可以调用图形 API 并执行所需的搜索查询。 在 `authenticate` 定义的下方插入以下代码片段：

```javascript
// Makes an API call to receive the user list
    requestData: function (authResult, searchText) {
        var req = new XMLHttpRequest();
        var url = resourceUri + "/" + authResult.tenantId + "/users?api-version=" + graphApiVersion;
        url = searchText ? url + "&$filter=mailNickname eq '" + searchText + "'" : url + "&$top=10";

        req.open("GET", url, true);
        req.setRequestHeader('Authorization', 'Bearer ' + authResult.accessToken);

        req.onload = function(e) {
            if (e.target.status >= 200 && e.target.status < 300) {
                app.renderData(JSON.parse(e.target.response));
                return;
            }
            app.error('Data request failed: ' + e.target.response);
        };
        req.onerror = function(e) {
            app.error('Data request failed: ' + e.error);
        }

        req.send();
    },

```
起点文件提供了一个简单的 UX 用于在文本框中输入用户的别名。 此方法使用该值来构造查询，将该查询与访问令牌相结合，然后将其发送到 Microsoft Graph 并分析结果。 起点文件中已提供了一个负责可视化结果的 `renderData` 方法。

## <a name="step-5-run-the-app"></a>步骤 5：运行应用
应用终于准备好运行了。 操作很简单：应用启动时，输入要查找的用户的别名，然后单击按钮。 系统将提示进行身份验证。 身份验证和搜索成功后，将显示搜索到的用户的属性。

后续运行将执行搜索而不显示任何提示，这是因为缓存中存在以前获取的令牌。

运行应用程序的具体步骤因平台而异。

### <a name="windows-10"></a>Windows 10
   平板电脑/电脑：`cordova run windows --archs=x64 -- --appx=uap`

   移动版（需要连接到电脑的 Windows10 移动设备）：`cordova run windows --archs=arm -- --appx=uap --phone`

   > [!NOTE]
   > 首次运行期间，系统可能会要求登录以获得开发人员许可证。 有关详细信息，请参阅[开发人员许可证](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)。

### <a name="windows-81-tabletpc"></a>Windows 8.1 平板电脑/电脑
   `cordova run windows`

   > [!NOTE]
   > 首次运行期间，系统可能会要求登录以获得开发人员许可证。 有关详细信息，请参阅[开发人员许可证](https://msdn.microsoft.com/library/windows/apps/hh974578.aspx)。

### <a name="windows-phone-81"></a>Windows Phone 8.1
   在连接的设备上运行：`cordova run windows --device -- --phone`

   在默认的模拟器上运行：`cordova emulate windows -- --phone`

   使用 `cordova run windows --list -- --phone` 查看所有可用目标，使用 `cordova run windows --target=<target_name> -- --phone` 在特定的设备或模拟器（例如 `cordova run windows --target="Emulator 8.1 720P 4.7 inch" -- --phone`）上运行应用程序。

### <a name="android"></a>Android
   在连接的设备上运行：`cordova run android --device`

   在默认的模拟器上运行：`cordova emulate android`

   确保已根据前面的“先决条件”部分所述，使用 AVD Manager 创建模拟器实例。

   使用 `cordova run android --list` 查看所有可用目标，使用 `cordova run android --target=<target_name>` 在特定的设备或模拟器（例如 `cordova run android --target="Nexus4_emulator"`）上运行应用程序。

### <a name="ios"></a>iOS
   在连接的设备上运行：`cordova run ios --device`

   在默认的模拟器上运行：`cordova emulate ios`

   > [!NOTE]
   > 确保已安装 `ios-sim` 包，使其在模拟器上运行。 有关详细信息，请参阅“先决条件”部分。

    Use `cordova run ios --list` to see all available targets and `cordova run ios --target=<target_name>` to run the application on specific device or emulator (for example, `cordova run android --target="iPhone-6"`).

    Use `cordova run --help` to see additional build and run options.

## <a name="next-steps"></a>后续步骤
[GitHub](https://github.com/AzureADQuickStarts/NativeClient-MultiTarget-Cordova/tree/complete/DirSearchClient) 中提供了已完成示例（无配置值）以供参考。

现在，让我们来了解更高级（也更有趣）的方案。 可能需要：[使用 Azure AD 保护 Node.js Web API](active-directory-devquickstarts-webapi-nodejs.md)。

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
