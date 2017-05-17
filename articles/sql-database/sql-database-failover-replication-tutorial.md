---
title: "对 Azure SQL 数据库解决方案进行复制和故障转移 | Microsoft Docs"
description: "了解如何配置 Azure SQL 数据库和应用程序以便故障转移到复制的数据库，以及如何测试故障转移。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>对 Azure SQL 数据库解决方案进行复制和故障转移

在本教程中，将配置 Azure SQL 数据库和应用程序以便故障转移到远程区域中，然后测试故障转移计划。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

若要完成本教程，请确保你具有：

- 最新版本的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 安装 SSMS 就会安装最新版本的 SQLPackage，这是一个可用于自动执行一系列数据库开发任务的命令行实用工具。 
- 要迁移的 Azure 数据库。 本教程使用以下任一快速入门中名为 **mySampleDatabase 的 AdventureWorksLT 示例数据库：

   - [创建 DB - 门户](sql-database-get-started-portal.md)
   - [创建 DB - CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>创建 Azure Active Directory 用户（可选）

在此步骤中，创建或标识 Azure Active Directory 用户，可作为用户添加到 Azure SQL 数据库逻辑服务器和示例数据库。
- 如果订阅属于包含现有用户帐户的 Azure Active Directory 企业环境，则标识 3 个用户帐户，分别用作本教程的 Active Directory 管理用户、应用程序管理用户和应用程序用户，并继续执行步骤 3：创建 SQL 数据库登录名和用户。 
- 如果订阅不属于 Azure Active Directory 企业环境，或者属于不含任何现有用户帐户的 Azure Active Directory 企业环境（并且你有权创建新的 Azure Active Directory 用户帐户。

1. 登录到 [Azure 门户](http://portal.azure.com)。
2. 在左侧菜单中，单击“更多服务”。
3. 在筛选器文本框中，键入 Azure ，然后选择“Azure Active Directory”。
4. 在“Azure Active Directory”页的“快速任务”窗格中，单击“添加用户”。
5. 在“用户”窗体中，创建以下用户。
   - 名称：ad-admin
   - 用户名：ad-admin@yourdomain (Yopu4708)
6. 选中“显示密码”复选框，然后记录此用户帐户的密码以供将来使用。
7. 单击“创建” 。
8. 重复上述 3 个步骤，创建以下 2 个新用户。
   - 名称：app-admin
   - 用户名：app-admin@yourdomain (Buju4319)
   - 名称：app-user
   - 用户名：app-user@yourdomain  (Nonu4001).

9. 打开新的浏览器窗口，并使用新创建的 ad-admin 帐户登录 Azure 门户。
10. 在“更新密码”页的“当前密码”框中，输入系统生成的密码。 
11. 在“新密码”和“确认密码”框中，输入自己的密码。
12. 单击“更新密码并登录”。

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>使用 Azure Active Directory 配置 SQL 数据库集成

1. 在左侧菜单中单击“更多服务”，在筛选器文本框中键入 sql，然后选择“SQL 服务器”。
2. 在“SQL 服务器”页上，单击 SQL 数据库服务器。
3. 在服务器“概述”页“概要”窗格的“Active Directory 管理员”下，单击“未配置”。
4. 在“Active Directory 管理员”页上单击“设置管理员”。
5. 选择“ad-admin”Azure Active Directory 帐户（或其他预先存在的帐户，例如自己的帐户），作为 Azure SQL 数据库服务器的服务器管理员。
6. 单击“选择”。
7. 单击“保存” 。

## <a name="create-users-with-permissions-for-your-database"></a>创建具有数据库权限的用户

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

使用 SQL Server Management Studio 连接到数据库，并创建用户帐户。 这些用户帐户将自动复制到辅助服务器。 如果进行连接的客户端所在的 IP 地址尚未配置防火墙，则需要配置防火墙规则。 有关步骤，请参阅[使用 Azure 门户创建 SQL DB](sql-database-get-started-portal.md)。

1. 打开 SQL Server Management Studio。
2. 将“身份验证”模式更改为“Active Directory 密码验证”。
3. 使用新设计的 Azure Active Directory 服务器管理员帐户连接到服务器。 
4. 在对象资源管理器中，展开“系统数据库”，右键单击“mySampleDatabase”，然后单击“新建查询”。
5. 在查询窗口中执行以下查询，以便在数据库中创建用户帐户，同时向两个管理帐户授予 db_owner 权限。 将域名的占位符替换为你的域。

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>创建数据库级防火墙

使用 SQL Server Management Studio 为数据库创建数据库级防火墙规则。 此数据库级防火墙规则将自动复制到辅助服务器。 出于测试目的，可以为所有 IP 地址（包括 0.0.0.0 和 255.255.255.255）创建防火墙规则，可以为已创建有服务器防火墙规则的单个 IP 地址创建防火墙规则，或者可以为要用于本教程测试的计算机 IP 地址配置一个或多个防火墙规则。  

- 在打开的查询窗口中，将之前的查询替换为以下查询，将 IP 地址替换为环境中相应的 IP 地址。 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>创建故障转移组 

选择故障转移区域，在该区域中创建一个空服务器，然后在现有服务器和新的空服务器之间创建故障转移组。

1. 填充变量。

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. 在故障转移区域中创建空的备份服务器。

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. 创建故障转移组。

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. 将数据库添加到故障转移组

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>将空的备份服务器添加到域

1. 在 Azure 门户的左侧菜单中单击“更多服务”，在筛选器文本框中键入 sql，然后选择“SQL 服务器”。
2. 在“SQL 服务器”页上，单击新的 SQL 数据库灾难恢复服务器。
3. 在服务器“概述”页“概要”窗格的“Active Directory 管理员”下，单击“未配置”。
4. 在“Active Directory 管理员”页上单击“设置管理员”。
5. 选择“ad-admin”Azure Active Directory 帐户（或其他预先存在的帐户，例如自己的帐户），作为新 Azure SQL 数据库灾难恢复服务器的服务器管理员。
6. 单击“选择”。
7. 单击“保存” 。

## <a name="prepare-client-tier"></a>准备客户端层

1. 使用故障转移配置文件 AWProfile 创建 TM 配置文件。
2. 配置监视

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>部署 Java 应用程序并连接到数据库

<In progress>请参阅[连接 Java](sql-database-connect-query-java.md)。

<TO DO: change user to app-user>

1. 安装 Java 8。
2. 安装 Maven。
3. 创建 Maven 项目。
4. 将以下内容添加到 pom.xml 

   - 依赖项

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - 语言级别

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - 支持 JAR 中清单文件的生成选项

      ```java
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
5. 将以下内容添加到 App.java 文件：

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

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
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

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
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

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
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
6. 保存文件。

## <a name="compile-and-run"></a>编译和运行

1. 转到控制台并执行

   ```java
   mvn package
   ```
2. 完成后执行，运行应用程序（将运行大约 1 小时，除非手动停止运行）：

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   如果运行成功，输出将如下所示：

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>异地分布式数据库教程 ##
   #######################################

   1. 成功插入在主终结点上，成功从辅助终结点上读取
   2. 成功插入在主终结点上，成功从辅助终结点上读取
   3. 成功插入在主终结点上，成功从辅助终结点上读取

## <a name="perform-dr-drill"></a>执行 DR 演练

1. 使用强制故障转移调用 FG 的手动故障转移。 如果无法接受演练期间的数据丢失，则应该删除 -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    禁用 TM 配置文件中的主终结点，触发终结点故障转移 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    重新运行应用程序。


## <a name="relocate-application-to-primary-region"></a>重新将应用程序定位到主要区域

1.    调用 FG 适合手动执行的故障转移。 未指定 -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    禁用 TM 配置文件中的辅助终结点 (webapp2ep)，触发终结点故障转移 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    重新运行应用程序。

## <a name="troubleshoot-failover"></a>故障转移排查 

找出现在作为主要区域的区域，确保发生了故障转移。 如果是主要区域，将显示角色。

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>后续步骤 

- 尽快进入附近的影院
