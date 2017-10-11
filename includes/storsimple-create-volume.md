<!--author=SharS last changed: 02/04/2016-->

#### <a name="to-create-a-volume"></a>创建卷
1. 在设备上**快速启动**页上，单击**添加卷**。 这将启动添加卷向导。
2. 在添加卷向导，在**基本设置**，执行以下操作：
   
   1. 提供**名称**卷。
   2. 指定**设置的容量**以 GB 或 TB 卷。 卷容量必须介于 1 GB 和 64 TB 之间使用物理设备。
   3. 在下拉列表中，选择**使用类型**卷。 
   4. 如果将此卷用于存档数据，选择**将此卷用于不常访问的存档数据**复选框。 对于所有其他使用情况，只需选择**分层卷**。 （分层的卷已以前称为主卷）。
      
        ![添加卷](./media/storsimple-create-volume/ScreenshotUpdate1VolumeFlow.png)
      
      1. 单击箭头图标 ![箭头图标](./media/storsimple-create-volume/HCS_ArrowIcon-include.png) 转到下一页。
3. 在**其他设置**对话框中，添加新的访问控制记录 (ACR):
   
   1. 提供**名称**为你的 ACR。
   2. 下**iSCSI 发起程序名称**，提供 iSCSI 限定名称 (IQN) 的 Windows 主机。 如果你没有 IQN，请转到[获取 Windows Server 主机的 IQN](#get-the-iqn-of-a-windows-server-host)。
   3. 我们建议你通过选择启用默认备份**启用默认备份此卷**复选框。 默认备份将创建一个策略，在 22:30 （设备时间） 每一天执行并创建此卷的云快照。
      
      > [!NOTE]
      > 在此处启用备份后，将无法还原。 你将需要编辑卷以修改此设置。
      > 
      > 
      
        ![添加卷](./media/storsimple-create-volume/AddVolume2-include.png)
4. 单击复选图标 ![选中图标](./media/storsimple-create-volume/HCS_CheckIcon-include.png)。 使用指定的设置，将创建一个卷。

![可用的视频](./media/storsimple-create-volume/Video_icon.png)**可用的视频**

若要观看的视频，演示如何创建 StorSimple 卷，请单击[此处](https://azure.microsoft.com/documentation/videos/create-a-storsimple-volume/)。

