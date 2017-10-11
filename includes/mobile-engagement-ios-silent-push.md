> [!IMPORTANT]
> 若要从 Mobile Engagement 接收推送通知，你需要启用`Silent Remote Notifications`应用程序中。 你需要将远程通知值添加到你的 Info.plist 文件中的 UIBackgroundModes 数组。
> 
> 

1. 打开`info.plist`项目文件中
2. 右键单击顶部的项列表中 (`Information Property List`) 并添加新行
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. 在新行中输入`Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. 单击左侧的箭头以展开的行
5. 将以下值添加到项 0`App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. 后进行更改，info.plist XML 应包含以下键和值：
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. 如果你使用**Xcode 7 +**和**iOS 9 +**:
   
   * 启用**推送通知**中目标 > 你的目标名称 > 功能。

