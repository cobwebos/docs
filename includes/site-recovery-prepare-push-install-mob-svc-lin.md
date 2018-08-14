### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>在 Linux 服务器上准备推送安装

1. 确保 Linux 计算机与进程服务器之间已建立网络连接。
1. 创建可供进程服务器用来访问计算机的帐户。 帐户应该是源 Linux 服务器上的 **root** 用户。 只能将此帐户用于推送安装和更新。
1. 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
1. 在要复制的计算机上安装最新的 openssh、openssh-server 和 openssl 包。
1. 确保安全外科 (SSH) 已启用且正在端口 22 上运行。
1. 在 sshd_config 文件中启用 SFTP 子系统和密码身份验证。 执行以下步骤:

    a. 以 **root** 身份登录。

    b. 在 **/etc/ssh/sshd_config** 文件中，找到以“PasswordAuthentication”开头的行。

    c. 取消注释该行，并将值更改为 **yes**。

    d. 找到以“Subsystem”开头的行，并取消注释该行。

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. 重启 sshd 服务。

1. 添加在 CSPSConfigtool 中创建的帐户。 执行以下步骤:

    a. 登录到配置服务器。

    b. 打开 **cspsconfigtool.exe**。 桌面上有该工具的快捷方式，也可以在 %ProgramData%\home\svsystems\bin 文件夹中找到它。

    c. 在“管理帐户”选项卡中，选择“添加帐户”。

    d. 添加已创建的帐户。

    d. 输入为计算机启用复制时使用的凭据。
