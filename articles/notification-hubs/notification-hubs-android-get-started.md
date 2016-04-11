<properties
	pageTitle="通过 Azure 通知中心向 Android 发送推送通知 | Microsoft Azure"
	description="在本教程中，你将了解如何使用 Azure 通知中心将通知推送到 Android 设备。"
	services="notification-hubs"
	documentationCenter="android"
	keywords="推送通知、推送通知、android 推送通知"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags 	
	ms.service="notification-hubs"
	ms.date="03/15/2016"
	wacn.date="" />

# 通过 Azure 通知中心向 Android 发送推送通知

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started)]

##概述

> [AZURE.IMPORTANT] 若要完成本教程，你必须有一个有效的 Azure 帐户。如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)。

本教程演示如何使用 Azure 通知中心将推送通知发送到 Android 应用程序。
你将创建一个空白 Android 应用，它使用 Google Cloud Messaging (GCM) 接收推送通知。

请务必遵循[标记教程](./notification-hubs-routing-tag-expressions.md)，以便了解如何使用通知中心发送具有目标的推送通知。


## 开始之前

[AZURE.INCLUDE [notification-hubs-hero-slug](../includes/notification-hubs-hero-slug.md)]

可以在 GitHub 上的[此处](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted)找到本教程的已完成代码。


##先决条件

除了上面提到的有效的 Azure 帐户外，本教程只需要最新版本的 [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797)。


完成本教程是学习有关 Android 应用的所有其他通知中心教程的先决条件。


##创建支持 Google Cloud Messaging 的项目

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-google-cloud-messaging.md)]


##配置新通知中心


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7.在“设置”边栏选项卡中选择“通知服务”，然后选择“Google (GCM)”。输入 API 密钥并保存。

