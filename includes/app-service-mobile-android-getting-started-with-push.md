1. 在你**应用**项目中，打开该文件`AndroidManifest.xml`。 在随后两个步骤中的代码，将 *`**my_app_package**`* 替换为你的项目的应用程序包的名称。 它的值`package`属性`manifest`标记。
2. 现有之后添加以下新权限`uses-permission`元素：

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE"
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" />
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />
3. 添加以下代码后的`application`开始标记：

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                         android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>
4. 打开文件*ToDoActivity.java*，并添加以下 import 语句：

        import com.microsoft.windowsazure.notifications.NotificationsManager;
5. 将以下私有变量添加到类。 替换 *`<PROJECT_NUMBER>`* 替换 Google 分配到前面的过程中的应用的项目编号。

        public static final String SENDER_ID = "<PROJECT_NUMBER>";
6. 更改的定义*MobileServiceClient*从**私有**到**公共静态**，因此它现在如下所示：

        public static MobileServiceClient mClient;
7. 添加了新类来处理通知。 在项目资源管理器，打开**src** > **主要** > **java**节点，然后右键单击包名称节点。 单击**新建**，然后单击**Java 类**。
8. 在**名称**，类型`MyHandler`，然后单击**确定**。

    ![](./media/app-service-mobile-android-configure-push/android-studio-create-class.png)

9. 在 MyHandler 文件中，将使用的类声明：

        public class MyHandler extends NotificationsHandler {
10. 添加以下 import 语句为`MyHandler`类：

        import com.microsoft.windowsazure.notifications.NotificationsHandler;
        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.AsyncTask;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
11. 接下来，添加到此成员`MyHandler`类：

        public static final int NOTIFICATION_ID = 1;
12. 在`MyHandler`类中，添加以下代码以重写**onRegistered**方法，用于移动服务通知中心注册你的设备。

        @Override
        public void onRegistered(Context context,  final String gcmRegistrationId) {
           super.onRegistered(context, gcmRegistrationId);

           new AsyncTask<Void, Void, Void>() {

               protected Void doInBackground(Void... params) {
                   try {
                       ToDoActivity.mClient.getPush().register(gcmRegistrationId);
                       return null;
                   }
                   catch(Exception e) {
                       // handle error                
                   }
                   return null;              
               }
           }.execute();
       }
13. 在`MyHandler`类中，添加以下代码以重写**onReceive**方法，这会导致通知显示时将会收到该通知。

        @Override
        public void onReceive(Context context, Bundle bundle) {
               String msg = bundle.getString("message");

               PendingIntent contentIntent = PendingIntent.getActivity(context,
                       0, // requestCode
                       new Intent(context, ToDoActivity.class),
                       0); // flags

               Notification notification = new NotificationCompat.Builder(context)
                       .setSmallIcon(R.drawable.ic_launcher)
                       .setContentTitle("Notification Hub Demo")
                       .setStyle(new NotificationCompat.BigTextStyle().bigText(msg))
                       .setContentText(msg)
                       .setContentIntent(contentIntent)
                       .build();

               NotificationManager notificationManager = (NotificationManager)
                       context.getSystemService(Context.NOTIFICATION_SERVICE);
               notificationManager.notify(NOTIFICATION_ID, notification);
       }
14. 回到 TodoActivity.java 文件中更新**onCreate**方法*ToDoActivity*类以注册通知处理程序类。 请确保添加此代码后的*MobileServiceClient*实例化。

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    你的应用程序现已更新以支持推送通知。
