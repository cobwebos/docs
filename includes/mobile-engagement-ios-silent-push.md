> [!IMPORTANT]
> 若要从 Mobile Engagement 接收推送通知，需要在应用程序中启用 `Silent Remote Notifications`。需要在 Info.plist 文件中，将 remote-notification 值添加到 UIBackgroundModes 数组中。
> 
> 

1. 在项目中打开 `info.plist` 文件
2. 右键单击列表 (`Information Property List`) 的第一项，然后添加新行
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)
3. 在新行中输入 `Required background modes`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)
4. 单击左箭头，扩展该行
5. 将以下值添加到第 0 项 `App downloads content in response to push notifications`
   
    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)
6. 进行更改后，info.plist XML 应包含以下项和值：
   
        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>
7. 如果使用的是 **Xcode 7+** 和 **iOS 9+**：
   
   * 依次启用“目标”中的“推送通知”>“目标名称”>“功能”。

<!---HONumber=AcomDC_0921_2016-->