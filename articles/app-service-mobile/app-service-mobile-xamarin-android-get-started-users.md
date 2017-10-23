---
title: "Xamarin Android 中的移动应用身份验证入门"
description: "了解如何使用移动应用通过各种标识提供程序（包括 AAD、Google、Facebook、Twitter 和 Microsoft）对 Xamarin Android 应用的用户进行身份验证。"
services: app-service\mobile
documentationcenter: xamarin
author: ggailey777
manager: panarasi
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 8f9a1109018c708d52cdcb7b8bce43861cecd31c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>向 Xamarin.Android 应用添加身份验证
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主题演示如何从客户端应用程序对移动应用的用户进行身份验证。 在本教程中，使用 Azure 移动应用支持的标识提供者向快速入门项目添加身份验证。 在移动应用中成功进行身份验证和授权后，显示用户 ID 值。

本教程基于移动应用快速入门。 还必须先完成教程[创建 Xamarin.Android 应用]。 如果不使用下载的快速入门服务器项目，必须将身份验证扩展包添加到项目。 有关服务器扩展包的详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>将应用添加到允许的外部重定向 URL

安全身份验证要求为应用定义新的 URL 方案。 这允许身份验证系统在身份验证过程完成后，重定向回应用。 在本教程中，我们将通篇使用 URL 方案 _appname_。 但是，可以使用所选择的任何 URL 方案。 对于移动应用程序而言，它应是唯一的。 在服务器端启用重定向：

1. 在 [Azure 门户]中，选择应用服务。

2. 单击“身份验证/授权”菜单选项。

3. 在“允许的外部重定向 URL”中，输入 `url_scheme_of_your_app://easyauth.callback`。  此字符串中的 url_scheme_of_your_app 是移动应用程序的 URL 方案。  它应该遵循协议的正常 URL 规范（仅使用字母和数字，并以字母开头）。  请记下所选的字符串，你将需要在几个地方使用 URL 方案调整移动应用程序代码。

4. 单击 **“确定”**。

5. 单击“保存” 。

## <a name="permissions"></a>将权限限制为已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Visual Studio 或 Xamarin Studio 中，运行设备或模拟器中的客户端项目。 验证在应用程序启动后是否引发状态代码为 401（“未授权”）的未处理异常。 发生此异常的原因是应用尝试以未经身份验证的用户身份访问移动应用后端。 *TodoItem* 表现在要求身份验证。

接下来，更新客户端应用，以使用经过身份验证的用户从移动应用后端请求资源。

## <a name="add-authentication"></a>向应用添加身份验证
已更新应用，在显示数据之前要求用户点击“登录”按钮进行身份验证。

1. 将以下代码添加到 **TodoActivity** 类：
   
        // Define a authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    此代码创建一个对用户进行身份验证的新方法和一个针对新“登录”按钮的方法处理程序。 上面示例代码中的用户使用 Facebook 登录进行身份验证。 对话框用于在进行身份验证后显示用户 ID。
   
   > [!NOTE]
   > 如果使用的标识提供程序不是 Facebook，请将传递给上述 **LoginAsync** 方法的值更改为下列其中一项：*MicrosoftAccount*、*Twitter*、*Google* 或 *WindowsAzureActiveDirectory*。
   > 
   > 
2. 在 **OnCreate** 方法中，删除或注释掉以下代码行：
   
        OnRefreshItemsSelected ();
3. 在 Activity_To_Do.axml 文件中，在现有 *AddItem* 按钮之前添加以下 *LoginUser* 按钮定义：
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. 将以下元素添加到 Strings.xml 资源文件：
   
        <string name="login_button_text">Sign in</string>
5. 打开 AndroidManifest.xml 文件，并在 `<application>` XML 元素中添加以下代码：

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. 在 Visual Studio 或 Xamarin Studio 中，运行设备或模拟器中的客户端项目，并使用所选的标识提供者登录。 成功登录后，应用会显示登录 ID 和待办事项列表，用户可以对数据进行更新。

<!-- URLs. -->
[创建 Xamarin.Android 应用]: app-service-mobile-xamarin-android-get-started.md