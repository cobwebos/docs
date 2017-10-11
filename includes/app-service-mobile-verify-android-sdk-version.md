由于正在进行开发，安装在 Android Studio 中的 Android SDK 版本可能会与代码中的版本不匹配。 在本教程中引用的 Android SDK 是版本 23，在撰写本文时最新。 将显示新版本的 sdk，并且我们建议使用可用的最新版本可能会增加的版本号。

版本不匹配的两种表现是：

- 当生成或重新生成项目时，可能会收到类似的 Gradle 错误消息"**找不到目标 Google Inc.:Google 不**"。
- 应该可以解决的代码中的标准 Android 对象基于`import`语句可能产生错误消息。

如果出现任何一种方法，在 Android Studio 中安装的 Android sdk 版本可能与下载的项目的 SDK 目标都不匹配。 若要验证版本，请进行以下更改：

1. 在 Android Studio 中，单击**工具** > **Android** > **SDK 管理器**。 如果你尚未安装最新版本的 SDK 平台，然后单击安装。 记下的版本号。
2. 上**项目资源管理器**选项卡上，在**Gradle 脚本**，打开文件**build.gradle (modeule: app)**。 确保**compileSdkVersion**和**buildToolsVersion**设置为安装的最新 SDK 版本。 标记可能如下所示：

             compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
3. 在 Android Studio 项目资源管理器，右键单击项目节点，选择**属性**，并在左侧列中选择**Android**。 确保**项目生成目标**设置为相同的 SDK 版本**targetSdkVersion**。

在 Android Studio 中，清单文件不再用于指定目标 SDK 版本和最低 SDK 版本，与与 Eclipse 的情况不同。
