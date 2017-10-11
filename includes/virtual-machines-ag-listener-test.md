在此步骤中，你都将使用在同一网络运行的客户端应用程序测试可用性组侦听器。

客户端连接具有以下要求：

* 客户端连接到侦听器必须来自于驻留在不同云服务于托管 Alwayson 可用性副本的计算机。
* 如果 Alwayson 副本位于不同子网，则客户端必须指定*MultisubnetFailover = True*连接字符串中。 这种情况会导致尝试并行连接到各种子网中的副本。 这种情况下包括跨区域 Alwayson 可用性组部署。

其中一个示例是从一个相同的 Azure 虚拟网络 （而不是托管副本） 中的虚拟机连接到侦听器。 若要完成此测试的简单方法是尝试将 SQL Server Management Studio 连接到可用性组侦听器。 另一种简单方法是运行[SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)、，如下所示：

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> 如果 EndpointPort 值为*1433年*，不要求你在调用中指定它。 前面的调用还假定客户端计算机已加入同一个域，并且调用方具有已授予权限在数据库上的使用 Windows 身份验证。
> 
> 

当测试侦听器时，一定要故障转移可用性组，以确保客户端可以连接到侦听器跨故障转移。

