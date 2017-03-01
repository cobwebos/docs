### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

1. 在“SQL 服务器”边栏选项卡中的“设置”下面，单击“防火墙”打开 SQL 服务器的“防火墙”边栏选项卡。

    ![SQL 服务器防火墙](./media/sql-data-warehouse-server-firewall/sql-server-firewall.png)
    
2. 查看显示的客户端 IP 地址，并使用所选的浏览器验证该地址是否为你在 Internet 上使用的 IP 地址（确认自己的 IP 地址）。 有时出于各种原因，这些 IP 地址并不匹配。

    ![你的 IP 地址](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. 假设 IP 地址匹配，请在工具栏上单击“添加客户端 IP”。

    ![添加客户端 IP](./media/sql-data-warehouse-server-firewall/add-client-ip.png)

    > [!NOTE]
    > 可以在 SQL 服务器（逻辑服务器）上向单个 IP 地址或整个地址范围打开防火墙。 打开防火墙后，SQL 管理员和用户可以登录到服务器上他们拥有有效凭据的任何数据库。
    >

4. 在工具栏上单击“保存”以保存此服务器级防火墙规则，然后单击“确定”。

    ![添加客户端 IP](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

<!--HONumber=Jan17_HO3-->


