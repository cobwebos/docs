
### <a name="update-manifest-file-to-enable-notifications"></a>更新清单文件，启用通知
将下面的应用内消息资源复制到 `<application>` 和 `</application>` 标记之间的 Manifest.xml 中。

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

### <a name="specify-an-icon-for-notifications"></a>为通知指定图标
在 `<application>` 和 `</application>` 标记之间的 Manifest.xml 中粘贴以下 XML 代码段。

        <meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

如此即可定义在系统和应用内通知中显示的图标。 对于应用内通知，这是可选项，而对于系统通知，这是必需项。 Android 将拒绝具有无效图标的系统通知。

请确保使用位于**可绘制**文件夹之一中的图标（如 ``engagement_close.png``）。 **mipmap** 文件夹。

> [!NOTE]
> 不应使用 **启动器** 图标。 该图标具有不同的分辨率，通常位于 mipmap 文件夹中，因此不支持使用。
> 
> 

对于实际应用，可以根据 [Android 设计准则](http://developer.android.com/design/patterns/notifications.html)，使用适用于通知的图标。

> [!TIP]
> 若要确保使用正确的图标分辨率，可以查看 [这些示例](https://www.google.com/design/icons)。
> 向下滚动到“通知”部分，单击任一图标，并单击 `PNGS` 下载图标可绘制集。 可以看到每个版本的图标所用的是哪些文件夹以及拥有怎样的分辨率。
> 
> 

### <a name="enable-your-app-to-receive-gcm-push-notifications"></a>使应用可以接收 GCM 推送通知
1. 在替换了从 Firebase 项目控制台获取的“发送者 ID”后，将以下内容粘贴到 `<application>` 和 `</application>` 标记之间的 Manifest.xml 中。 特意使用了 \n，所以请确保最后一个项目编号与之相符。
   
        <meta-data android:name="engagement:gcm:sender" android:value="************\n" />
2. 将以下代码粘贴到 `<application>` 和 `</application>` 标记之间的 Manifest.xml 中。 替换包名称 <Your package name>。
   
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
3. 添加最后一组权限（在 `<application>` 标记前亮显）。 将 `<Your package name>` 替换为应用程序的实际程序包名称。
   
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
        <permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

