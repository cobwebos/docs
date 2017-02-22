#### <a name="to-install-mpio-on-the-host"></a>在主机上安装 MPIO
1. 在 Windows Server 主机上打开服务器管理器。 默认情况下，当管理员组的成员登录到运行 Windows Server 2012 R2 或 Windows Server 2012 的计算机时，服务器管理器便会启动。 如果服务器管理器尚未打开，请依次单击“开始”>“服务器管理器”。
   
    ![服务器管理器](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. 依次单击“服务器管理器”>“仪表板”>“添加角色和功能”。 这将启动“添加角色和功能”向导。
   
    ![添加角色和功能向导 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. 在“添加角色和功能”向导中，执行以下操作：
   
   * 在“开始之前”页上，单击“下一步”。
   * 在“选择安装类型”页上，接受“基于角色或基于功能的安装”的默认设置。 单击“资源组名称” 的 Azure 数据工厂。
     
       ![添加角色和功能向导 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * 在“选择目标服务器”页上，选择“从服务器池中选择服务器”。 主机服务器应该会被自动发现。 单击“资源组名称” 的 Azure 数据工厂。
   * 在“选择服务器角色”页上，单击“下一步”。
   * 在“选择功能”页上，选择“多路径 I/O”，然后单击“下一步”。
     
       ![添加角色和功能向导 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * 在“确认安装选择”页上，确认选择，然后选择“如果需要，自动重新启动目标服务器”，如下所示。 单击“安装” 。
     
       ![添加角色和功能向导 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * 安装完成后，你将收到通知。 单击“关闭”以关闭向导。
     
       ![添加角色和功能向导 9](./media/storsimple-install-mpio-windows-server/IC741002.png)



<!--HONumber=Nov16_HO3-->


