#### <a name="to-create-a-new-service"></a>创建新服务
1. 使用 Microsoft 帐户凭据登录到 Azure 经典门户，URL 为 [https://manage.windowsazure.com/](https://manage.windowsazure.com/)。 如果在政府门户中部署设备，请登录：[https://manage.windowsazure.us/](https://manage.windowsazure.us/)
2. 在门户中，单击“新建”>“数据服务”>“StorSimple Manager”>“快速创建”。
3. 在显示的表单中，执行以下操作：
   
   1. 为你的服务提供唯一“名称”  。 这是可用于标识该服务的友好名称。 名称可以为 2 到 50 个字符，包括字母、数字和连字符。 名称必须以字母或数字开头和结尾。
   2. 对于用于管理 StorSimple 虚拟设备的服务，请从“托管设备类型”下拉列表中选择“虚拟设备系列”。
   3. 为服务提供“位置”  。 位置是指要部署设备的地理区域。
      
      * 如果 Azure 中存在希望使用 StorSimple 设备部署的其他工作负荷，我们建议使用该数据中心。
      * StorSimple Manager 和 Azure 存储可位于两个不同的位置。 在这种情况下，需要分别创建 StorSimple Manager 和 Azure 存储帐户。 若要创建 Azure 存储帐户，请转到门户中的 Azure 存储服务，并按照[创建 Azure 存储帐户](../articles/storage/storage-create-storage-account.md#create-a-storage-account)中的步骤操作。 创建此帐户后，通过按照[针对服务配置新的存储帐户](#optional-step-configure-a-new-storage-account-for-the-service)中的步骤，将它添加到 StorSimple Manager 服务。
      * 如果在政府门户中部署虚拟设备，StorSimple Manager 服务将位于美国爱荷华州和美国弗吉尼亚州。
   4. 从下拉列表中选择一个“订阅”  。 订阅链接到计费帐户。 如果只有一个订阅，此字段不存在。
   5. 选择“创建新的 Azure 存储帐户”可自动通过该服务创建存储帐户。 此存储帐户具有特殊名称，例如“storsimplebwv8c6dcnf”。 如果需要将数据存储在不同的位置，请清除此复选框。
   6. 单击“创建 StorSimple Manager”  创建服务。
      
      ![](./media/storsimple-ova-create-new-service/image1m-include.png)
   
   随后定向到“服务”  登录页。 创建服务需要几分钟时间。 成功创建服务后，你会收到相应的通知。
   
   ![](./media/storsimple-ova-create-new-service/image2-include.png)
   
   服务的状态将更改为“活动”。

