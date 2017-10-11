<!--author=alkohli last changed: 01/20/17-->


#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>在 StorSimple 设备管理器服务作为同一 Azure 订阅中添加存储帐户凭据

1. 请转到你的 StorSimple 设备管理器服务。 在**配置**部分中，单击**存储帐户凭据**。

    ![存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. 上**存储帐户凭据**边栏选项卡，单击**+ 添加**。

    ![添加存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. 在**添加存储帐户凭据**边栏选项卡中，执行以下步骤：

    1. 如要在与你的服务相同的 Azure 订阅中添加存储帐户凭据，确保**当前**选择。

    2. 从**存储帐户**下拉列表中，选择现有存储帐户。

    3. 基于存储帐户选择，**位置**将显示 （灰显，不能在此处更改）。

    4. 选择**启用 SSL 模式**创建用于你的设备和云之间的网络通信的安全通道。 禁用**启用 SSL**仅当你在私有云内进行操作。

        ![添加存储帐户凭据边栏选项卡](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. 单击**添加**启动作业创建的存储帐户凭据。 成功创建存储帐户凭据后，你将收到通知。

        ![存储帐户凭据的成功通知](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

新创建的存储帐户凭据将显示在列表**存储帐户凭据**。

![存储帐户凭据的列表](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

