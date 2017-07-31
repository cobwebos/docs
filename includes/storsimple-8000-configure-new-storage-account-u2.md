<!--author=alkohli last changed: 01/20/17-->


#### <a name="to-add-a-storage-account-credential-in-the-same-azure-subscription-as-the-storsimple-device-manager-service"></a>在 StorSimple Device Manager 服务所在的同一 Azure 订阅中添加存储帐户凭据

1. 转到 StorSimple Device Manager 服务。 在“配置”部分中，单击“存储帐户凭据”。

    ![存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct1.png)

2. 在“存储帐户凭据”边栏选项卡上，单击“+ 添加”。

    ![添加存储帐户凭据](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct2.png)

3. 在“添加存储帐户凭据”边栏选项卡中，执行以下步骤：

    1. 在你的服务所在的同一 Azure 订阅中添加存储帐户凭据时，请确保选中“当前”。

    2. 从“存储帐户”下拉列表中，选择一个现有存储帐户。

    3. 将根据所选的存储帐户显示**位置**（灰显并且在此处无法更改）。

    4. 选择“启用 SSL 模式”  创建用于在设备和云之间传输网络通信的安全通道。 如果仅在私有云中操作，请禁用“启用 SSL”。

        ![“添加存储帐户凭据”边栏选项卡](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct3.png)

    5. 单击“添加”为存储帐户凭据启动作业创建。 成功创建存储帐户凭据后，你将收到通知。

        ![存储帐户凭据的成功通知](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct5.png)

新创建的存储帐户凭据将显示在“存储帐户凭据”列表下。

![存储帐户凭据的列表](./media/storsimple-8000-configure-new-storage-account-u2/createnewstorageacct6.png)

