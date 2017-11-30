1. 在**应用**项目中，打开文件 `AndroidManifest.xml`。 在 `application` 开始标记之后添加以下代码：

    ```xml
    <service android:name=".ToDoMessagingService">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT"/>
        </intent-filter>
    </service>
    <service android:name=".ToDoInstanceIdService">
        <intent-filter>
            <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
        </intent-filter>
    </service>
    ```

2. 打开 `ToDoActivity.java` 文件并进行以下更改：

    - 添加 Import 语句：

        ```java
        import com.google.firebase.iid.FirebaseInstanceId;
        ```

    - 将 `MobileServiceClient` 的定义从“专用”更改为“专用静态”，使之现在如以下所示：

        ```java
        private static MobileServiceClient mClient;
        ```

    - 添加 `registerPush` 方法：

        ```java
        public static void registerPush() {
            final String token = FirebaseInstanceId.getInstance().getToken();
            if (token != null) {
                new AsyncTask<Void, Void, Void>() {
                    protected Void doInBackground(Void... params) {
                        mClient.getPush().register(token);
                        return null;
                    }
                }.execute();
            }
        }
        ```

    - 更新 `ToDoActivity` 类的 onCreate 方法。 请确保在 `MobileServiceClient` 实例化之后再添加此代码。

        ```java
        registerPush();
        ```

3. 添加新类以处理通知。 在项目资源管理器中，打开 **app** > **java** > **your-project-namespace** 节点，并右键单击包名称节点。 单击“新建”，并单击“Java 类”。 在“名称”中，键入 `ToDoMessagingService`，然后单击“确认”。 然后将类声明替换为：

    ```java
    import android.app.Notification;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;

    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;

    public class ToDoMessagingService extends FirebaseMessagingService {

        private static final int NOTIFICATION_ID = 1;

        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            String message = remoteMessage.getData().get("message");
            if (message != null) {
                sendNotification("Notification Hub Demo", message);
            }
        }

        private void sendNotification(String title, String messageBody) {
            PendingIntent contentIntent = PendingIntent.getActivity(this, 0, new Intent(this, ToDoActivity.class), 0);
            Notification.Builder notificationBuilder = new Notification.Builder(this)
                    .setSmallIcon(R.drawable.ic_launcher)
                    .setContentTitle(title)
                    .setContentText(messageBody)
                    .setContentIntent(contentIntent);
            NotificationManager notificationManager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
            if (notificationManager != null) {
                notificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
            }
        }
    }
    ```

4. 添其加他类来处理令牌更新。 创建 `ToDoInstanceIdService` java 类，然后将类声明替换为：

    ```java
    import com.google.firebase.iid.FirebaseInstanceIdService;

    public class ToDoInstanceIdService extends FirebaseInstanceIdService {

        @Override
        public void onTokenRefresh() {
            ToDoActivity.registerPush();
        }
    }
    ```

应用现已更新，可支持推送通知。
