## <a name="add-the-applications-registration-information-to-your-app"></a>向应用添加应用程序的注册信息

在此步骤中，需要将客户端 ID 添加到项目。

1.  打开 `MainActivity`（在 `app` > `java` > `{host}.{namespace}` 下）
2.  将以 `final static String CLIENT_ID` 开头的行替换为：
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. 打开：`app` > `manifests` > `AndroidManifest.xml`
4. 将下面的活动添加到 `manifest\application` 节点。 此操作会注册 `BrowserTabActivity`，可允许 OS 在完成身份验证后继续运行应用程序：

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