&emsp;&emsp;![Azure 通知中心 - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

你的通知中心现在已配置为使用 GCM，并且你有连接字符串，既可用于注册你的应用以接收通知，又可用于发送推送通知。

##<a id="connecting-app"></a>将你的应用连接到通知中心

###创建新的 Android 项目

1. 在 Android Studio 中，启动新的 Android Studio 项目。

   	![Android Studio - 新项目][13]

2. 选择“手机和平板电脑”外形规格和你要支持的“最低 SDK 版本”。然后单击“下一步”。

   	![Android Studio - 项目创建工作流][14]

3. 选择“空白活动”作为主活动，单击“下一步”，然后单击“完成”。

###将 Google Play 服务添加到项目

[AZURE.INCLUDE [添加 Play Services](../includes/notification-hubs-android-studio-add-google-play-services.md)]

###添加代码

1. 从 [Notification-Hubs-Android-SDK on Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4) 的“文件”选项卡下载 notification-hubs-0.4.jar 文件。在 Android Studio 的“项目视图”窗口中，将文件直接拖放到 **libs** 文件夹。右键单击该文件，然后单击“添加为库”。
  
2. 在**应用**的 Build.Gradle 文件的 **dependencies** 节中添加以下行。

	    compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

	在 **dependencies** 节的后面添加以下存储库。

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

3. 设置该应用程序以从 GCM 获取注册 ID，并使用它将应用实例注册到通知中心。

	在 AndroidManifest.xml 文件中，在 `</application>` 标记的下面添加以下权限。请确保将 `<your package>` 替换为 AndroidManifest.xml 文件顶部显示的包名称（在本示例中为 `com.example.testnotificationhubs`）。

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. 在 **MainActivity** 类中，在类声明的上面添加以下 `import` 语句。

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.widget.Toast;



4. 将以下私有成员添加到类的顶部。我们使用这些成员来配置你的应用程序和云服务之间的推送通知通道。

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	请确保更新以下三个占位符：
	* **SENDER\_ID**：将 `SENDER_ID` 设置为你之前在 [Google 云控制台](http://cloud.google.com/console)中获取的项目号。
	* **HubListenConnectionString**：将 `HubListenConnectionString` 设置为你的中心的 **DefaultListenAccessSignature** 连接字符串。你可以复制此连接字符串，方法是在 [Azure 门户]的你的中心的“设置”边栏选项卡上单击“访问策略”。
	* **HubName**：使用 [Azure 门户]的中心边栏选项卡上显示的你的通知中心的名称。


5. 在 `MainActivity` 类的 `OnCreate` 方法中，添加以下代码，以便在创建活动时向通知中心注册。

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. 在 `MainActivity.java` 中，为 `registerWithNotificationHubs()` 方法添加以下代码。向 Google Cloud Messaging 和通知中心注册之后，此方法将报告成功。

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    ToastNotify("Registered Successfully - RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	ToastNotify("Registration Exception Message - " + e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. 向活动添加 `ToastNotify` 方法可在应用正在运行且可见时显示通知。此外还重写 `onStart`、`onPause`、`onResume` 和 `onStop` 以确定该活动是否可见以便显示 toast。

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }

	    public void ToastNotify(final String notificationMessage)
	    {
	        if (isVisible == true)
	            runOnUiThread(new Runnable() {
	                @Override
	                public void run() {
	                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                }
	            });
	    }

8. 因为在默认情况下 Android 不处理通知显示问题，所以你必须编写自己的接收器。在 `AndroidManifest.xml` 中的 `<application>` 元素内添加以下元素。

	> [AZURE.NOTE] 将占位符替换为你的包名称。

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. 在“项目”视图中，展开“应用”>“src”>“main”>“java”。右键单击 **java** 下的包文件夹，单击“新建”，然后单击“Java 类”。

	![Android Studio - 新 Java 类][6]

10. 在新类的“名称”字段中键入 **MyHandler**，然后单击“确定”。


11. 在 `MyHandler.java` 的最上面添加以下 import 语句：

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. 更新类声明，使 `MyHandler` 成为 `com.microsoft.windowsazure.notifications.NotificationsHandler` 的子类，如下所示。

		public class MyHandler extends NotificationsHandler {


13. 在 `MyHandler` 类中添加以下代码。

	此代码将重写 `OnReceive` 方法，因此处理程序将弹出 Toast 以显示收到的通知。此处理程序还使用 `sendNotification()` 方法将推送通知发送到 Android 通知管理器。

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
	        mainActivity.ToastNotify(nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

14. 在 Android Studio 的菜单栏上，单击“生成”>“重新生成项目”，以确保你的代码中没有任何错误。

##发送推送通知

你可以通过在 [Azure 门户]中发送通知来测试你的应用程序接收推送通知的功能，如下所示，你可以在中心边栏选项卡中查找“故障排除”部分来进行此测试。

![Azure 通知中心 - 测试发送](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## （可选）直接从应用程序发送推送通知

对于大多数测试用例，你可能希望直接从你开发的应用程序发送推送通知。本节介绍如何正确实现此场景。

1. 在 Android Studio 的项目视图中展开“应用”>“src”>“main”>“res”>“layout”。打开 `activity_main.xml` 布局文件，然后单击“文本”选项卡以更新此文件的文本内容。使用以下代码更新此文件，此代码将添加新的 `Button` 和 `EditText` 控件，用于将推送通知消息发送到通知中心。将此代码添加到底部紧靠 `</RelativeLayout>` 前面的位置。

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. 将此行添加到 `build.gradle` 文件中的 `android` 下面。

		useLibrary 'org.apache.http.legacy'

3. 在 Android Studio 的项目视图中展开“应用”>“src”>“main”>“res”>“values”。打开 `strings.xml` 文件，并添加新的 `Button` 和 `EditText` 控件引用的字符串值。在文件底部紧靠在 `</resources>` 前面的位置添加这些值。

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


4. 在 `MainActivity.java` 文件中，将以下 `import` 语句添加在 `MainActivity` 类的上面。

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;
		
		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


5. 在 `MainActivity.java` 文件中，在 `MainActivity` 类的最上面添加以下成员。

	使用中心的 **DefaultFullSharedAccessSignature** 连接字符串更新 `HubFullAccess`。可从 [Azure 门户]复制此连接字符串，方法是单击通知中心的“设置”边栏选项卡上的“访问策略”。

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

6. 活动保留中心名称以及中心的完整共享访问连接字符串。你必须创建软件访问签名 (SaS) 令牌对 POST 请求进行身份验证，以便将消息发送到通知中心。为此，可以分析连接字符串中的密钥数据，然后按照[基本概念](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API 参考中所述创建 SaS 令牌。

	在 `MainActivity.java` 中，将以下方法添加到 `MainActivity` 类，以分析连接字符串。

	    /**
    	 * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
    	 * to parse the connection string so a SaS authentication token can be
    	 * constructed.
    	 *
    	 * @param connectionString This must be the DefaultFullSharedAccess connection
    	 *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);

	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }

7. 在 `MainActivity.java` 中，将以下方法添加到 `MainActivity` 类，以创建 SaS 身份验证令牌。

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {

            String targetUri;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();

                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;

                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");

                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);

                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));

            	// Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");

                // Construct authorization string
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


8. 在 `MainActivity.java` 中，将以下方法添加到 `MainActivity` 类，以使用内置的 REST API 处理“发送通知”按钮的点击行为，并将推送通知消息发送到中心。

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";

            new Thread()
            {
                public void run()
                {
                    try
                    {
                        HttpClient client = new DefaultHttpClient();

                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);

                        // Authenticate the POST request with the SaS token
                        post.setHeader("Authorization", generateSasToken(url));

                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");

                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));

                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##测试应用程序

####模拟器中的推送通知

如果你想要在模拟器中测试推送通知，请确保模拟器映像支持你为应用程序选择的 Google API 级别。如果你的映像不支持本机 Google API，那么此测试最终以 **SERVICE\_NOT\_AVAILABLE** 异常结束。

除上述条件外，请确保已将你的 Google 帐户添加到运行的模拟器的“设置”>“帐户”下。否则，尝试向 GCM 注册可能会导致 **AUTHENTICATION\_FAILED** 异常。

####运行应用程序

1. 运行应用，并留意报告注册成功的注册 ID。

   	![测试 Android - 通道注册][18]

2. 输入一条要发送到已在中心注册的所有 Android 设备的通知消息。

   	![测试 Android - 发送一条消息][19]

3. 按“发送通知”。运行此应用程序的所有设备将显示一个包含此推送通知消息的 `AlertDialog` 实例。未运行此应用程序，但之前已注册推送通知的设备将在 Android 通知管理器中收到通知。从左上角向下轻扫即可查看通知。

   	![测试 Android - 通知][21]

##后续步骤

建议下一步学习[使用通知中心向用户推送通知]教程。它将显示如何使用标记从 ASP.NET 后端将通知发送到目标特定的用户。

如果要按兴趣组划分用户，可以查看[使用通知中心发送最新消息]教程。

若要了解有关通知中心的更多一般信息，请参阅[通知中心指南]。

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.cn/
[通知中心指南]: http://msdn.microsoft.com/library/jj927170.aspx
[使用通知中心向用户推送通知]: notification-hubs-aspnet-backend-android-notify-users.md
[使用通知中心发送最新消息]: notification-hubs-aspnet-backend-android-breaking-news.md
[Azure 门户]: https://portal.azure.cn

<!---HONumber=Mooncake_0405_2016-->