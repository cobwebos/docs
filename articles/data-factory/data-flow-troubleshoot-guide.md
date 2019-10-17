---
title: 排查 Azure 数据工厂数据流问题 |Microsoft Docs
description: 了解如何对 Azure 数据工厂中的数据流问题进行故障排除。
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: 5cf4773ac781ae51a60ef7d987c3dc324c125d95
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387730"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>排查 Azure 数据工厂数据流问题

本文探讨了 Azure 数据工厂中数据流的常见故障排除方法。

## <a name="common-errors-and-messages"></a>常见错误和消息

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>错误消息： DF-SYS-01： databricks. AzureException： StorageException：指定的容器不存在。 "%1"，指定的容器不存在。

- **症状**：由于容器不存在，数据预览、调试和管道数据流执行失败

- **原因**：数据集包含存储中不存在的容器

- **解决方法**：确保在数据集中引用的容器存在

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>错误消息： DF-SYS-01： AssertionError： assertion 失败：检测到冲突的目录结构。 可疑路径

- **症状**：在使用 Parquet 文件的源转换中使用通配符时

- **原因**：通配符语法不正确或无效

- **解决方法**：检查在源转换选项中使用的通配符语法

### <a name="error-message-df-src-002-container-container-name-is-required"></a>错误消息： DF-SRC-002： "container" （容器名称）是必需的

- **症状**：由于容器不存在，数据预览、调试和管道数据流执行失败

- **原因**：数据集包含存储中不存在的容器

- **解决方法**：确保在数据集中引用的容器存在

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>错误消息： DF-a-001： PrimaryKeyValue 具有不兼容的类型 IntegerType 和 StringType

- **症状**：由于容器不存在，数据预览、调试和管道数据流执行失败

- **原因**：尝试在数据库接收器中插入错误的主键类型时发生

- **解决方法**：使用派生列转换用于数据流中的主键的列，使其与目标数据库的数据类型相匹配

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>错误消息： DF-SYS-01： SQLServerException：到主机 xxxxx.database.windows.net 端口1433的 TCP/IP 连接已失败。 错误： "xxxx.database.windows.net"。 验证连接属性。 请确保在主机上运行 SQL Server 实例，并在端口上接受 TCP/IP 连接。 请确保防火墙未阻止到端口的 TCP 连接。

- **症状**：无法通过数据库源或接收器预览数据或执行管道

- **原因**：数据库受防火墙保护

- **解决方法**：打开对数据库的防火墙访问权限

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>错误消息： DF-SYS-01： SQLServerException：数据库中已存在一个名为 "xxxxxx" 的对象。

- **症状**：接收器无法创建表

- **原因**：目标数据库中已存在具有相同名称的现有表名称，这些名称在源或数据集中定义

- **解决方法**：更改尝试创建的表的名称

## <a name="general-troubleshooting-guidance"></a>一般故障排除指南

1. 检查数据集连接的状态。 在每个源和接收器转换中，访问使用的每个数据集的链接服务并测试连接。
2. 从数据流设计器检查文件和表连接的状态。 切换调试，并单击源转换上的 "数据预览"，以确保能够访问数据。
3. 如果所有内容都从数据预览中看起来很好，请进入管道设计器，并将数据流置于管道活动中。 调试用于端到端测试的管道。

## <a name="next-steps"></a>后续步骤

有关故障排除的详细信息，请尝试以下资源：

*  [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [数据工厂功能请求](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 视频](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 论坛](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [用于数据工厂的 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [有关数据工厂的 Twitter 信息](https://twitter.com/hashtag/DataFactory)
*  [ADF 映射数据流性能指南](concepts-data-flow-performance.md)
