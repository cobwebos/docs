
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>在云设备上配置远程管理

1. 在你的 StorSimple 设备管理器服务，单击**设备**。 选择并单击你从列表中的设备连接到服务的云设备。
    ![StorSimple 选择云设备](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. 转到**设置 > 安全性**以打开**安全设置**边栏选项卡。

     ![StorSimple 安全设置](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. 转到**远程管理**部分。 单击**远程管理**框。
     ![StorSimple 远程管理](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. 在**远程管理**边栏选项卡：

    1. 确保**启用远程管理**已启用。
    2. 默认值是通过 HTTPS 进行连接。 你可以选择使用 HTTP 进行连接。 通过 HTTP 连接是仅在受信任的网络上可接受的。 确保 HTTP 处于启用状态。
    3. 从边栏选项卡顶部的命令栏中，单击**...详细**，然后单击**下载证书**下载远程管理证书。 你可以指定要在其中保存此文件的位置。 应该用于连接到云设备的客户端或主机计算机上安装此证书。

        ![远程管理边栏选项卡](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. 单击**保存**并出现提示时，确认所做的更改。