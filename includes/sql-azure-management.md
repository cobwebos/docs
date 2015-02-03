
# 使用 SQL Server Management Studio 管理 Azure SQL Database 

您可以使用 Azure SQL Database 管理门户或 SQL Server Management Studio (SSMS) 客户端应用程序来管理 SQL Database 订阅以及创建和管理相关联的逻辑服务器和数据库。下方的指导介绍如何使用 Management Studio 管理 SQL Database 逻辑服务器和数据库。

<div class="dev-callout-new-collapsed">
<strong>说明 <span>Click to collapse</span></strong>
<div class="dev-callout-content">
<p>可以使用 SQL Server 2014、 SQL Server 2012 或 SQL Server 2008 R2 版本的 SSMS。不支持早期版本。</p>
</div>
</div>

本主题包括以下步骤：

-   [步骤 1：获取 SQL Server Management Studio][]
-   [步骤 2：连接到 SQL Database][]
-   [步骤 3：创建和管理数据库][]
-   [步骤 4：创建和管理登录名][]
-   [步骤 5：使用动态管理视图监视 SQL Database][]

<h2><a id="Step1" name="Step1"> </a>步骤 1：获取 Management Studio</h2>

Management Studio 是用于管理 SQL database 的集成的环境。当管理 Azure 上的数据库时，可以使用 Management Studio 应用程序与 SQL Server 一起安装或下载免费的 SQL Server 2012 Management Studio Express (SSMSE) 版本。下面的步骤介绍如何安装 SSMSE。

1.  在上[Microsoft SQL Server 2012 Express][]页上，选择 x86 版本的 Management Studio 如果您运行的是 64 位操作系统，在运行 32 位操作系统或 x64。单击**下载**，并出现提示时，运行安装程序。

2.  单击**全新的 SQL Server 独立安装或向现有安装添加功能**单击**确定**。

3.  接受许可条款，然后单击**下一步**。

4. 单击**安装**安装所需的 SQL Server 安装程序文件。

5.  在上**功能选择**屏幕上，**管理工具-
    基本**处于预先选定状态。这是因为正在运行的安装程序 Management Studio。如果您正在运行的所有 SQL Server Express 的安装程序，选择**管理工具-基本**选项，然后单击**下一步**。

6.  在上**错误报告**屏幕上，您可以根据需要选择发送错误报告给 Microsoft。这不需要使用 SSMSE。单击**下一步**以开始安装。

7.  安装完成后，您将看到**完成**页。单击**关闭**。 


<h2><a id="Step2" name="Step2"> </a>步骤 2：连接到 SQL Database</h2>

连接到 SQL Database 需要您知道在 Azure 上的服务器名称。您可能需要登录到该门户来获取此信息。

1.  登录到[Azure 管理门户][]。

2.  在左窗格中，单击**SQL 数据库**。

3.  在 SQL Database 主页上，单击**服务器**顶部的页后，可以列出所有与您的订阅关联的服务器。找到您要连接，并将其复制到剪贴板上的服务器的名称。

	接下来，配置 SQL Database 防火墙以允许来自本地计算机的连接。通过将您本地计算机的 IP 地址添加到防火墙例外列表来执行此操作。

1.  在 SQL Database 主页上，单击**服务器**，然后单击您想要连接的服务器。

2.  单击**配置**在页面的顶部。

3.  复制当前客户端 IP 地址中的 IP 地址。

4.  在配置页中，**允许的 IP 地址**包括三个框可以作为开始和结束值指定规则名称和范围内的 IP 地址。输入规则名称时，可以输入您的计算机的名称。对于开始和结束范围，将在您的计算机的 IP 地址粘贴到这两个框中，然后单击显示的复选框。

	规则名称必须是唯一的。如果这是您的开发计算机，您可以在 IP 范围开始框和 IP 范围结束框中输入的 IP 地址。否则，您可能需要输入更广泛的范围内的 IP 地址，以容纳来自贵组织中的其他计算机的连接。
 
5. 单击**保存**在页面的底部。

    **注意：**在防火墙设置的更改生效之前，可能最多有五分钟的延迟。

	现在您就可以连接到 SQL Database 使用 Management Studio。

