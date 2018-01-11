---
title: "通过 Azure 通知中心发送安全推送通知"
description: "了解如何从 Azure 将安全推送通知发送到 Android 应用。 用 Java 和 C# 编写的代码示例。"
documentationcenter: android
keywords: "推送通知、推送通知、推送消息、android 推送通知"
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 29f8c516e611c13fb73c7edc15e7c52708c75bb0
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/21/2017
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>通过 Azure 通知中心发送安全推送通知
> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>概述
> [!IMPORTANT]
> 要完成本教程，必须有一个有效的 Azure 帐户。 如果没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)。
> 
> 

利用 Microsoft Azure 中的推送通知支持，可访问易于使用且横向扩展的多平台推送消息基础结构，这大大简化了为移动平台的使用者应用程序和企业应用程序实现推送通知的过程。

由于法规或安全约束，有时应用程序可能想要在通知中包含某些无法通过标准推送通知基础结构传输的内容。 本教程介绍如何通过客户端 Android 设备和应用程序后端之间的安全且经过身份验证的连接发送敏感信息来获得相同的体验。

在高级别中，此流程如下所示：

1. 应用后端：
   * 在后端数据库中存储安全有效负载。
   * 将此通知的 ID 发送到 Android 设备（不发送任何安全信息）。
2. 此设备上的应用在接收通知时：
   * 此 Android 设备将联系请求安全有效负载的后端。
   * 此应用可以将有效负载显示为设备上的通知。

请务必注意，在之前的流程（以及本教程中）中，我们假设此设备会在用户登录后在本地存储中存储身份验证令牌。 这可以保证完全无缝的体验，因为该设备可以使用此令牌检索通知的安全有效负载。 如果应用程序未在设备上存储身份验证令牌，或者如果这些令牌可能已过期，那么此设备应用程序在收到推送通知时应显示提示用户启动此应用程序的一般性通知。 然后应用会对用户进行身份验证，并显示通知有效负载。

本教程介绍如何发送安全推送通知。 本教程以[通知用户](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md)教程为基础，因此，应该先完成“通知用户”教程中的步骤（若未完成）。

> [!NOTE]
> 本教程假设已按照[通知中心入门 (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md) 中所述创建并配置了通知中心。
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>修改 Android 项目
现在，已将应用程序后端修改为只发送推送通知的 *ID*，因此，必须更改 Android 应用程序以处理此通知，并回调后端以检索要显示的安全消息。
要实现此目标，必须确保 Android 应用在收到推送通知时知道如何使用后端对自身进行身份验证。

现在我们将修改*登录*流程，以在应用的共享首选项中保存身份验证标头值。 可以使用类似机制来存储应用将需要使用的任何身份验证令牌（例如 OAuth 令牌），从而无需用户凭据。

1. 在 Android 应用项目中，在 **MainActivity** 类的顶部添加以下常量：
   
        public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
        public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
2. 仍在 **MainActivity** 类中，更新 `getAuthorizationHeader()` 方法以包含以下代码：
   
        private String getAuthorizationHeader() throws UnsupportedEncodingException {
            EditText username = (EditText) findViewById(R.id.usernameText);
            EditText password = (EditText) findViewById(R.id.passwordText);
            String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
            basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);
   
            SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();
   
            return basicAuthHeader;
        }
3. 在 **MainActivity** 文件的顶部添加以下 `import` 语句：
   
        import android.content.SharedPreferences;

现在我们将更改收到通知时调用的处理程序。

1. 在 **MyHandler** 类中，更改 `OnReceive()` 方法以包含：
   
        public void onReceive(Context context, Bundle bundle) {
            ctx = context;
            String secureMessageId = bundle.getString("secureId");
            retrieveNotification(secureMessageId);
        }
2. 然后添加 `retrieveNotification()` 方法，并将占位符 `{back-end endpoint}` 替换为部署后端时获取的后端终结点：
   
        private void retrieveNotification(final String secureMessageId) {
            SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
            final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);
   
            new AsyncTask<Object, Object, Object>() {
                @Override
                protected Object doInBackground(Object... params) {
                    try {
                        HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                        request.addHeader("Authorization", "Basic "+authorizationHeader);
                        HttpResponse response = new DefaultHttpClient().execute(request);
                        if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                            Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                            throw new RuntimeException("Error retrieving secure notification");
                        }
                        String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                        JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                        sendNotification(secureNotification.getString("Payload"));
                    } catch (Exception e) {
                        Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                        return e;
                    }
                    return null;
                }
            }.execute(null, null, null);
        }

此方法使用存储在共享首选项中的凭据调用应用后端来检索通知内容，并将它显示为普通通知。 通知呈现给应用用户的外观与任何其他推送通知完全相同。

请注意，最好由后端处理缺失身份验证标头属性或拒绝的情况。 这些情况下的特定处理主要取决于目标用户的体验。 一种选择是显示包含用户用来进行身份验证的通用提示的通知，从而检索实际通知。

## <a name="run-the-application"></a>运行应用程序
若要运行应用程序，请执行以下操作：

1. 确保 **AppBackend** 已部署在 Azure 中。 如果使用 Visual Studio，则运行 **AppBackend** Web API 应用程序。 将显示 ASP.NET 网页。
2. 在 Eclipse 中，运行物理 Android 设备或模拟器上的应用。
3. 在 Android 应用 UI 中，输入用户名和密码。 这些信息可以是任意字符串，但必须是相同的值。
4. 在 Android 应用 UI 中，单击“**登录**”。 然后单击“发送推送”。

