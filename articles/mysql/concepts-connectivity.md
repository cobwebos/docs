---
title: 暂时性连接错误-Azure Database for MySQL
description: 了解如何处理暂时性连接错误并高效地连接到 Azure Database for MySQL。
keywords: mysql 连接，连接字符串，连接问题，暂时性错误，连接错误，高效连接
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d91048c52794869b5db1467a3456ca58e703d1ad
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719919"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>处理暂时性错误并高效连接到 Azure Database for MySQL

本文介绍如何处理暂时性错误并高效地连接到 Azure Database for MySQL。

## <a name="transient-errors"></a>일시적 오류

일시적 결함이라고도 하는 일시적 오류는 자체적으로 해결되는 오류입니다. 가장 일반적으로 이러한 오류는 삭제되는 데이터베이스 서버에 대한 연결로 나타납니다. 또한 서버에 대한 새 연결을 열 수 없습니다. 예를 들어 하드웨어 또는 네트워크 오류가 발생할 때 일시적 오류가 발생할 수 있습니다. 另一个原因可能是要推出的 PaaS 服务的新版本。在60秒内，系统会自动降低其中的大多数事件。 클라우드에서 애플리케이션을 설계하고 개발하는 가장 좋은 방법은 일시적 오류를 예상하는 것입니다. 언제든지 모든 구성 요소에서 발생할 수 있으며 이러한 상황을 처리할 수 있는 적절한 논리를 갖추고 있다고 가정합니다.

## <a name="handling-transient-errors"></a>일시적 오류 처리

일시적 오류는 다시 시도 논리를 사용하여 처리해야 합니다. 고려해야 할 상황은 다음과 같습니다.

* 연결을 열려고 하면 오류가 발생합니다.
* 서버 쪽에서 유휴 연결이 삭제됩니다. 명령을 실행하려고 하면 실행할 수 없습니다.
* 현재 명령을 실행하는 활성 연결이 삭제됩니다.

첫 번째와 두 번째 경우는 매우 간단하게 처리할 수 있습니다. 연결을 열기 위해 다시 시도합니다. 성공하면 일시적 오류가 시스템에서 완화됩니다. Azure Database for MySQL을 다시 사용할 수 있습니다. 연결을 다시 시도하기 전에 기다리는 것이 좋습니다. 초기 다시 시도가 실패하면 잠시 그대로 있습니다. 이렇게 하면 오류 상황을 극복하기 위해 시스템에서 사용 가능한 모든 리소스를 사용할 수 있습니다. 따라야 할 좋은 패턴은 다음과 같습니다.

* 5초 동안 기다린 후에 다시 시도합니다.
* 다시 시도할 때마다 대기 시간이 최대 60초까지 기하급수적으로 증가합니다.
* 애플리케이션에서 작업이 실패한 것으로 간주하는 시점의 최대 재시도 횟수를 설정합니다.

활성 트랜잭션과의 연결에 실패하면 복구를 제대로 처리하기가 더 어렵습니다. 두 가지 경우가 있습니다. 트랜잭션이 실제로 읽기 전용인 경우 연결을 다시 열고 트랜잭션을 다시 시도하는 것이 안전합니다. 不过，如果事务也是写入数据库，则必须确定事务是否已回滚，或者是否在暂时性错误发生之前成功。 在这种情况下，你可能只是从数据库服务器接收到了提交确认。

이 작업을 수행하는 한 가지 방법은 클라이언트에서 모든 다시 시도에 사용되는 고유 ID를 생성하는 것입니다. 이 고유 ID를 트랜잭션의 일환으로 서버에 전달하고, 고유 제약 조건이 있는 열에 이를 저장합니다. 이렇게 하면 트랜잭션을 안전하게 다시 시도할 수 있습니다. 如果以前的事务已回滚并且系统中不存在客户端生成的唯一 ID，则会成功。 이전 트랜잭션이 성공적으로 완료되었으므로 고유 ID가 이전에 저장된 경우 중복 키 위반을 나타내는 데 실패합니다.

프로그램에서 타사 미들웨어를 통해 Azure Database for MySQL과 통신하는 경우 공급업체에 문의하여 일시적 오류에 대한 다시 시도 논리가 미들웨어에 포함되어 있는지를 확인해야 합니다.

다시 시도 논리는 테스트해야 합니다. 例如，尝试执行你的代码，同时增加或减少 Azure Database for MySQL 服务器的计算资源。 애플리케이션에서 이 작업 중에 발생하는 짧은 가동 중지 시간을 아무 문제 없이 처리해야 합니다.

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

## <a name="next-steps"></a>다음 단계

* [Azure Database for MySQL에 대한 연결 문제 해결](howto-troubleshoot-common-connection-issues.md)
