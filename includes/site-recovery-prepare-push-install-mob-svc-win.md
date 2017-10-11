### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>准备 Windows 计算机上的推送安装

1. 请确保 Windows 计算机和进程服务器之间的网络连接。
2. 创建进程服务器可用于访问计算机的帐户。 该帐户应具有管理员权限 （本地或域）。 （使用此帐户仅用于推式安装和代理更新。）

   > [!NOTE]
   > 如果你不使用域帐户，禁用本地计算机上的远程用户访问控制。 若要禁用远程用户访问控制，HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 注册表项，添加一个新的 dword 值： **LocalAccountTokenFilterPolicy**。 将值设置为**1**。 若要执行此操作在命令提示符处，运行以下命令：  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
2. 在 Windows 防火墙中你想要保护的计算机上，选择**允许应用或功能通过防火墙**。 启用**文件和打印机共享**和**Windows Management Instrumentation (WMI)**。 对于属于某个域的计算机，你可以通过使用组策略对象 (GPO) 配置防火墙设置。

   ![防火墙设置](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

3. 添加在 CSPSConfigtool 中创建的帐户。
    1.  登录到配置服务器。
    2.  打开**cspsconfigtool.exe**。 （它是可用的快捷方式在桌面上和 %ProgramData%\home\svsystems\bin 文件夹中。）
    3.  上**管理帐户**选项卡上，选择**添加帐户**。
    4.  添加你创建的帐户。
    5.  输入当你启用复制计算机使用的凭据。
