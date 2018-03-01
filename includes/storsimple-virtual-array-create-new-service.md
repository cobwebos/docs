#### <a name="to-create-a-new-service"></a>创建新服务

1.  使用 Microsoft 帐户凭据登录到 Azure 门户，URL 如下：<https://portal.azure.com/>。 如果是在政府门户中部署设备，请从以下地址登录：<https://portal.azure.us/>

2.  在 Azure 门户中，单击“+ 创建资源”&gt;“存储”&gt;“StorSimple 虚拟系列”。

    ![创建新服务](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  在打开的“StorSimple 设备管理器”边栏选项卡中，执行以下操作：

    1.  为服务提供唯一“资源名称”。 资源名称是可用来识别该服务的友好名称。 名称可以为 2 到 50 个字符，包括字母、数字和连字符。 名称必须以字母或数字开头和结尾。

    2.  从下拉列表中选择一个“订阅”  。 订阅链接到计费帐户。 如果只有一个订阅，此字段不存在。

    3.  对于“资源组”，选择一个现有组或创建一个新组。 有关详细信息，请参阅 [Azure 资源组](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)。

    4.  为服务提供“位置”  。 有关哪个区域中有哪些服务可用的详细信息，请参阅 [Azure 区域](https://azure.microsoft.com/regions/#services)。 一般情况下，请选择距离要在其中部署设备的地理区域最近的**位置**。 还可能需要考虑以下因素：

        -   如果 Azure 中还存在希望使用 StorSimple 设备部署的现有工作负荷，我们建议使用该数据中心。

        -   StorSimple 设备管理器和 Azure 存储可位于两个不同的位置。 在这种情况下，需要分别创建 StorSimple 设备管理器和 Azure 存储帐户。 若要创建 Azure 存储帐户，请转到 Azure 门户中的 Azure 存储服务，并按照[创建 Azure 存储帐户](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)中的步骤操作。 创建此帐户后，按照[针对服务配置新的存储帐户](https://azure.microsoft.com/en-us/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service)中的步骤，将它添加到 StorSimple 设备管理器服务。

        -   如果是在政府门户中部署虚拟设备，则 StorSimple 设备管理器服务在美国爱荷华州和美国弗吉尼亚州可用。

    5.  选择“创建新的 Azure 存储帐户”可自动通过该服务创建存储帐户。 指定**存储帐户名称**。 如果需要将数据存储在不同位置，请取消选中此框。

    6.  如果希望在仪表板上显示此服务的快速链接，请选中“固定到仪表板”。

    7.  单击“创建”以创建 StorSimple 设备管理器。

        ![创建新服务](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

随后会定向到“服务”登陆页。 创建服务需要几分钟时间。 成功创建该服务后，用户将收到相应通知，服务状态将更改为“活动”。


