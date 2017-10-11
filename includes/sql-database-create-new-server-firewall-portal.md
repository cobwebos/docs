
<!--
includes/sql-database-create-new-server-firewall-portal.md

Latest Freshness check:  2016-11-28 , rickbyh.

As of circa 2016-04-11, the following topics might include this include:
articles/sql-database/sql-database-get-started.md
articles/sql-database/sql-database-configure-firewall-settings
articles/sql-data-warehouse-get-started-provision.md

-->
### <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>在 Azure 门户中创建服务器级防火墙规则

1. 上的 SQL 服务器边栏选项卡，在设置下，单击**防火墙**以打开 SQL server 的防火墙边栏选项卡。

    <!-- ![sql server firewall](../articles/sql-database/media/sql-database-get-started/sql-server-firewall.png) -->

2. 查看显示的客户端 IP 地址并验证这是你在使用你选择的浏览器在 Internet 上的 IP 地址 （询问"什么是我的 IP 地址）。 有时它们不匹配出于各种原因。

    <!-- ![your IP address](../articles/sql-database/media/sql-database-get-started/your-ip-address.png) -->

3. 假设的 IP 地址匹配，则单击**添加客户端 IP**工具栏上。

    ![添加客户端 IP](../articles/sql-data-warehouse/media/sql-data-warehouse-get-started-provision/add-client-ip.png)

    > [!NOTE]
    > 你可以打开到单个 IP 地址或地址的整个范围的服务器上的 SQL 数据库防火墙。 打开防火墙，SQL 管理员和用户登录到的服务器上任何数据库，它们具有有效的凭据。
    >

4. 单击**保存**保存此服务器级防火墙规则，然后单击工具栏上**确定**。

    ![添加客户端 IP](../articles/sql-database/media/sql-database-get-started-portal/server-firewall-rule.png)

> [!Tip]
> 本教程，请参阅[SQL 数据库教程： 创建服务器、 服务器级防火墙规则、 示例数据库、 数据库级防火墙规则并连接到 SQL Server](../articles/sql-database/sql-database-get-started.md)。    
>
