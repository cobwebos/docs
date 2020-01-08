---
title: 暂时性连接错误-Azure Database for MySQL
description: 了解如何处理暂时性连接错误并高效地连接到 Azure Database for MySQL。
keywords: mysql 连接，连接字符串，连接问题，暂时性错误，连接错误，高效连接
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 92e213a87796247128e7e3810db99fde8525e12a
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659213"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>处理暂时性错误并高效连接到 Azure Database for MySQL

本文介绍如何处理暂时性错误并高效地连接到 Azure Database for MySQL。

## <a name="transient-errors"></a>暂时性错误

暂时性错误也称为暂时性故障，是一种可以自行解决的错误。 这些错误往往表现为与数据库服务器的连接断开。 此外，无法与服务器建立的新连接。 例如，在发生硬件或网络故障时，可能会出现暂时性错误。 另一个原因可能是要推出的 PaaS 服务的新版本。在60秒内，系统会自动降低其中的大多数事件。 设计和开发云中的应用程序时，预料到会出现暂时性错误是最佳做法。 假设这些错误随时可能在任意组件中发生，并部署相应的逻辑来应对这种情况。

## <a name="handling-transient-errors"></a>处理暂时性错误

应使用重试逻辑来处理暂时性错误。 必须考虑的情况包括：

* 尝试打开连接时出错
* 服务器端的空闲连接断开。 尝试发出的命令无法执行
* 当前正在用于执行命令的活动连接断开。

第一种和第二种情况的处理方式非常直接。 可以再试打开连接。 如果连接成功，则表示系统解决了暂时性错误。 可以再次使用 Azure Database for MySQL。 我们建议在重试连接之前等待一段时间。 如果初始重试失败，则回退。 这样，系统便可以使用所有可用资源来解决错误局面。 遵循的良好模式是：

* 在首次重试之前等待 5 秒。
* 对于每次后续重试，以指数级增大等待时间，最长为 60 秒。
* 设置最大重试次数，达到该次数时，应用程序认为操作失败。

活动事务的连接失败时，适当地处理恢复会更困难。 存在两种情况：如果事务在性质上是只读的，则可以安全地重新打开连接并重试事务。 不过，如果事务也是写入数据库，则必须确定事务是否已回滚，或者是否在暂时性错误发生之前成功。 在这种情况下，你可能只是从数据库服务器接收到了提交确认。

解决此问题的方法之一是，在客户端上生成一个用于所有重试的唯一 ID。 将此唯一 ID 作为事务的一部分传递给服务器，并将其存储在具有唯一约束的列中。 这样，便可以安全重试事务。 如果以前的事务已回滚并且系统中不存在客户端生成的唯一 ID，则会成功。 如果之前已存储该唯一 ID（因为前一事务已成功完成），则重试将会失败，并指示重复键冲突。

如果程序通过第三方中间件来与 Azure Database for MySQL 通信，请咨询供应商该中间件是否包含暂时性错误的重试逻辑。

请务必测试重试逻辑。 例如，尝试执行你的代码，同时增加或减少 Azure Database for MySQL 服务器的计算资源。 应用程序应可处理此操作期间遇到的短暂停机，而不会出现任何问题。

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>有效地连接到 Azure Database for MySQL

数据库连接是一种有限的资源，因此，充分利用连接池来访问 Azure Database for MySQL 可以优化性能。 以下部分介绍如何使用连接池或持续连接来更有效地访问 Azure Database for MySQL。

## <a name="access-databases-by-using-connection-pooling-recommended"></a>使用连接池访问数据库（推荐）

管理数据库连接可能对应用程序的性能有很大的影响。 若要优化应用程序的性能，目标应该是减少建立连接的次数以及在关键代码路径中建立连接的时间。 强烈建议使用数据库连接池或持久连接连接到 Azure Database for MySQL。 数据库连接池处理数据库连接的创建、管理和分配。 当程序请求数据库连接时，它会划分现有空闲数据库连接的分配，而不是创建新连接。 在程序完成使用数据库连接后，连接将会恢复，以便进一步使用，而不是只是关闭。

为了更好地说明，本文提供了一个使用 JAVA 作为示例的[示例代码](./sample-scripts-java-connection-pooling.md)。 有关详细信息，请参阅[Apache COMMON DBCP](https://commons.apache.org/proper/commons-dbcp/)。

> [!NOTE]
> 服务器配置超时机制，以便在一段时间内关闭处于空闲状态的连接，以释放资源。 请确保设置验证系统，以确保在使用永久性连接时的有效性。 有关详细信息，请参阅在[客户端配置验证系统以确保永久性连接的有效性](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections)。

## <a name="access-databases-by-using-persistent-connections-recommended"></a>使用持久性连接访问数据库（推荐）

持久性连接的概念与连接池的概念类似。 将短连接替换为持久性连接只需对代码进行少量更改，但在许多典型的应用程序方案中，它会影响性能。

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>使用具有短连接的等待和重试机制访问数据库

如果有资源限制，我们强烈建议使用数据库池或持久连接来访问数据库。 如果你的应用程序在你接近并发连接数的上限时使用短连接并遇到连接故障，则可以尝试等待和重试机制。 你可以设置适当的等待时间，并在第一次尝试后等待时间较短。 此后，你可以尝试多次等待事件。

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>配置客户端中的验证机制以确认永久性连接的有效性

服务器配置超时机制，以便在一段时间内关闭处于空闲状态的连接，以释放资源。 当客户端再次访问数据库时，这相当于在客户端和服务器之间创建新的连接请求。 若要确保在使用连接的过程中连接的有效性，请在客户端上配置验证机制。 如以下示例中所示，可以使用 Tomcat JDBC 连接池来配置此验证机制。

通过设置 TestOnBorrow 参数，在有新请求时，连接池将自动验证任何可用空闲连接的有效性。 如果此类连接有效，则它直接返回，否则连接池将收回连接。 然后，连接池创建一个新的有效连接并将其返回。 此过程可确保有效地访问数据库。 

有关特定设置的信息，请参阅[JDBC 连接池官方简介文档](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes)。 主要需要设置以下三个参数： TestOnBorrow （设置为 true）、ValidationQuery （设置为 SELECT 1）和 ValidationQueryTimeout （设置为1）。 具体的示例代码如下所示：

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
            // set other usefull pool properties.
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

* [解决 Azure Databases for MySQL 的连接问题](howto-troubleshoot-common-connection-issues.md)