1.  在任务栏上，单击**启动**，依次指向**所有程序**，依次指向**Microsoft SQL Server 2012**，然后单击**SQL Server Management Studio**。

2.  在**连接到服务器**，完全限定的服务器名称指定为 * serverName *.database.chinacloudapi.cn。在 Azure 上的服务器名称是由字母数字字符组成的自动生成的字符串。

3.  选择**SQL Server 身份验证**。

4.  在**登录**框中，输入格式创建您的服务器时在门户中指定的 SQL Server 管理员登录名 * login*@*yourServerName*。

5.  在**密码**框中，输入在创建您的服务器时在门户中指定的密码。

8.  单击**连接**来建立连接。

在 Azure 上，每个 SQL Database 逻辑服务器是定义一组数据库的抽象。每个数据库的物理位置可能是在数据中心中的任何计算机上。 

在早期版本中，您必须直接连接到**master**设置 Management Studio 中的连接时。此步骤不再是必需的。连接现在将会成功取决于服务器名称、 身份验证类型和管理员凭据。

您可以使用有关任务 （如创建和修改登录名的许多 SSMS 向导和 SQL Server 数据库上的数据库不能用于在 Azure 上的 SQL 数据库，因此将需要利用 TRANSACT-SQL 语句来完成这些任务。下面的步骤提供了这些语句的示例。有关 TRANSACT-SQL 中使用 SQL 数据库，其中包括有关哪些支持命令，详细信息的详细信息请参阅[TRANSACT-SQL 参考 (SQL Database)][]。

<h2><a id="Step3" name="Step3"> </a>步骤 3：创建并管理数据库</h2>
 在连接到**master**数据库中，您可以在服务器上创建新的数据库和修改或删除现有数据库。下面的步骤介绍如何通过 Management Studio 完成几个常见数据库管理任务。若要执行这些任务，请确保您连接到**master**与您设置您的服务器时创建的服务器级别主体登录名的数据库。

若要在 Management Studio 中打开查询窗口，请打开数据库文件夹，展开**系统 Databsaes**文件夹中，右键单击**master**，然后单击**新查询**。

-   使用**CREATE DATABASE**语句以创建一个新的数据库。有关详细信息，请参阅[CREATE DATABASE (SQL Database)][]。下面的语句创建一个新的数据库，名为**myTestDB** ，并指定它是具有最大大小为 1 GB 的 Web Edition 数据库。

        CREATE DATABASE myTestDB
        (MAXSIZE=1GB,
         EDITION='web');

单击**Execute**以运行查询。

-   使用**ALTER DATABASE**语句来修改现有的数据库，例如，如果您想要更改名称、 最大大小或数据库版本 （企业或 web）。有关详细信息，请参阅[ALTER DATABASE (SQL Database)][]。下面的语句修改数据库创建在上一步，若要更改的最大大小为 5 GB。

        ALTER DATABASE myTestDB
        MODIFY
        (MAXSIZE=5GB,
         EDITION='web');

-   使用**DROP DATABASE**语句删除现有数据库。有关详细信息，请参阅[DROP DATABASE (SQL Database)][]。下面的语句删除**myTestDB**数据库，但由于您将使用它创建登录名在下一步现在没有删除它。

        DROP DATABASE myTestBase;

-   Master 数据库具有**sys.databases**视图，可用于查看有关所有数据库的详细信息。若要查看所有现有数据库，请执行以下语句：

        SELECT * FROM sys.databases;

-   在 SQL Database 中**使用**语句不支持数据库之间切换。相反，您需要建立直接到目标数据库的连接。

<div class="dev-callout-new">
 <strong>说明 <span>Click to collapse</span></strong>
 <div class="dev-callout-content">    <p>许多创建或修改数据库的 TRANSACT-SQL 语句必须在自己的批处理中运行的不能与其他 TRANSACT-SQL 语句分组。有关详细信息，请参阅上面列出的链接中提供的特定于语句的信息。</p>
</div>
</div>

<h2><a id="Step4" name="Step4"> </a>步骤 4：创建并管理登录</h2>

