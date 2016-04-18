
<!--
../includes/sql-database-include-ip-address-22-v12portal.md

Latest Freshness check:  2015-09-04 , GeneMi.

As of circa 2015-09-04, the following topics might include this include:
/documentation/articles/sql-database-configure-firewall-settings
/documentation/articles/sql-database-connect-query


## Server-level firewall rules

### Add a server-level firewall rule through the new Azure portal
-->


1. 登录到 [Azure 门户](http://manage.windowsazure.cn/)（网址为 http://manage.windowsazure.cn/）。

3. 滚动并单击“SQL Server”。此时会显示“SQL Server”边栏选项卡。

4. 单击服务器所对应的行。此时会显示服务器的边栏选项卡。

5. 在服务器边栏选项卡上单击“设置”。此时会显示“设置”边栏选项卡。

6. 单击“防火墙”。此时会显示“防火墙设置”边栏选项卡。

7. 单击“添加客户端 IP”以添加客户端计算机的 IP 地址。门户将为你的规则构造名称。或者，你可以键入一个名称，并为你要启用的范围提供下限和上限 IP 地址值。为方便起见，可以让下限值以 **.0** 结尾，让上限值以 **.255** 结尾。

8. 单击“保存”。



<!-- Image references. -->

[b21-FindServerInPortal]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b21-v12portal-findsvr.png

[b31-SettingsFirewallNavig]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b31-v12portal-settingsfirewall.png

[b41-AddRange]: ./media/sql-database-include-ip-address-22-v12portal/firewall-ip-b41-v12portal-addrange.png



<!--
These includes/ files are a sequenced set, but you can pick and choose:

../includes/sql-database-include-ip-address-22-v12portal.md
? ../includes/sql-database-include-ip-address-*.md
-->

<!---HONumber=Mooncake_0411_2016-->