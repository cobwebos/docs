---
title: "Xamarin Android 中的移动应用身份验证入门"
description: "了解如何使用移动应用通过各种标识提供程序（包括 AAD、Google、Facebook、Twitter 和 Microsoft）对 Xamarin Android 应用的用户进行身份验证。"
services: app-service\mobile
documentationcenter: xamarin
author: adrianhall
manager: erikre
editor: 
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: adrianha
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4c0ba82ca010f1ee571424fa3d650718e5acdd8b
ms.lasthandoff: 11/17/2016


---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>向 Xamarin.Android 应用添加身份验证
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

本主题演示如何从客户端应用程序对移动应用的用户进行身份验证。 在本教程中，使用 Azure 移动应用支持的标识提供者向快速入门项目添加身份验证。 在移动应用中成功进行身份验证和授权后，显示用户 ID 值。

本教程基于移动应用快速入门。 还必须先完成教程[创建 Xamarin.Android 应用]。 如果不使用下载的快速入门服务器项目，必须将身份验证扩展包添加到项目。 有关服务器扩展包的详细信息，请参阅[使用适用于 Azure 移动应用的 .NET 后端服务器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-app-services"></a><a name="register"></a>注册应用以进行身份验证并配置应用服务
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>将权限限制给已经过身份验证的用户
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Visual Studio 或 Xamarin Studio 中，运行设备或模拟器中的客户端项目。 验证在应用程序启动后是否引发状态代码为 401（“未授权”）的未处理异常。 发生此异常的原因是应用尝试以未经身份验证的用户身份访问移动应用后端。 *TodoItem* 表现在要求身份验证。

接下来，更新客户端应用，以使用经过身份验证的用户从移动应用后端请求资源。

## <a name="a-nameadd-authenticationaadd-authentication-to-the-app"></a><a name="add-authentication"></a>向应用程序添加身份验证
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
                        MobileServiceAuthenticationProvider.Facebook);
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
5. 在 Visual Studio 或 Xamarin Studio 中，运行设备或模拟器中的客户端项目，并使用所选的标识提供者登录。
   
       When you are successfully logged-in, the app will display your login ID and the list of todo items, and you can make updates to the data.

<!-- URLs. -->
[创建 Xamarin.Android 应用]: app-service-mobile-xamarin-android-get-started.md