**Master**将跟踪数据库的登录名以及哪些登录有权创建数据库或其他登录名。通过连接到管理登录名**master**与您设置您的服务器时创建的服务器级别主体登录名的数据库。您可以使用**CREATE LOGIN**， **ALTER LOGIN**，或**DROP LOGIN**语句来执行对将管理整个服务器的登录名的 master 数据库的查询。有关详细信息，请参阅[管理数据库和 SQL Database 中的登录名][]。 


-   使用**CREATE LOGIN**语句以创建新的服务器级别登录名。有关详细信息，请参阅[CREATE LOGIN (SQL Database)][]。下面的语句创建新的登录名为**login1**。替换**password1**与所选的密码。

        CREATE LOGIN login1 WITH password='password1';

-   使用**CREATE USER**语句来授予数据库级别权限。必须在中创建的所有登录名**master**数据库，但若要连接到不同的数据库，必须授予它的登录名的数据库级别权限使用**CREATE USER**语句对该数据库。有关详细信息，请参阅[CREATE USER (SQL Database)][]。 

-   将一个数据库，称为 login1 权限给予**myTestDB**，完成以下步骤：

 1.  若要刷新对象资源管理器，若要查看**myTestDB**数据库，您刚刚创建，用鼠标右键单击对象资源管理器中的服务器名称，然后单击**刷新**。  

 如果关闭了连接，您可以通过选择重新连接**连接对象资源管理器**文件菜单上。重复[步骤 2：连接到 SQL Database][]若要连接到数据库。

 2. 右键单击**myTestDB**数据库并选择**新查询**。

    3.  执行以下语句对 myTestDB 数据库来创建一个名为的数据库用户**login1User**对应的服务器级别登录**login1**。

            CREATE USER login1User FROM LOGIN login1;

-   使用**sp\_addrolemember**存储过程来为用户帐户提供适当级别的对数据库的权限。有关详细信息，请参阅[sp_addrolemember (TRANSACT-SQL)][]。下面提供的语句**login1User**到加上数据库的只读权限**login1User**到**db\_datareader**角色。

        exec sp_addrolemember 'db_datareader', 'login1User';    

-   使用**ALTER LOGIN**语句来修改现有登录名，例如，如果您想要更改的登录名的密码。有关详细信息，请参阅[ALTER LOGIN (SQL Database)][]。**ALTER LOGIN**语句应该针对运行**master**数据库。切换回连接到该数据库的查询窗口。 

    下面的语句修改**login1**登录来重置密码。
    替换**newPassword**替换为您的选择，密码和**oldPassword**具有的当前密码的登录名。

        ALTER LOGIN login1
        WITH PASSWORD = 'newPassword'
        OLD_PASSWORD = 'oldPassword';

-   使用**DROP LOGIN**语句删除现有的登录名。删除处于服务器级别的登录还会删除任何关联的数据库用户帐户。有关详细信息，请参阅[DROP DATABASE (SQL Database)][]。**DROP LOGIN**语句应该针对运行**master**数据库。下面的语句删除**login1**登录名。

        DROP LOGIN login1;

-   Master 数据库具有**sys.sql\_logins**查看可用于查看登录名。若要查看所有现有的登录名，请执行以下语句：

        SELECT * FROM sys.sql_logins;

<h2><a id="Step5" name="Step5"> </a>步骤 5：使用动态管理视图监视 SQL Database</h2>

SQL Database 支持多个可用于监视单独的数据库的动态管理视图。下面是您可通过这些视图检索的监视器数据类型的一些示例。有关更多用法示例的完整详细信息，请参阅[使用动态管理视图监视 SQL Database][]。

-   查询动态管理视图需要**VIEW DATABASE STATE**权限。若要授予**VIEW DATABASE STATE**对特定数据库用户，权限连接到您想要使用您的服务器级别主体登录名进行管理和执行以下语句对数据库的数据库：

        GRANT VIEW DATABASE STATE TO login1User;

