### <a name="grant-access-to-your-push-certificate-to-mobile-engagement"></a>授予 Mobile Engagement 访问推送证书的权限
若要允许 Mobile Engagement 代表用户发送推送通知，需授予其访问证书的权限。 通过配置并将证书输入 Mobile Engagement 门户中，即可完成授权。 请确保获取 [Apple 的文档](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

1. 导航到 Mobile Engagement 门户。 确保位于正确的位置，并单击底部的“参与”  按钮：
   
    ![](./media/mobile-engagement-ios-send-push/engage-button.png)
2. 单击 Engagement 门户中的“设置”  页面。 从中单击“原生推送”  部分，上传 p12 证书：
   
    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)
3. 选择 p12、上载，并键入密码：
   
    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

## <a id="send"></a>向应用发送通知
现在我们将创建一个简易的推送通知市场活动，向我们的应用发送通知：

1. 导航到 Mobile Engagement 门户中的“市场宣传”  选项卡。
2. 单击“新建公告”  创建推送市场活动
   
    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)
3. 设置市场活动的第一个字段：
   
    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)
   
   * 为市场活动提供 **名称** 
   * 选择“仅应用外”作为“传递时间”：这是简易的 Apple 推送通知类型，有几行文字。
   * 在通知文本中先键入“标题”（在推送中会在第一行显示）。
   * 然后键入消息（会在第二行显示）
4. 向下滚动，并在内容部分中选择“仅通知” 
   
    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)
5. 已设置完最基本的市场活动。 现在向下滚动，单击“创建”  按钮保存推送通知市场活动。 
6. 最后，单击“激活”  发送推送通知。 
   
    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)
7. 可以在 iOS 设备的通知中心接收通知，如下所示：
   
    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)
8. 如果有 Apple Watch 与此 iOS 设备进行了配对，那么在 Apple Watch 上也可以看到该通知：
   
    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)

