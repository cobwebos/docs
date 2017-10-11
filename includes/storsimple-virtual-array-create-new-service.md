#### <a name="to-create-a-new-service"></a>若要创建新的服务

1.  使用 Microsoft 帐户凭据，登录到此 URL 处的 Azure 门户： <https://portal.azure.com/>。 如果部署政府门户中的设备，登录在： <https://portal.azure.us/>

2.  在 Azure 门户中，单击**+ 新建** &gt; **存储** &gt; **StorSimple 虚拟系列**。

    ![创建新的服务](./media/storsimple-virtual-array-create-new-service/createnewservice2.png) 

3.  在**StorSimple 设备管理器**打开的边栏选项卡执行以下操作：

    1.  提供一个唯一**资源名称**为你的服务。 资源名称是用于标识服务的友好名称。 名称可以包含 2 到 50 个字符，可以是字母、 数字和连字符。 名称必须开头和以字母或数字结尾。

    2.  选择**订阅**从下拉列表。 订阅链接到你的计费帐户。 此字段不存在，如果必须只有一个订阅。

    3.  有关**资源组**、 选择一个现有或创建新的组。 有关详细信息，请参阅[Azure 资源组](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)。

    4.  提供**位置**为你的服务。 请参阅[Azure 区域](https://azure.microsoft.com/regions/#services)有关哪些服务可以在哪个区域中的详细信息。 一般情况下，选择**位置**离你想要部署你的设备的地理区域。 你可能还要考虑以下因素：

        -   如果你还打算部署 StorSimple 设备的 Azure 中有现有的工作负荷，我们建议你使用该数据中心。

        -   你的 StorSimple 设备管理器和 Azure 存储可以是在两个不同的位置。 在这种情况下，你需要单独创建 StorSimple 设备管理器和 Azure 存储帐户。 若要创建 Azure 存储帐户，请转到 Azure 门户中的 Azure 存储服务，并按照中的步骤[创建 Azure 存储帐户](https://azure.microsoft.com/documentation/articles/storage-create-storage-account/#create-a-storage-account)。 创建此帐户后，将其添加到 StorSimple 设备管理器服务中的步骤[配置新的存储帐户服务](https://azure.microsoft.com/en-us/documentation/articles/storsimple-deployment-walkthrough/#configure-a-new-storage-account-for-the-service)。

        -   如果部署政府门户中的虚拟设备，StorSimple 设备管理器服务是在美国 Iowa 和美国 Virginia 位置中。

    5.  选择**创建新的 Azure 存储帐户**自动与服务创建存储帐户。 指定**存储帐户名称**。 如果你需要不同的位置中的数据，请取消选中此框。

    6.  检查**固定到仪表板**如果你想在你的仪表板上的快速链接到此服务。

    7.  单击**创建**创建 StorSimple 设备管理器。

        ![创建新的服务](./media/storsimple-virtual-array-create-new-service/createnewservice4.png)  

你将定向到**服务**登录页。 服务创建过程需要几分钟时间。 已成功创建了服务后，你将收到相应通知和服务的状态将更改为后**Active**。


