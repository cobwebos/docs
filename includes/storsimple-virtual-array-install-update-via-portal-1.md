<!--author=alkohli last changed: 11/02/17 -->

#### <a name="to-install-updates-via-the-azure-portal"></a>通过 Azure 门户安装更新

1. 转到 StorSimple 设备管理器并选择“设备”。 从连接到服务的设备列表中，选择并单击要更新的设备。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. 在“设置”边栏选项卡中，单击“设备更新”。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. 如果有可用的软件更新，会显示一条消息。 若要检查更新，还可以单击“扫描”。 记下运行的软件版本。 

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    在扫描开始和成功完成时，系统会发出通知。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. 在扫描更新后，单击“下载更新”。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. 在“新更新”边栏选项卡中，查看发行说明。 另请注意，在下载更新之后，需确认安装。 单击 **“确定”**。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. 在上传开始和成功完成时，系统会发出通知。

     ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. 在“设备更新”边栏选项卡中，单击“安装”。

     ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. 在“新的更新”边栏选项卡中，会警告该更新会造成中断。 因为虚拟阵列是单节点设备，所以设备在更新后会重新启动。 这会中断正在进行的任何 IO。 单击“确定”以安装更新。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. 当安装作业开始时，系统会发出通知。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  在安装作业成功完成后，单击“设备更新”边栏选项卡中的“查看作业”链接来监视安装。 

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    此操作将转到“安装更新”边栏选项卡。 可以在此处查看关于该作业的详细信息。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. 如果使用的是运行软件版本为 Update 0.6 (10.0.10293.0) 的虚拟数组，则正在运行 Update 1 且已完成操作。 可以跳过其余步骤。 如果使用的虚拟数组运行的是 Update 0.6 (10.0.10293.0) 之前的软件版本，则现已更新到 Update 0.6。 将再显示一条消息，指出更新可用。 重复步骤 4-8 以安装 Update 1。

    ![更新设备](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

