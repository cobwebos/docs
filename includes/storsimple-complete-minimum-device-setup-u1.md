<!--author=alkohli last changed: 9/17/15-->

#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>若要完成最小的 StorSimple 设备设置
1. 选择设备，然后单击**快速启动**。 单击**完成设备设置**以启动配置设备向导。
2. 配置设备向导中**基本设置**对话框框中，执行以下操作：
   
   1. 提供**友好名称**为你的设备。 默认设备名称将反映设备型号和序列号等信息。 你可以分配最多为 64 个字符来管理你的设备的友好名称。
   2. 设置**时区**基于在其中部署该设备的地理位置。 你的设备将针对所有计划操作使用此时区。
   3. 下**DNS 设置**，提供的地址你**辅助 DNS 服务器**。 如果你要使用 IPv6，将基于在 Windows PowerShell 界面中提供的 IPv6 前缀来填充该字段。 
      如果未配置辅助 DNS 服务器，你将不允许保存你的设备配置。
   4. 在启用的 iSCSI 接口下启用为 iSCSI 至少一个网络。 在至少一个网络接口必须是支持云的和一个接口必须是支持 iSCSI 的。 DATA 0 是自动支持云的。
      
      ![StorSimple 最小设备设置基本设置](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupBasicSettings1-include.png)
3. 单击箭头图标。 ![StorSimple 箭头图标](./media/storsimple-complete-minimum-device-setup/HCS_ArrowIcon-include.png)
4. 在**网络接口**对话框框中，为控制器 0 和控制器 1 提供固定的 IP 地址。 **控制器的固定 IP 地址需要是可访问的设备 IP 地址的子网内的免费 Ip。** 如果 DATA 0 接口配置了 IPv4，固定的 IP 地址需要采用 IPv4 格式提供。 如果为 IPv6 配置提供前缀，固定的 IP 地址将自动填充这些字段中。

    ![StorSimple 最小设备设置网络接口](./media/storsimple-complete-minimum-device-setup-u1/HCS_MinDeviceSetupNetworkInterfaces2-include.png)

    控制器的固定的 IP 地址用于为设备提供更新，因此，固定的 Ip 必须是路由，并且能够连接到 Internet。 你可以检查你的固定的控制器 Ip 是通过使用可路由[Test-hcsmconnection] [ Test] cmdlet。 下面的示例演示固定的控制器 Ip 路由到 Internet，并且可以访问 Microsoft 更新服务器。 

     ![测试 HcsmConnection 显示可路由的 Ip](./media/storsimple-complete-minimum-device-setup-u1/Test-HcsmConnectionOutputRegisteredDevice.png)

1. 单击复选图标![StorSimple 复选图标](./media/storsimple-complete-minimum-device-setup/HCS_CheckIcon-include.png)。
   你将返回到该设备**快速启动**页。
   
   > [!NOTE]
   > 你可以随时修改所有其他设备设置，通过访问**配置**页。
   > 
   > 

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx