1. 在浏览器中，导航到 Jenkins 虚拟机。 由于 Jenkins 仪表板无法通过不安全 HTTP 进行访问，因此将显示一条消息，指示需要使用 SSH 通过隧道登录到虚拟机。

    ![Jenkins 会提供相关信息，说明如何使用 SSH 连接到 Jenkins 虚拟机。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. 通过访问 SSH 打开 bash 或终端窗口。

1. 输入以下 `ssh` 命令，将 **&lt;username>** 和 **&lt;domain>** 占位符替换为创建 Jenkins 虚拟机时指定的值：

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. 按照 `ssh` 提示登录到 Jenkins 虚拟机。

1. 输入以下命令检索用于解锁 Jenkins 的初始密码。

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. 将显示在 bash 或终端窗口中的密码复制到剪贴板。

1. 在浏览器中，导航到 `http://localhost:8080`。

1. 将以前检索到的密码粘贴到“管理密码”字段中，并选择“继续”。

    ![Jenkins 存储初始密码，首次连接到 Jenkins 虚拟机时必须使用它进行解锁。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. 选择“安装建议的插件”。

    ![初始进入时，Jenkins 使你能够轻松安装一组建议的插件](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. 在“创建第一个管理员用户”页上，创建 Jenkins 仪表板的管理用户和密码，并选择“保存并完成”。

1. 在“Jenkins 已准备就绪!” 页上，选择“开始使用 Jenkins”。

    ![安装并配置用于访问的 Jenkins 后，即可使用它来创建和生成作业。](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)