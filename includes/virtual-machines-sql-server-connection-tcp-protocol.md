1. 使用远程桌面连接到虚拟机以后，搜索“配置管理器”：

    ![打开 SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. 在“SQL Server 配置管理器”的控制台窗格中，展开“SQL Server 网络配置”。

1. 在控制台窗格中，单击“MSSQLSERVER 的协议”（默认实例名称）。在详细信息窗格中，右键单击“TCP”，并单击“启用”（如果尚未启用）。

    ![启用 TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. 在控制台窗格中，单击“SQL Server 服务”。 在详细信息窗格中，右键单击“SQL Server (实例名)” （默认实例为 **SQL Server (MSSQLSERVER)**），然后单击“重新启动”以停止并重新启动该 SQL Server 实例。

    ![重新启动数据库引擎](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. 关闭 SQL Server 配置管理器。

有关启用 SQL Server 数据库引擎的协议的详细信息，请参阅[启用或禁用服务器网络协议](http://msdn.microsoft.com/library/ms191294.aspx)。
