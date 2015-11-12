<properties 
	pageTitle="升级到 SQL 数据库 V12" 
	description="介绍如何从先前版本的 Azure SQL 数据库升级到 Azure SQL 数据库 V12。" 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg"
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.date="10/08/2015" 
	wacn.date=""/>


# 升级到 SQL 数据库 V12


> [AZURE.SELECTOR]
- [Azure 预览门户](/documentation/articles/sql-database-v12-upgrade)
- [PowerShell](/documentation/articles/sql-database-upgrade-server)


SQL 数据库 V12 是 SQL 数据库的最新版本，它具有[超越以前的 V2 版本的许多优点](/documentation/articles/sql-database-v12-whats-new)。本文介绍如何使用 Azure 预览门户将 V2 服务器升级到 V12。

在升级到 SQL 数据库 V12 的过程中，还必须[将所有 Web 和企业数据库更新到新的服务层](/documentation/articles/sql-database-upgrade-new-service-tiers)。下面的说明包括根据数据库的历史使用情况，使用定价层建议更新 Web 和企业数据库的步骤。



1. 在 [Azure 预览门户](http://portal.azure.com/)中，通过选择“浏览全部”>“SQL 服务器”并选择所需的服务器，浏览到要升级的服务器。
2. 选择“最新的 SQL 数据库更新”，然后选择“升级此服务器”。

      ![升级服务器][1]

## 升级 Web 和企业数据库

2. 升级所有 Web 和企业数据库。如果你的服务器具有任何 Web 或企业数据库，你必须对这些数据库进行升级。为了帮助你进行升级，SQL 数据库服务会为每个数据库建议适当的服务层和性能级别（定价层）。通过分析每个数据库的历史使用情况，该服务建议最适合运行你的现有数据库工作负荷的层。 
    
    选择每个数据库可查看并选择建议的要升级到的定价层。你始终可以浏览可用的定价层，并选择最适合你环境的定价层。

     ![数据库][2]



7. 单击建议的层后，你将会看到“选择你的定价层”边栏选项卡，你可以在其中选择一个层，然后单击“选择”按钮以更改为该层。为每个 Web 或企业数据库选择新层

    ![建议][6]


在服务器上的所有数据库都符合要求后，你便可以开始升级了

## 开始升级

3. 在服务器上的所有数据库都符合升级要求后，请**键入服务器名称**以确认要执行升级，然后单击“确定”。 

    ![验证升级][3]


4. 升级将启动并显示正在进行通知。此时将启动升级过程。根据你的特定数据库的详细信息，升级到 V12 可能需要一些时间。在此期间，服务器上的所有数据库都保持联机，但服务器和数据库管理操作会受到限制。

    ![正在进行升级][4]

    在实际转换到新的性能级别时，数据库连接可能会暂时中断一小段时间（通常以秒计）。如果应用程序对连接终止有暂时性故障处理机制（重试逻辑），则足以防止升级结束时连接中断。

5. 在升级操作完成后，将启用 SQL 数据库 V12 服务器功能。

    ![已启用 V12][5]


## 相关链接

-  [SQL 数据库 V12 的新增功能](/documentation/articles/sql-database-v12-whats-new)
- [规划和准备升级到 SQL 数据库 V12](/documentation/articles/sql-database-v12-plan-prepare-upgrade)


<!--Image references-->
[1]: ./media/sql-database-v12-upgrade/latest-sql-database-update.png
[2]: ./media/sql-database-v12-upgrade/upgrade-server2.png
[3]: ./media/sql-database-v12-upgrade/upgrade-server3.png
[4]: ./media/sql-database-v12-upgrade/online-during-upgrade.png
[5]: ./media/sql-database-v12-upgrade/enabled.png
[6]: ./media/sql-database-v12-upgrade/recommendations.png

<!---HONumber=79-->