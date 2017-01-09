
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

1. 在“SQL 服务器”边栏选项卡中的“设置”下面，单击“防火墙”打开 SQL 服务器的“防火墙”边栏选项卡。

    ![SQL 服务器防火墙](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png)
    
2. 查看显示的客户端 IP 地址，并使用所选的浏览器验证该地址是否为你在 Internet 上使用的 IP 地址（确认自己的 IP 地址）。 有时出于各种原因，这些 IP 地址并不匹配。

    ![你的 IP 地址](../articles/sql-database/media/sql-database-get-started/your-ip-address.png)

3. 假设 IP 地址匹配，请在工具栏上单击“添加客户端 IP”。

    ![添加客户端 IP](../articles/sql-database/media/sql-database-get-started/add-client-ip.png)

    > [!NOTE]
    > 可以在服务器上向单个 IP 地址或整个地址范围打开 SQL 数据库防火墙。 打开防火墙后，SQL 管理员和用户可以登录到服务器上他们拥有有效凭据的任何数据库。
    >

4. 在工具栏上单击“保存”以保存此服务器级防火墙规则，然后单击“确定”。

    ![添加客户端 IP](../articles/sql-database/media/sql-database-get-started/save-firewall-rule.png)

> [!Tip]
> 有关教程，请参阅 [SQL 数据库教程：创建服务器、服务器级防火墙规则、示例数据库、数据库级防火墙规则并连接到 SQL Server](../articles/sql-database/sql-database-get-started.md)。    
>


<!--HONumber=Jan17_HO1-->


