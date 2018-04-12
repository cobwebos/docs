---
title: 实现地理分散的 Azure SQL 数据库解决方案 | Microsoft Docs
description: 了解如何配置 Azure SQL 数据库和应用程序以便故障转移到复制的数据库，以及如何测试故障转移。
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,business continuity
ms.topic: tutorial
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 569eef6e1d930e505bc6dff9b692814438e5bd4d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="implement-a-geo-distributed-database"></a>实现地理分散的数据库

在本教程中，将配置 Azure SQL 数据库和应用程序以便故障转移到远程区域中，然后测试故障转移计划。 学习如何： 

> [!div class="checklist"]
> * 创建数据库用户并授予权限
> * 设置数据库级防火墙规则
> * 创建[异地复制故障转移组](sql-database-geo-replication-overview.md)
> * 创建并编译 Java 应用程序以查询 Azure SQL 数据库
> * 执行灾难恢复演练

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。


## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保已完成了以下先决条件：

- 安装最新的 [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)。 
- 安装 Azure SQL 数据库。 本教程使用以下任一快速入门中名为“mySampleDatabase”的 AdventureWorksLT 示例数据库：

   - [创建 DB - 门户](sql-database-get-started-portal.md)
   - [创建 DB - CLI](sql-database-get-started-cli.md)
   - [创建 DB - PowerShell](sql-database-get-started-powershell.md)

