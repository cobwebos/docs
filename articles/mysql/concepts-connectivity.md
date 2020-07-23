---
title: 暂时性连接错误 - Azure Database for MySQL
description: 了解如何处理暂时性连接错误并有效地连接到 Azure Database for MySQL。
keywords: mysql 连接, 连接字符串, 连接问题, 暂时性错误, 连接错误, 有效连接
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cb5adb3787176e3bdbfb7897aa7d7deb9cc2dae7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82100135"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>处理暂时性错误并有效地连接到 Azure Database for MySQL

本文介绍如何处理暂时性错误并有效地连接到 Azure Database for MySQL。

## <a name="transient-errors"></a>暂时性错误

暂时性错误也称为暂时性故障，是一种可以自行解决的错误。 这些错误往往表现为与数据库服务器的连接断开。 此外，无法与服务器建立的新连接。 例如，在发生硬件或网络故障时，可能会出现暂时性错误。 另一个可能的原因是正在推出 PaaS 服务的新版本。系统在 60 秒以内可自动解决其中的大部分事件。 设计和开发云中的应用程序时，预料到会出现暂时性错误是最佳做法。 假设这些错误随时可能在任意组件中发生，并部署相应的逻辑来应对这种情况。

## <a name="handling-transient-errors"></a>处理暂时性错误

应使用重试逻辑来处理暂时性错误。 必须考虑的情况包括：

* 尝试打开连接时出错
* 服务器端的空闲连接断开。 尝试发出的命令无法执行
* 当前正在用于执行命令的活动连接断开。

第一种和第二种情况的处理方式非常直接。 可以再试打开连接。 如果连接成功，则表示系统解决了暂时性错误。 可以再次使用 Azure Database for MySQL。 我们建议在重试连接之前等待一段时间。 如果初始重试失败，则回退。 这样，系统便可以使用所有可用资源来解决错误局面。 遵循的良好模式是：

* 在首次重试之前等待 5 秒。
* 对于每次后续重试，以指数级增大等待时间，最长为 60 秒。
* 设置最大重试次数，达到该次数时，应用程序认为操作失败。

活动事务的连接失败时，适当地处理恢复会更困难。 存在两种情况：如果事务在性质上是只读的，则可以安全地重新打开连接并重试事务。 但是，如果事务也在写入数据库，则必须确定事务在发生暂时性错误之前是已回滚还是已成功。 在这种情况下，你可能尚未从数据库服务器收到提交确认。

解决此问题的方法之一是，在客户端上生成一个用于所有重试的唯一 ID。 将此唯一 ID 作为事务的一部分传递给服务器，并将其存储在具有唯一约束的列中。 这样，便可以安全重试事务。 如果前一事务已回滚，并且客户端生成的唯一 ID 在系统中尚不存在，则重试将会成功。 如果之前已存储该唯一 ID（因为前一事务已成功完成），则重试将会失败，并指示重复键冲突。

如果程序通过第三方中间件来与 Azure Database for MySQL 通信，请咨询供应商该中间件是否包含暂时性错误的重试逻辑。

请务必测试重试逻辑。 例如，尝试在纵向扩展或缩减 Azure Database for MySQL 服务器的计算资源时执行代码。 应用程序应可处理此操作期间遇到的短暂停机，而不会出现任何问题。

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>有效连接到 Azure Database for MySQL

数据库连接是有限的资源，因此，有效利用连接池访问 Azure Database for MySQL 可以优化性能。 以下部分介绍如何使用连接池或持久性连接来更有效地访问 Azure Database for MySQL。

## <a name="access-databases-by-using-connection-pooling-recommended"></a>使用连接池访问数据库（建议方法）

管理数据库连接可能会对整个应用程序的性能造成很大的影响。 若要优化应用程序的性能，目标应是减少建立连接的次数，以及在关键代码路径中建立连接的时间。 我们强烈建议使用数据库连接池或持久性连接来与 Azure Database for MySQL 建立连接。 数据库连接池将处理数据库连接的创建、管理和分配。 当某个程序请求数据库连接时，它会优先分配现有的空闲数据库连接，而不是创建新的连接。 该程序用完数据库连接后，该连接将会恢复，以供进一步使用，而不是直接关闭。

为方便演示，本文提供了一段使用 JAVA 的[示例代码](./sample-scripts-java-connection-pooling.md)。 有关详细信息，请参阅 [Apache 通用 DBCP](https://commons.apache.org/proper/commons-dbcp/)。

> [!NOTE]
> 服务器将配置一个超时机制，以便在某个连接空闲一段时间后将其关闭，从而释放资源。 请务必设置验证系统，以确保在使用持久性连接时这些连接保持有效性。 有关详细信息，请参阅[在客户端中配置验证系统以确保持久性连接的有效性](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)。

## <a name="access-databases-by-using-persistent-connections-recommended"></a>使用持久性连接访问数据库（建议方法）

持久性连接的概念类似于连接池的概念。 将短期连接替换为持久性连接只需对代码进行轻微的更改，但在许多典型的应用方案中，这种做法可以大幅提高性能。

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>结合短期连接使用等待和重试机制访问数据库

如果资源有限制，我们强烈建议使用数据库池或持久性连接来访问数据库。 如果应用程序使用短期连接，并在即将达到并发连接数的上限时遇到连接故障，则你可以尝试等待和重试机制。 可以设置适当的等待时间，在第一次尝试后使用较短的等待时间。 然后，可以尝试等待事件多次。

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>在客户端中配置验证机制以确认持久性连接的有效性

服务器配置超时机制，以便在一段时间内关闭处于空闲状态的连接，以释放资源。 当客户端再次访问数据库时，这相当于在客户端和服务器之间创建新的连接请求。 为了确保在使用连接过程中的连接有效性，请在客户端中配置验证机制。 如以下示例中所示，可以使用 Tomcat JDBC 连接池来配置此验证机制。

设置 TestOnBorrow 参数后，当有新的请求时，连接池会自动验证任何可用空闲连接的有效性。 如果此类连接有效，它会直接返回，否则连接池将收回连接。 然后，连接池会创建新的有效连接并将其返回。 此过程可确保有效访问数据库。 

有关具体设置的信息，请参阅 [JDBC 连接池官方简介文档](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)。 主要需要设置以下三个参数： TestOnBorrow （设置为 true）、ValidationQuery （设置为 SELECT 1）和 ValidationQueryTimeout （设置为1）。 具体的示例代码如下所示：

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>后续步骤

* [排查 Azure Databases for MySQL 的连接问题](howto-troubleshoot-common-connection-issues.md)
