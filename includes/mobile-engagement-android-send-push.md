
### <a name="update-manifest-file-to-enable-notifications"></a>更新清单文件，以启用通知
将下面的应用内消息资源复制到你 Manifest.xml 之间`<application>`和`</application>`标记。

        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/plain" />
              </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="text/html" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
        </activity>
        <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
                <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
        </receiver>
        <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
        </receiver>

### <a name="specify-an-icon-for-notifications"></a>指定通知图标
将以下 XML 代码段粘贴之间你 Manifest.xml`<application>`和`</application>`标记。

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

这定义同时在系统和应用内通知中显示的图标。 它是可选的系统通知但是必需的应用内通知。 Android 将拒绝具有无效的图标的系统通知。

请确保使用之一中存在一个图标**可绘制**文件夹 (如``engagement_close.png``)。 **mipmap**文件夹不受支持。

> [!NOTE]
> 不应使用**启动器**图标。 具有不同的解决方法，通常会在 mipmap 文件夹中，我们不支持。
> 
> 

为实际的应用程序，您可以使用适用于每个通知图标[Android 设计准则](http://developer.android.com/design/patterns/notifications.html)。

> [!TIP]
> 若要确保使用正确的图标的解决方法，你可以查看[这些示例](https://www.google.com/design/icons)。
> 向下滚动到**通知**部分中单击一个图标，图标，然后单击`PNGS`下载图标可绘制组。 你可以看到哪些的解决方法要用于每个版本的图标可绘制的文件夹。
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>使你的应用以接收 GCM 推送通知
1. 将以下内容粘贴到你 Manifest.xml 之间`<application>`和`</application>`替换后的标记**发件人 ID**你 Firebase 项目控制台获得。 \N 是有意因此请确保你最终数字与其项目。
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. 将下面的代码粘贴到你 Manifest.xml 之间`<application>`和`</application>`标记。 将包名称<Your package name>。
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
        android:exported="false">
            <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
            </intent-filter>
        </receiver>
   
        <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<Your package name>" />
            </intent-filter>
        </receiver>
3. 添加的最后一个之前突出显示的权限集`<application>`标记。 替换`<Your package name>`由你的应用程序的实际包名称。
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

