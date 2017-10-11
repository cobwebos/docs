### <a name="grant-mobile-engagement-access-to-your-gcm-api-key"></a>移动用户参与策略访问权限授予你的 GCM API 密钥
若要允许 Mobile Engagement 替你发送推送通知，你需要允许其访问你的 API 密钥。 这可通过配置和 Mobile Engagement 门户中输入你的密钥。

1. 从 Azure 经典门户中，确保你在应用程序中我们将用于此项目，并依次**接洽**底部的按钮：
   
    ![](./media/mobile-engagement-android-send-push/engage-button.png)
2. 然后单击**设置** -> **原生推送**部分以输入 GCM 密钥：
   
    ![](./media/mobile-engagement-android-send-push/engagement-portal.png)
3. 单击**编辑**前端中的图标**API 密钥**中**GCM 设置**部分如下所示：
   
    ![](./media/mobile-engagement-android-send-push/native-push-settings.png)
4. 在弹出窗口中，粘贴你之前获取的 GCM 服务器密钥，然后单击**确定**。
   
    ![](./media/mobile-engagement-android-send-push/api-key.png)

## <a id="send"></a>将通知发送到你的应用程序
现在，我们将创建将推送通知发送到我们的应用程序的简单的推送通知营销活动。

1. 导航到**到达**Mobile Engagement 门户中的选项卡。
2. 单击**新公告**创建你的推送通知活动。
   
    ![](./media/mobile-engagement-android-send-push/new-announcement.png)
3. 设置完成以下步骤活动的第一个字段：
   
    ![](./media/mobile-engagement-android-send-push/campaign-first-params.png)
   
    a. 命名你的活动。
   
    b。 选择**传递类型**作为*系统通知-> 简单*： 这是功能标题和小型行文本的简单 Android 推送通知类型。
   
    c. 选择**传递时间**作为*随时*以允许应用程序是否应用程序将启动或未收到通知。
   
    d. 在通知文本框键入**标题**将中加粗的推送。
   
    e. 然后键入你**消息**
4. 滚动缩小对象，而在**内容**部分中，选择**仅通知**。
   
    ![](./media/mobile-engagement-android-send-push/campaign-content.png)
5. 已完成设置可能最基本的营销活动。 现在再次向下滚动并单击**创建**按钮以保存你的活动。
6. 最后步骤： 单击**激活**若要激活活动以发送推送通知。
   
    ![](./media/mobile-engagement-android-send-push/campaign-activate.png)