- 已确定了对数据库执行 SQL 脚本的一种方法，可以使用以下查询工具之一：
   - [Azure 门户](https://portal.azure.com)中的查询编辑器。 有关在 Azure 门户中使用查询编辑器的详细信息，请参阅[使用查询编辑器进行连接和查询](sql-database-get-started-portal.md#query-the-sql-database)。
   - 最新版本的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 是用于管理任何 SQL 基础结构的集成环境，这些基础结构包括从 Microsoft Windows 的 SQL Server 到 SQL 数据库等不同的结构。
   - 最新版本的 [Visual Studio Code](https://code.visualstudio.com/docs) 是一种图形代码编辑器，适用于 Linux、macOS 和 Windows，并且支持各种扩展，其中包括 [mssql 扩展](https://aka.ms/mssql-marketplace)（用于查询 Microsoft SQL Server、Azure SQL 数据库和 SQL 数据仓库）。 有关在 Azure SQL 数据库中使用此工具的详细信息，请参阅[使用 VS 代码进行连接和查询](sql-database-connect-query-vscode.md)。 

## <a name="create-database-users-and-grant-permissions"></a>创建数据库用户并授予权限

使用以下查询工具之一连接到数据库并创建用户帐号：

- Azure 门户中的查询编辑器
- SQL Server Management Studio
- Visual Studio Code

这些用户帐号将自动复制到辅助服务器（并保持同步）。 如果进行连接的客户端所在的 IP 地址尚未配置防火墙，若要使用 SQL Server Management Studio 或 Visual Studio Code，则需要配置防火墙规则。 有关详细步骤，请参阅[创建服务器级防火墙规则](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。

- 在查询窗口中，执行以下查询以在数据库中创建两个用户帐户。 此脚本向“app_admin”帐户授予“db_owner”权限，并向“app_user”帐户授予“选择”和“更新”权限。 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>创建数据库级防火墙

为 SQL 数据库创建一个[数据库级防火墙规则](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)。 此数据库级防火墙规则将自动复制到本教程中创建的辅助服务器。 为简单起见（在本教程中），请使用本教程中执行步骤的计算机的公共 IP 地址。 若要确定用于当前计算机的服务器级防火墙规则的 IP 地址，请参阅[创建服务器级防火墙](sql-database-get-started-portal.md#create-a-server-level-firewall-rule)。  

- 在打开的查询窗口中，将之前的查询替换为以下查询，将 IP 地址替换为环境中相应的 IP 地址。  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>创建活动异地复制自动故障转移组 

使用 Azure PowerShell 在现有 Azure SQL Server 和 Azure 区域中新的空白 Azure SQL Server 之间创建[活动异地复制自动故障转移组](sql-database-geo-replication-overview.md)，然后将示例数据库添加到故障转移组。

> [!IMPORTANT]
> 这些 cmdlet 需要 Azure PowerShell 4.0。 [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. 使用现有服务器和示例数据库的值填充 PowerShell 脚本的变量，并为故障转移组名称提供全局唯一值。

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. 在故障转移区域中创建空的备份服务器。

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. 在两个服务器之间创建故障转移组。

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. 将数据库添加到故障转移组。

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>安装 Java 软件

为了能够执行此部分中的步骤，需要熟悉如何使用 Java 开发，但不需要熟悉如何使用 Azure SQL 数据库。 

### <a name="mac-os"></a>**Mac OS**
打开终端并导航到要在其中创建 Java 项目的目录。 输入以下命令安装 **brew** 和 **Maven**： 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

有关安装和配置 Java 和 Maven 环境的详细指导，请转到[使用 SQL Server 构建应用](https://www.microsoft.com/sql-server/developer-get-started/)，选择“Java”，选择“MacOS”，然后按照步骤 1.2 和 1.3 中配置 Java 和 Maven 的详细说明进行操作。

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
打开终端并导航到要在其中创建 Java 项目的目录。 输入以下命令安装 **Maven**：

```bash
sudo apt-get install maven
```

有关安装和配置 Java 和 Maven 环境的详细指导，请转[使用 SQL Server 构建应用](https://www.microsoft.com/sql-server/developer-get-started/)，选择“Java”，选择“Ubuntu”然后按照步骤 1.2、1.3 和 1.4 中配置 Java 和 Maven 的详细说明进行操作。

### <a name="windows"></a>**Windows**
使用官方安装程序安装 [Maven](https://maven.apache.org/download.cgi)。 使用 Maven 帮助管理依赖项、内部版本、测试和运行 Java 项目。 有关安装和配置 Java 和 Maven 环境的详细指导，请转[使用 SQL Server 构建应用](https://www.microsoft.com/sql-server/developer-get-started/)，选择“Java”，选择“Windows”，然后按照步骤 1.2 和 1.3 中配置 Java 和 Maven 的详细说明进行操作。

## <a name="create-sqldbsample-project"></a>创建 SqlDbSample 项目

1. 在命令控制台（例如 Bash）中，创建 Maven 项目。 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. 键入“Y”，然后单击“输入”。
3. 将目录更改到新创建的项目中。

   ```bash
   cd SqlDbSamples
   ```

4. 使用喜爱的编辑器，打开项目文件夹中的 pom.xml 文件夹。 

5. 通过打开喜爱的文本编辑器并将以下行复制并粘贴到 pom.xml 文件中，将 Microsoft JDBC Driver for SQL Server 依赖项添加到 Maven 项目。 不要覆盖文件中预填充的现有值。 JDBC 依赖项必须粘贴在更大的“依赖项”部分（）中。   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. 通过在“依赖项”部分之后的 pom.xml 文件中添加以下“属性”部分，指定编译项目时面向的 Java 版本。 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. 在“属性”部分之后的 pom.xml 文件中添加以下“构建”部分，以支持 jar 中的清单文件。       

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```
8. 保存并关闭 pom.xml 文件。
9. 打开 App.java 文件 (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) 并将内容替换为以下内容。 将故障转移组名称替换为自己的故障转移组名称。 如果更改过数据库名称、用户或密码的值，则同时也更改这些值。

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. 保存并关闭 App.java 文件。

## <a name="compile-and-run-the-sqldbsample-project"></a>编译并运行 SqlDbSample 项目

1. 在命令控制台中执行以下命令。

   ```bash
   mvn package
   ```
2. 完成后，执行以下命令以运行该应用程序（将运行约 1 小时，除非手动停止）：

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>执行灾难恢复演练

1. 调用故障转移组的手动故障转移。 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. 在故障转移期间观察应用程序结果。 DNS 缓存刷新时，某些插入将失败。     

3. 找出灾难恢复服务器正在执行的角色。

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. 故障回复。

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. 故障回复期间观察应用程序结果。 DNS 缓存刷新时，某些插入将失败。     

6. 找出灾难恢复服务器正在执行的角色。

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[活动异地复制和故障转移组](sql-database-geo-replication-overview.md)。
