---
title: 实现地理分散的解决方案
description: 了解如何在 Azure SQL 数据库和客户端应用程序中配置数据库以故障转移到已复制的数据库，以及如何测试故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli, devx-track-azurepowershell
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: bdc03d93654fdb0ae90600f8dad279f0fc8efbe9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070813"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>教程：实现地理分散的数据库（Azure SQL 数据库）
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在 SQL 数据库和客户端应用程序中配置数据库以故障转移到远程区域，并测试故障转移计划。 你将学习如何执行以下操作：

> [!div class="checklist"]
>
> - 创建[故障转移组](auto-failover-group-overview.md)
> - 运行 Java 应用程序以在 SQL 数据库中查询数据库
> - 测试故障转移

如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure 资源管理器模块仍受 Azure SQL 数据库的支持，但所有未来的开发都是针对 Az.Sql 模块的。 若要了解这些 cmdlet，请参阅 [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)。 Az 模块和 AzureRm 模块中的命令参数大体上是相同的。

若要完成本教程，请确保已安装以下项目：

- [Azure PowerShell](/powershell/azure/)
- Azure SQL 数据库中的单一数据库。 若要创建一个，请使用：
  - [Azure 门户](single-database-create-quickstart.md)
  - [Azure CLI](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > 本教程使用 *AdventureWorksLT* 示例数据库。

- Java 和 Maven，请参阅 [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/)（使用 SQL Server 生成应用），突出显示 **Java**，选择环境，然后按步骤操作。

> [!IMPORTANT]
> 请务必设置防火墙规则，以便使用要在其上执行本教程中步骤的计算机的公共 IP 地址。 数据库级防火墙规则会自动复制到辅助服务器。
>
> 有关信息，请参阅[创建数据库级防火墙规则](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)；若要确定计算机的用于服务器级防火墙规则的 IP 地址，请参阅[创建服务器级防火墙](firewall-create-server-level-portal-quickstart.md)。  

## <a name="create-a-failover-group"></a>创建故障转移组

使用 Azure PowerShell，在现有的服务器和另一区域中的新服务器之间创建[故障转移组](auto-failover-group-overview.md)。 然后，将示例数据库添加到故障转移组。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

若要创建故障转移组，请运行以下脚本：

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> 运行 `az login` 以登录到 Azure。

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

异地复制设置也可在 Azure 门户中更改，方法是：选择数据库，然后选择“设置” > “异地复制”。

![异地复制设置](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

## <a name="run-the-sample-project"></a>运行示例项目

1. 在控制台中，使用以下命令创建一个 Maven 项目：

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. 键入 **Y**，然后按 **Enter**。

1. 将目录切换到新项目。

   ```bash
   cd SqlDbSample
   ```

1. 使用最常用的编辑器，打开项目文件夹中的 *pom.xml* 文件。

1. 通过添加下面的 `dependency` 节来添加 Microsoft JDBC Driver for SQL Server 依赖项。 此依赖项必须粘贴在更大的 `dependencies` 节中。

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. 将 `properties` 节添加到 `dependencies` 节之后，指定 Java 版本：

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. 将 `build` 节添加到 `properties` 节之后，为清单文件提供支持：

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

1. 保存并关闭 *pom.xml* 文件。

1. 打开 ..\SqlDbSample\src\main\java\com\sqldbsamples 中的 *App.java* 文件，将内容替换为以下代码：

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

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id stored in the table to be able to make unique inserts
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

1. 保存并关闭 *App.java* 文件。

1. 在命令控制台中运行以下命令：

   ```bash
   mvn package
   ```

1. 启动应用程序。如果不手动停止，应用程序将运行大约 1 小时，让你有时间运行故障转移测试。

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>测试故障转移

运行以下脚本来模拟故障转移，观察应用程序结果。 注意在数据库迁移过程中某些插入和选择操作是如何失败的。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

可使用以下命令，检查灾难恢复服务器在测试过程中的角色：

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

若要测试某个故障转移，请执行以下操作：

1. 启动对故障转移组的手动故障转移：

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. 将故障转移组还原到主服务器：

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

可使用以下命令，检查灾难恢复服务器在测试过程中的角色：

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

若要测试某个故障转移，请执行以下操作：

1. 启动对故障转移组的手动故障转移：

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. 将故障转移组还原到主服务器：

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>后续步骤

在本教程中，已在 Azure SQL 数据库和应用程序配置了数据库以便故障转移到远程区域中，并测试了故障转移计划。 你已了解如何：

> [!div class="checklist"]
>
> - 创建异地复制故障转移组
> - 运行 Java 应用程序以在 SQL 数据库中查询数据库
> - 测试故障转移

进入下一个教程，了解如何将 Azure SQL 托管实例的实例添加到故障转移组：

> [!div class="nextstepaction"]
> [将 Azure SQL 托管实例的实例添加到故障转移组](../managed-instance/failover-group-add-instance-tutorial.md)
