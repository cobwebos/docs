注销进程服务器的步骤取决于进程服务器与配置服务器的连接状态。

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>注销处于连接状态的进程服务器

1. 以管理员身份远程登录到进程服务器。
2. 启动“控制面板”，并打开“程序”>“卸载程序”
3. 卸载名为“Microsoft Azure Site Recovery 配置/进程服务器”的程序
4. 完成步骤 3 后，即可卸载“Microsoft Azure Site Recovery 配置/进程服务器依赖项”

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>注销处于断开连接状态的进程服务器

> [!WARNING]
> 如果无法恢复其上安装了进程服务器的虚拟机，则可使用以下步骤。

1. 以管理员身份登录到配置服务器。
2. 打开管理命令提示符，并浏览到目录 `%ProgramData%\ASR\home\svsystems\bin`。
3. 现在运行该命令。

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. 成功删除进程服务器时会发出此消息：**成功注销了 server-name> (server-IP-address)**。
