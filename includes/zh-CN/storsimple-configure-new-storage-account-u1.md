<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-add-a-storage-account-in-storsimple-8000-series-update-10"></a>在 StorSimple 8000 系列 Update 1.0 中添加存储帐户
1. 在 StorSimple Manager 服务登录页上，选择服务并双击。 这会使你转到“快速启动”页。 选择“配置”  页。
2. 单击“添加/编辑存储帐户” 。
3. 在“添加/编辑存储帐”对话框中，单击“新增”。
4. 在 **提供程序** 字段中，选择适当的云服务提供程序。 受支持的提供程序有 Azure、Amazon S3、带 RRS 的 Amazon S3、HP 以及 OpenStack。 指定凭据以及与云服务提供程序的存储帐户关联的位置。 根据指定的云服务提供程序，为凭证显示的字段将不同。 
   
   * 如果已选择 Azure 作为云服务提供程序，则为 Microsoft Azure 存储帐户提供**名称**和主**访问密钥**。 对于 Azure 帐户，会自动填充位置。
     
        ![添加 Azure 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddAzureStorageaccount-include.png)
   * 如果已选择 Amazon S3 或带 RRS 的 Amazon S3，则提供一个友好**存储帐户名称**、**访问密钥**和**密钥**。 对于 Amazon S3 和带 RRS 的 Amazon S3，支持以下位置：
     
     * 美国标准
     * 美国西部（俄勒冈州）
     * 美国西部（北加利福尼亚）
     * 欧洲（爱尔兰）
     * 亚太（新加坡）
     * 亚太（悉尼）
     * 亚太（东京）
     * 南美洲（圣保罗）
       
       ![添加 Amazon 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddAmazonStorageaccount-include.png)
   * 如果已选择 HP 作为云服务提供程序，则提供一个友好**存储帐户名称**、**租户 ID**、**用户名**和**密码**。 对于 HP，支持以下位置：
     
     * 美国东部
     * 美国西部
       
       ![添加 HP 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddHPStorageaccount-include.png)
   * 如果已选择 **Openstack** 作为云服务提供程序，则提供**主机名**、**访问密钥**和**密钥**。
     
     > [!NOTE]
     > 对于所有云服务提供程序（Azure 除外），允许一个友好名称。 可以使用不同的友好名称，并使用相同的凭据集创建多个存储帐户。
     > 
     > 
     
        ![添加 Openstack 存储帐户](./media/storsimple-configure-new-storage-account-u1/AddOpenstackStorageaccount-include.png)
5. 选择“启用 SSL 模式”  创建用于在设备和云之间传输网络通信的安全通道。 仅当在私有云内执行操作时清除“ **启用 SSL 模式** ”复选框。
   
   > [!NOTE]
   > 如果使用 HP 作为提供程序，将始终启用 SSL。
   > 
   > 
6. 单击选中图标  ![选中图标](./media/storsimple-configure-new-storage-account/HCS_CheckIcon-include.png)。 将收到成功创建存储帐户的通知。
7. 新创建的存储帐户会显示在“存储帐户”下的“配置”页上。 单击“ **保存** ”，以保存新建的存储帐户。 提示确认时单击“ **确定** ”。

