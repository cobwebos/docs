### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>创建虚拟机的 TCP 终结点
若要从 internet 访问 SQL Server，虚拟机必须具有终结点以侦听传入的 TCP 通信。 此 Azure 配置步骤将定向到虚拟机可以访问的 TCP 端口的传入 TCP 端口通信。

> [!NOTE]
> 如果你要连接同一云服务或虚拟网络中，你不需要创建一个公开访问的终结点。 在这种情况下，你可以继续执行下一步。 有关详细信息，请参阅[连接方案](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)。
> 
> 

1. 在 Azure 门户中，选择**虚拟机 （经典）**。
2. 然后选择 SQL Server 虚拟机。
3. 选择**终结点**，然后单击**添加**终结点边栏选项卡顶部的按钮。
   
    ![门户创建终结点的步骤](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. 上**添加终结点**边栏选项卡，提供**名称**如 SQLEndpoint。
5. 选择**TCP**为**协议**。
6. 有关**公用端口**，如指定的端口号**57500**。
7. 有关**专用端口**，指定 SQL Server 的侦听端口，默认为**1433年**。
8. 单击**确定**创建的终结点。

