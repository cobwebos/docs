
<!--
includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2016-03-21 , daleche.

As of circa 2015-09-04, the following topics might include this include:
articles/sql-database/sql-database-configure-firewall-settings.md
articles/sql-database/sql-database-connect-query.md


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. 登录到[Azure 门户](https://portal.azure.com/)在 http://portal.azure.com/。
2. 在左侧标题中，单击**浏览所有**。 **浏览**显示边栏选项卡。
3. 滚动并单击**SQL 服务器**。 **SQL 服务器**显示边栏选项卡。
   
    ![在门户中找到你的 Azure SQL 数据库服务器][b21-FindServerInPortal]
4. 为方便起见，请单击上早期版本的最小化控件**浏览**边栏选项卡。
5. 在筛选器文本框中，开始键入你的服务器的名称。 将显示你的行。
6. 单击你的服务器的行。 将显示你的服务器的边栏选项卡。
7. 服务器边栏选项卡，单击**设置**。 **设置**显示边栏选项卡。
8. 单击**防火墙**。 **防火墙设置**显示边栏选项卡。
   
    ![单击设置 > 防火墙][b31-SettingsFirewallNavig]
9. 单击**添加客户端 IP**。 第一个文本框中键入你的新规则的名称。
10. 键入你想要启用的范围的下限和上限 IP 地址值。
    
    * 方便起见，可使具有较低的值结尾**.0**和与高**.255**。
    
    ![添加用于允许的 IP 地址范围][b41-AddRange]
11. 单击 **“保存”**。

<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

includes/sql-database-include-ip-address-22-v12portal.md
? includes/sql-database-include-ip-address-*.md
-->
