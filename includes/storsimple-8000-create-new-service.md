<!--author=alkohli last changed:02/10/2017-->


#### 创建新服务
<a id="to-create-a-new-service" class="xliff"></a>

1. 使用 Microsoft 帐户凭据登录到 [Azure 门户](https://portal.azure.com/)。

2. 在 Azure 门户中，单击 **+**，然后在应用商店中单击“查看所有”。

    ![创建 StorSimple Device Manager](./media/storsimple-8000-create-new-service/createssdevman1.png)

    搜索“StorSimple 物理”。 选择并单击“StorSimple 物理设备系列”，然后单击“创建”。 或者，在 Azure 门户中，单击 **+**，然后在“存储”下单击“StorSimple 物理设备系列”。

    ![创建 StorSimple Device Manager](./media/storsimple-8000-create-new-service/createssdevman11.png)

3. 在“StorSimple 设备管理器”边栏选项卡中，执行以下步骤：
   
   1. 为服务提供唯一“资源名称”。 此名称是可用来标识服务的友好名称。 名称可以为 2 到 50 个字符，包括字母、数字和连字符。 名称必须以字母或数字开头和结尾。

   2. 从下拉列表中选择一个“订阅”  。 订阅链接到计费帐户。 如果只有一个订阅，此字段不存在。

   3. 对于“资源组”，请使用现有组或创建新组。 有关详细信息，请参阅 [Azure 资源组](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)。
   
   4. 为服务提供“位置”  。 一般情况下，请选择离要部署设备的地理区域最近的位置。 还可能需要考虑以下注意事项： 
      
      * 如果 Azure 中还存在希望使用 StorSimple 设备部署的现有工作负荷，应使用该数据中心。
      * StorSimple Device Manager 服务和 Azure 存储可位于两个不同的位置。 在这种情况下，需要分别创建 StorSimple 设备管理器和 Azure 存储帐户。 若要创建 Azure 存储帐户，请转到 Azure 门户中的 Azure 存储服务，并按照[创建 Azure 存储帐户](../articles/storage/storage-create-storage-account.md#create-a-storage-account)中的步骤操作。 创建此帐户后，按照[针对服务配置新的存储帐户](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#configure-a-new-storage-account-for-the-service)中的步骤，将它添加到 StorSimple 设备管理器服务。

   5. 选择“新建存储帐户”  自动通过该服务创建存储帐户。 为此存储帐户指定名称。 如果需要将数据存储在不同位置，请取消选中此框。

   6. 如果希望在仪表板上显示此服务的快速链接，请选中“固定到仪表板”。
      
   7. 单击“创建”以创建 StorSimple 设备管理器。

       ![创建 StorSimple Device Manager](./media/storsimple-8000-create-new-service/createssdevman2.png)
   
创建服务需要几分钟时间。 在服务成功创建后，会发出一条通知并且会打开新的服务边栏选项卡。
   
![创建 StorSimple Device Manager](./media/storsimple-8000-create-new-service/createssdevman5.png)


