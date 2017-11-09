
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>在云设备上配置远程管理

1. 在 StorSimple Device Manager 服务中，单击“设备”。 从连接到该服务的设备列表中选择并单击云设备。
    ![StorSimple 选择云设备](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. 转到“设置”>“安全性”以打开“安全设置”边栏选项卡。

     ![StorSimple 安全设置](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. 转到“远程管理”部分。 单击“远程管理”框。
     ![StorSimple 远程管理](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. 在“远程管理”边栏选项卡中：

    1. 确保启用了“启用远程管理”。
    2. 默认通过 HTTPS 连接。 可以选择使用 HTTP 进行连接。 只有受信任的网络才支持通过 HTTP 连接。 确保启用了 HTTP。
    3. 从边栏选项卡顶部的命令栏中，单击“...更多”，然后单击“下载证书”以下载远程管理证书。 可以指定要在其中保存此文件的位置。 应当将此证书安装在用来连接到云设备的客户端或主机上。

        ![“远程管理”边栏选项卡](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. 在出现提示时单击“保存”，确认更改。