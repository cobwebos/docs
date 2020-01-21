---
title: 在 MSAL Android 中使用共享设备模式 | Azure
description: 了解如何准备 Android 设备以在共享模式下运行，并运行一个一线工作人员应用。
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 401681b1704e36072da3854ef5b5f8e7c1e3f7b2
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76030753"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>教程：在 Android 应用程序中使用共享设备模式

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="developer-guide"></a>开发人员指南

本指南为开发人员提供了使用 Microsoft 身份验证库 (MSAL) 在 Android 应用程序中实现共享设备模式的指导。 请参阅 [MSAL Android 教程](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android)，了解如何将 MSAL 与 Android 应用集成、将用户登录、调用 Microsoft Graph 以及将用户注销。

### <a name="download-the-sample"></a>下载示例

从 GitHub 克隆[示例应用程序](https://github.com/Azure-Samples/ms-identity-android-java/)。 示例能够在[单帐户或多帐户模式](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account)下工作。

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>将 MSAL SDK 添加到本地 Maven 存储库

如果你使用的不是示例应用，请在 build.gradle 文件中将 MSAL 库添加为依赖项，如下所示：

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>将应用配置为使用共享设备模式

有关设置配置文件的详细信息，请参阅[配置文档](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)。

在 MSAL 配置文件中将 `"shared_device_mode_supported"` 设置为 `true`。

你可能不打算支持多帐户模式。 如果使用的不是共享设备，并且用户可以同时使用多个帐户登录到应用，则可能会出现这种情况。 如果是这样，请将 `"account_mode"` 设置为 `"SINGLE"`。 这可以确保你的应用始终获得 `ISingleAccountPublicClientApplication`，并大大简化你的 MSAL 集成。 `"account_mode"` 的默认值为 `"MULTIPLE"`，因此，如果使用 `"single account"` 模式，则必须在配置文件中更改此值。

下面是示例应用的 **app**>**main**>**res**>**raw** 目录中包括的 auth_config.json 文件的示例：

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>检测共享设备模式

使用共享设备模式，你可以将 Android 设备配置为由多名员工共享，同时为设备提供由 Microsoft Identity 支持的管理。 员工可以登录到其设备并快速访问客户信息。 当他们完成其班次或任务后，只需要单击一次即可注销共享设备上的所有应用，并且设备将立即准备就绪，可供下一位员工使用。

使用 `isSharedDevice()` 确定应用是否正在处于共享设备模式的设备上运行。 你的应用可以使用此标志来确定是否应当相应地修改 UX。

下面是一个代码片段，展示了可以如何使用 `isSharedDevice()`。  它来自示例应用中的 `SingleAccountModeFragment` 类：

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>初始化 PublicClientApplication 对象

如果在 MSAL 配置文件中设置了 `"account_mode":"SINGLE"`，则可以放心地将返回的应用程序对象转换为 `ISingleAccountPublicCLientApplication`。

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/ 
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config, 
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});  
```

### <a name="detect-single-vs-multiple-account-mode"></a>检测单帐户与多帐户模式

如果你正在编写的应用将仅用于共享设备上的一线工作人员，则建议你将应用编写为仅支持单帐户模式。 这包括以任务为中心的大多数应用程序，例如医疗记录应用、发票应用和大多数业务线应用。 这将简化开发，因为不需要包含 SDK 的许多功能。

如果你的应用支持多个帐户和共享设备模式，则必须执行类型检查并强制转换为相应的接口，如下所示。

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>获取已登录用户并确定该设备上的用户是否已更改

`loadAccount` 方法检索已登录用户的帐户。 `onAccountChanged` 方法确定已登录用户是否已更改，如果已更改，则清除：

```java 
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override 
    public void onError(@NonNull Exception exception) 
    {
    }
  }
}  
```

### <a name="globally-sign-in-a-user"></a>将用户全局登录

以下操作将设备中的用户登录到将 MSAL 与验证器应用结合使用的其他应用：

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>将用户全局注销

以下操作将删除已登录的帐户，并从应用中以及处于共享设备模式下的设备中同时清除缓存的令牌：

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>管理员指南

以下步骤介绍了如何在 Azure 门户中设置应用程序，并将设备置于共享设备模式。

### <a name="register-your-application-in-azure-active-directory"></a>在 Azure Active Directory 中注册应用程序

首先，在你的组织租户中注册应用程序。 然后，在 auth_config.json 中提供以下值，以便你的应用程序能够正常运行。

有关如何执行此操作的信息，请参阅[注册应用程序](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-android#register-your-application)。

> [!NOTE]
> 注册应用时，请使用左侧的快速入门指南，然后选择“Android”  。 这会出现一个页面，要求你为应用提供**包名称**和**签名哈希**。 这些值对于确保你的应用程序配置正常运行非常重要。 然后，你会收到一个配置对象，可将其用于你要剪切并粘贴到 auth_config.json 文件中的应用。

![应用注册屏幕](media/tutorial-v2-shared-device-mode/register-app.png) 你应该选择“为我进行此更改”  ，然后在 Azure 门户中提供快速入门索要的值。 完成此操作后，我们将生成你需要的所有配置文件。

![应用配置信息屏幕](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>设置租户

若要进行测试，请在租户中设置以下各项：至少两名员工、一名云设备管理员和一名全局管理员。 在 Azure 门户中，通过修改组织角色来设置云设备管理员。 在 Azure 门户中，通过选择“Azure Active Directory” > “角色和管理员” > “云设备管理员”来访问你的组织角色。    添加可将设备置于共享模式的用户。

## <a name="set-up-an-android-device-in-shared-mode"></a>在共享模式下设置 Android 设备

### <a name="download-the-authenticator-app"></a>下载验证器应用

从 Google Play 商店下载 Microsoft Authenticator 应用。 如果已安装该应用，请确保它是最新版本。

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>验证器应用设置以及在云中注册设备

启动验证器应用，并导航到主帐户页面。 看到“添加帐户”  页面后，便可将设备置于共享模式了。

![验证器“添加帐户”屏幕](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 使用右侧菜单栏转到“设置”  窗格。 在“工作和学校帐户”下选择“设备注册”。  
 
 ![验证器“添加帐户”屏幕](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 单击此按钮时，系统将要求你授权访问设备联系人。 这是由设备上的 Android 帐户集成导致的。 选择“允许”  。

 ![验证器“添加帐户”屏幕](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

云设备管理员应在“或注册为共享设备”  下输入其组织电子邮件。 然后，单击“注册为共享设备”  按钮并输入其凭据。

![“注册设备”屏幕](media/tutorial-v2-shared-device-mode/register-device.png)

![登录](media/tutorial-v2-shared-device-mode/sign-in.png)

设备现在已处于共享模式。

![“注册设备”屏幕](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 设备上的任何登录和注销都是全局性的，这意味着它们将应用于与设备上的 MSAL 和 Microsoft Authenticator 集成的所有应用。 你现在可以将应用程序部署到使用共享设备模式功能的设备了。

## <a name="view-the-shared-device-in-the-azure-portal"></a>在 Azure 门户中查看共享设备

将设备置于共享模式后，它会为你的组织所知，并可在你的组织租户中跟踪它。 可以通过在 Azure 门户的 Azure Active Directory 边栏选项卡中查看“联接类型”  来查看共享设备。

![Azure 门户中的“所有设备”边栏选项卡](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>运行示例应用

该示例应用程序是一个简单应用，它调用你的组织的图形 API。 首次运行时，系统将提示你表明是否同意，因为对于你的员工帐户而言，该应用程序是新的。

![应用配置信息屏幕](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>后续步骤

在 [Android 设备的共享设备模型](shared-device-mode.md)中详细了解共享模型