<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>若要完成最小的 StorSimple 设备设置
1. 在**设备**页上，选择的设备，单击要转到特定设备的页面的设备名称旁的箭头。 
   
    ![使用设备设置为联机的设备页](./media/storsimple-complete-minimum-device-setup/HCS_DevicesPageM-include.png) 
2. 单击快速启动图标![快速启动图标](./media/storsimple-complete-minimum-device-setup/HCS_QuickStartIcon-include.png)若要访问设备快速启动页。 单击**完成设备设置**启动**配置设备**向导。
   
    ![设备快速启动页](./media/storsimple-complete-minimum-device-setup/Device_Quick_Start_page_1M.png)
3. 上**基本设置**页上，执行以下操作：
   
   1. 提供**友好名称**为你的设备。 默认设备名称将反映设备型号和序列号等信息。 你可以分配最多为 64 个字符来管理你的设备的友好名称。
   2. 设置**时区**基于在其中部署该设备的地理位置。 你的设备将针对所有计划操作使用此时区。
   3. 下**DNS 设置**，提供的地址你**辅助 DNS 服务器**。 如果你要使用 IPv6，将基于在 Windows PowerShell 界面中提供的 IPv6 前缀来填充该字段。 
      如果未配置辅助 DNS 服务器，你将不允许保存你的设备配置。
   4. 在启用的 iSCSI 接口下启用为 iSCSI 至少一个网络。 在至少一个网络接口必须是支持云的和一个接口必须是支持 iSCSI 的。 DATA 0 是自动支持云的。
      
      ![StorSimple 最小设备设置基本设置](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupBasicSettings1-include.png)
4. 单击箭头图标。 ![StorSimple 箭头图标](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
5. 上**网络接口**页上，为控制器 0 和控制器 1 提供固定的 IP 地址。 如果 DATA 0 接口配置了 IPv4，固定的 IP 地址需要采用 IPv4 格式提供。 如果为 IPv6 配置提供前缀，固定的 IP 地址将自动填充这些字段中。

    > [!NOTE] 
    > - 控制器的固定 IP 地址需要是可访问的设备 IP 地址的子网内的免费 Ip。
    > - 控制器的固定的 IP 地址用于为设备提供更新，因此，固定的 Ip 必须是路由，并且能够连接到 Internet。

    ![StorSimple 最小设备设置网络接口](./media/storsimple-complete-minimum-device-setup/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

1. 单击复选图标![StorSimple 复选图标](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png)。
   你将返回到该设备**快速启动**页。
   
   > [!NOTE]
   > 你可以随时修改所有其他设备设置，通过访问**配置**页。
   > 
   > 

![可用的视频](./media/storsimple-complete-minimum-device-setup/Video_icon.png)**可用的视频**

若要观看的视频，演示如何完成最低要求的设备设置，请单击[此处](https://azure.microsoft.com/documentation/videos/minimum-storsimple-device-setup/)。

