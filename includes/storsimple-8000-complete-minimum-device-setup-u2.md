<!--author=alkohli last changed: 01/12/17-->

<a id="to-complete-the-minimum-storsimple-device-setup" class="xliff"></a>

#### 完成最低要求的 StorSimple 设备设置

1. 从“设备”边栏选项卡中的表格式设备列表中，选择并单击你的设备。 该设备处于“就绪可设置”状态。 “配置设备”边栏选项卡随即打开。

     ![StorSimple 最低要求设备设置网络接口](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. 在“配置设备”边栏选项卡中：
   
   1. 为你的设备提供“友好名称”  。 默认设备名称可反映设备型号和序列号等信息。 可分配最多包含 64 个字符的友好名称来管理设备。
   2. 基于部署设备的地理位置设置“时区”  。 设备将此时区用于所有计划操作。
   3. 在“DATA 0 设置”下：

       1. 你的 DATA 0 网络接口显示为已启用，并且具有通过设置向导配置的网络设置（IP、子网、网关）。 还会自动为云和 iSCSI 启用 DATA 0。

       2. 为控制器 0 和控制器 1 提供固定 IP 地址。 **控制器的固定 IP 地址需为子网内可由设备 IP 地址访问的可用 IP。** 如果已为 IPv4 配置 DATA 0 接口，需要以 IPv4 格式提供固定 IP 地址。 如果已为 IPv6 配置提供了前缀，则固定 IP 地址将自动填充到这些字段中。

            ![StorSimple 最低要求设备设置网络接口](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            控制器的固定 IP 地址用于为设备提供更新。 因此，这些固定 IP 必须可路由并能够连接到 Internet。 可以使用 [Test-HcsmConnection][Test] cmdlet 检查你的固定控制器 IP 是否可路由。 下面的示例演示固定控制器 IP 路由到 Internet，并且可以访问 Microsoft Update 服务器。

            ![Test-HcsmConnection 显示可路由的 IP](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. 单击 **“确定”**。 设备配置将启动。 当设备配置完成时，你将收到通知。 在“设备”边栏选项卡中，设备状态将更改为“联机”。

    ![StorSimple 最低要求设备设置网络接口](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

   > [!NOTE]
   > 通过访问“设备设置”边栏选项卡，你可以随时修改所有其他设备设置。

<!--Link reference-->
[Test]: https://technet.microsoft.com/library/dn715782(v=wps.630).aspx