-   计算数据库大小使用**sys.dm\_db\_partition\_stats**视图。**Sys.dm\_db\_partition\_stats**视图返回可用于计算数据库大小的数据库中的每个分区的页和行计数信息。下面的查询返回您的数据库的大小以兆字节为单位：

        SELECT SUM(reserved_page_count)*8.0/1024
        FROM sys.dm_db_partition_stats;   

-   使用**sys.dm\_exec\_connections**和**sys.dm\_exec\_sessions**视图来检索有关当前用户连接和与数据库相关联的内部任务的信息。下面的查询返回有关当前连接的信息：

        SELECT
            e.connection_id,
            s.session_id,
            s.login_name,
            s.last_request_end_time,
            s.cpu_time
        FROM
            sys.dm_exec_sessions s
            INNER JOIN sys.dm_exec_connections e
              ON s.session_id = e.session_id;

-   使用**sys.dm\_exec\_query\_stats**视图来检索的聚合性能统计信息缓存的查询计划。下面的查询返回有关按平均 CPU 时间排名前五个查询的信息。

        SELECT TOP 5 query_stats.query_hash AS "Query Hash",
            SUM(query_stats.total_worker_time), SUM(query_stats.execution_count) AS "Avg CPU Time",
            MIN(query_stats.statement_text) AS "Statement Text"
        FROM
            (SELECT QS.*,
            SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
            ((CASE statement_end_offset
                WHEN -1 THEN DATALENGTH(ST.text)
                ELSE QS.statement_end_offset END
                    - QS.statement_start_offset)/2) + 1) AS statement_text
             FROM sys.dm_exec_query_stats AS QS
             CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
        GROUP BY query_stats.query_hash
        ORDER BY 2 DESC;

<h2>其他资源</h2>

* [SQL Database 简介][]
* [管理数据库和 SQL Database 中的登录名][]
* [使用动态管理视图监视 SQL Database][]
* [SQL Database 设置模型][]
* [将用户添加到您的 SQL 数据库][]
* [TRANSACT-SQL 参考 (SQL Database)][]

  [如何使用 Azure SQL Database]: /zh-cn/documentation/articles/sql-database-dotnet-how-to-use/
  [步骤 1：获取 SQL Server Management Studio]: #Step1
  [步骤 2：连接到 SQL Database]: #Step2
  [步骤 3：创建和管理数据库]: #Step3
  [步骤 4：创建和管理登录名]: #Step4
  [步骤 5：使用动态管理视图监视 SQL Database]:#Step5
  [Microsoft SQL Server 2012 Express]: http://www.microsoft.com/zh-cn/download/details.aspx?id=29062
  [SSMS 安装程序-选择安装类型]: /media/installer_installation_type.png
  [SSMS 安装程序-选择的功能]: /media/installer_feature_selection.png
  [SSMS 安装程序-安装完成]: /media/installer_completed.png
  [Azure 管理门户]: http://manage.windowsazure.cn/
  [从管理门户中获取 SQL Database 服务器名称]: /media/portal_get_database_name.png
  [连接到 SSMS]: /media/ssms_connect.png
  [连接到 SSMS-属性]: /media/ssms_connect_properties.png
  [TRANSACT-SQL 参考 (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/bb510741(v=sql.120).aspx
  [创建数据库 (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee336274.aspx
  [ALTER DATABASE (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ff394109.aspx
  [删除数据库 (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee336259.aspx
  [管理数据库和 SQL Database 中的登录名]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee336235.aspx
  [创建登录名 (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee336268.aspx
  [创建用户 (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/ee336277.aspx
  [sp_addrolemember (TRANSACT-SQL)]: http://msdn.microsoft.com/zh-cn/library/ms187750.aspx
  [ALTER LOGIN (SQL Database)]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee336254.aspx
  [使用动态管理视图监视 SQL Database]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ff394114.aspx
  [SQL Database 简介]: http://msdn.microsoft.com/zh-cn/library/windowsazure/ee336230.aspx
  [SQL Database 设置模型]: http://msdn.microsoft.com/zh-cn/library/ee336227.aspx
  [将用户添加到您的 SQL 数据库]: http://blogs.msdn.com/b/sqlazure/archive/2010/06/21/10028038.aspx
