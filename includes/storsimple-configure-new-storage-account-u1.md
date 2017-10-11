<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>若要在 StorSimple 8000 系列更新 1.0 中添加存储帐户
1. 在 StorSimple Manager 服务的登录页面上，选择你的服务并双击它。 这将需要你**快速启动**页。 选择**配置**页。
2. 单击**添加/编辑存储帐户**。
3. 在**添加/编辑存储帐户**对话框中，单击**添加新**。
4. 在**提供程序**字段中，选择适当的云服务提供程序。 支持的提供程序是 Azure，Amazon S3，使用 RR、 HP 和 OpenStack Amazon S3。 指定凭据和与云服务提供商的存储帐户关联的位置。 提供凭据的字段将不同取决于你指定的云服务提供程序。 
   
   * 如果您已选择 Azure 作为云服务提供程序，提供**名称**和主**访问密钥**为 Microsoft Azure 存储帐户。 Azure 帐户，将自动填充位置。
     
        ![添加 Azure 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * 如果你选择了 Amazon S3 或带 RR Amazon S3，提供一个友好**存储帐户名称**，**访问密钥**，和**密钥**。 对于 Amazon S3 带 RR Amazon S3，支持以下位置：
     
     * 美国标准
     * 我们西部 (Oregon)
     * 我们西部 (Northern California)
     * 欧洲 （爱尔兰）
     * 亚太 （新加坡）
     * 亚太 （悉尼）
     * 亚太 （东京）
     * 南美洲 （圣保罗）
       
       ![添加 Amazon 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * 如果选择了 HP 作为云服务提供程序提供一个友好**存储帐户名称**，**租户 ID**，**用户名**，和**密码**。 对于 HP，支持以下位置：
     
     * 美国东部
     * 美国西部
       
       ![添加 HP 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * 如果选择了**Openstack**作为云服务提供程序，提供**主机名**，**访问密钥**，和**密钥**。
     
     > [!NOTE]
     > 对于所有云服务提供商，不包括 Azure 中，允许有一个友好名称。 可以使用不同的友好名称，还可以使用相同的凭据集创建多个存储帐户。
     > 
     > 
     
        ![添加 Openstack 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. 选择**启用 SSL 模式**创建用于你的设备和云之间的网络通信的安全通道。 清除**启用 SSL 模式**复选框仅当你在私有云内进行操作。
   
   > [!NOTE]
   > 如果你使用 HP 作为提供程序，则始终会启用 SSL。
   > 
   > 
6. 单击复选图标 ![选中图标](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png)。 成功创建存储帐户后，你将收到通知。
7. 新创建的存储帐户将显示在**配置**下页上**存储帐户**。 单击**保存**以保存新的存储帐户。 单击**确定**当系统提示确认。

