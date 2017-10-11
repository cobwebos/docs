1. 打开 Android SDK 管理器，通过单击 Android studio 工具栏上的图标，或通过单击**工具** -> **Android** -> **SDK 管理器**菜单上。 在你的项目中找到使用的 Android sdk 目标版本，通过单击打开**显示包的详细信息**，然后选择**Google Api**，如果尚未安装。
2. 单击**SDK 工具**选项卡。 如果你尚未安装 Google Play 服务，请单击**Google Play 服务**如下所示。 然后单击**应用**安装。 
   
    记下 SDK 路径，以便在稍后的步骤中使用。 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. 打开**build.gradle**应用目录中的文件。
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. 下添加此行*依赖关系*: 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. 单击**项目与 Gradle 文件同步**在工具栏中的图标。
6. 打开**AndroidManifest.xml**并添加到此标记*应用程序*标记。
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />

