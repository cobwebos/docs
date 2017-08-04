---
title: "HBase 故障排除 - Azure HDInsight | Microsoft Docs"
description: "排查区域链中出现缺口的原因。"
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 29b1776d6b0c456515738aae3c5fd836c9a0295d
ms.contentlocale: zh-cn
ms.lasthandoff: 07/11/2017

---

# <a name="hbase-troubleshooting"></a>HBASE 故障排除

本文介绍了处理 Apache Ambari 中的 HBASE 有效负载时遇到的最常见问题及其解决方法。

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>如何对多个未分配区域运行 hbck 命令报告

HBase 用户在运行“hbase hbck”命令时看到“多个区域未分配或者区域链中有缺口”是一个常见问题。

用户会在 HBase Master UI 上看到在所有区域服务器间未均衡的区域的计数。 此后，用户可以运行“hbase hbck”命令，并且应当会在区域链中看到缺口。

用户应当首先纠正分配，因为缺口可能是由那些脱机区域导致的。 

请遵循以下步骤使未分配的区域恢复为正常状态：

1. 使用 SSH 登录到 HDInsight HBase 群集。
1. 运行“hbase zkcli”命令来与 zookeeper shell 进行连接。
1. 运行“rmr /hbase/regions-in-transition”或“rmr /hbase-unsecure/regions-in-transition”命令。
1. 使用“exit”命令退出“hbase zkcli”shell。
1. 打开 Ambari UI 并从 Ambari 重启 Active HBase Master 服务。
1. 再次运行“hbase hbck”命令（不使用任何其他选项）。

检查步骤 6 中命令的输出并确保分配了所有区域。

---

## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>如何纠正针对区域分配的 hbck 命令的超时问题

### <a name="probable-cause"></a>可能的原因

此处的可能原因可能是长时间内有多个处于“正在转换”状态的区域。 从 HBase Master UI 中来看，那些区域可能显示为脱机。 由于有大量区域正在尝试进行转换，因此，HBase Master 可能会超时并且将无法使那些区域恢复为联机状态。

### <a name="resolution-steps"></a>解决步骤

下面是用于解决 hbck 超时问题的步骤：

1. 使用 SSH 登录到 HDInsight HBase 群集。
1. 运行“hbase zkcli”命令来与 zookeeper shell 进行连接。
1. 运行“rmr /hbase/regions-in-transition”或“rmr /hbase-unsecure/regions-in-transition”命令。
1. 使用“exit”命令退出“hbase zkcli”shell。
1. 打开 Ambari UI 并从 Ambari 重启 Active HBase Master 服务。
1. 再次运行“hbase hbck -fixAssignments”命令，它应当不再超时。

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-an-cluster"></a>如何在群集中强制禁用 HDFS 安全模式

### <a name="issue"></a>问题：

本地 HDFS 在 HDInsight 群集上在安全模式下停止响应。   

### <a name="detailed-description"></a>详细说明：

无法运行如下所示的简单 HDFS 命令：

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

尝试运行以上命令时遇到的错误如下所示：

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>可能的原因：

HDInsight 群集已减少到非常少的节点数以下或接近 HDFS 复制因子。

### <a name="resolution-steps"></a>解决步骤： 

- 使用以下命令报告 HDInsight 群集上的 HDFS 的状态：

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
```

```apache
hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
Safe mode is ON
Configured Capacity: 3372381241344 (3.07 TB)
Present Capacity: 3138625077248 (2.85 TB)
DFS Remaining: 3102710317056 (2.82 TB)
DFS Used: 35914760192 (33.45 GB)
DFS Used%: 1.14%
Under replicated blocks: 0
Blocks with corrupt replicas: 0
Missing blocks: 0
Missing blocks (with replication factor 1): 0

-------------------------------------------------
Live datanodes (8):

Name: 10.0.0.17:30010 (10.0.0.17)
Hostname: 10.0.0.17
Decommission Status : Normal
Configured Capacity: 421547655168 (392.60 GB)
DFS Used: 5288128512 (4.92 GB)
Non DFS Used: 29087272960 (27.09 GB)
DFS Remaining: 387172253696 (360.58 GB)
DFS Used%: 1.25%
DFS Remaining%: 91.85%
Configured Cache Capacity: 0 (0 B)
Cache Used: 0 (0 B)
Cache Remaining: 0 (0 B)
Cache Used%: 100.00%
Cache Remaining%: 0.00%
Xceivers: 2
Last contact: Wed Apr 05 16:22:00 UTC 2017
...

```
- 使用以下命令检查 HDInsight 群集上的 HDFS 的完整性：

```apache
hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
```

```apache
Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
....................................................................................................

....................................................................................................
..................Status: HEALTHY
 Total size:    9330539472 B
 Total dirs:    37
 Total files:   2618
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2535 (avg. block size 3680686 B)
 Minimally replicated blocks:   2535 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          8
 Number of racks:               1
FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds


The filesystem under path '/' is HEALTHY
```

- 如果确定没有缺失、损坏或正在复制的块，或者这些块可以忽略，请运行以下命令使名称节点退出安全模式：

```apache
hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>如何纠正 Apache Phoenix 的 JDBC 或 sqlline 连接问题

### <a name="resolution-steps"></a>解决步骤：

若要与 Apache phoenix 进行连接，必须提供活动 zookeeper 节点的 IP。 确保 sqlline.py 尝试连接的 zookeeper 服务已启动且正在运行。
1. 执行到 HDInsight 群集的 SSH 登录。
1. 尝试运行以下命令：
        
```apache
        "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
```     
    Note: The IP of Active Zooker node can be identified from Ambari UI, by following the links to HBase -> "Quick Links" -> "ZK* (Active)" -> "Zookeeper Info". 

如果 sqlline.py 能够连接到 Apache Phoenix 并且未超时，则运行以下命令来验证 Apache Phoenix 的可用性和运行状况：

```apache
        !tables
        !quit
```      
- 如果以上命令正常运行，则说明没有问题。 用户提供的 IP 可能不正确。
   
    不过，如果命令暂停的时间太长，并且随后引发了下面的错误方法，则继续根据下面的故障排除指南进行操作：

```apache
        Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
```

- 从头节点 (hn0) 运行以下命令来诊断 phoenix SYSTEM.CATALOG 表的情况：

```apache
        hbase shell
        
        count 'SYSTEM.CATALOG'
```        
- 此命令应当返回类似于以下内容的错误： 

```apache
        ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
```
- 遵循以下步骤，通过 Ambari UI 在所有 zookeeper 节点上重启 HMaster 服务：

    a. 转到 HBase 的摘要部分中的“HBase -> Active HBase Master”链接。 
    a. 在“组件”部分中，重启 HBase Master 服务。
    a. 为剩余的“Standby HBase Master”服务重复以上步骤。 

HBase Master 服务可能需要花费长达五分钟才能稳定下来并完成恢复。 等待几分钟后，重复 sqlline.py 命令以确认系统目录表已开启并且可以查询。 

当“SYSTEM.CATALOG”表恢复正常后，Apache Phoenix 的连接问题应当会自动得以解决。


## <a name="what-causes-a-master-server-to-fail-to-start"></a>导致主服务器无法启动的原因

### <a name="error"></a>错误： 

原子重命名失败

### <a name="detailed-description"></a>详细说明：

在启动过程中，HMaster 执行大量的初始化步骤，包括将数据从暂存 (.tmp) 文件夹移动到数据文件夹；它还会查看 WAL（预写日志）文件夹来检查是否存在任何僵死的区域服务器等等。 

在上述所有情况下，它都会对这些文件夹执行基本的“list”命令。 在任何时候，如果它发现这些文件夹任意一个中存在意外的文件，它将引发一个异常并因此不会启动。  

### <a name="probable-cause"></a>可能的原因：

尝试查明文件创建时间线并在区域服务器日志中查看在该时间附近是否有任何进程崩溃（可联系任何 HBase 职员来协助执行此操作）。 这可以帮助我们提供更可靠的机制以避免出现此 bug 并确保正常的进程关闭。

### <a name="resolution-steps"></a>解决步骤：

在这种情况下，请尝试检查调用堆栈并查看哪个文件夹可能导致问题（例如，是 WAL 文件夹还是 .tmp 文件夹）。 然后，通过 Cloud Explorer 或通过 hdfs 命令，尝试找到问题文件 - 通常，这是一个 *-renamePending.json 文件（一个用于在 WASB 驱动程序中实现自动重命名操作的日志文件；由于此实现中的 bug，在发生进程崩溃后，这样的文件可能会保留）。 可以通过 Cloud Explorer 强制删除此文件。 

此外，有时候在此位置中可能会存在 $$$.$$$ 性质的临时文件，无法通过 cloud explorer 看到此文件，只能通过 hdfs ls 命令看到。 可以使用 hdfs 命令“hdfs dfs -rm /<the path>/\$\$\$.\$\$\$”删除此文件。  

执行此操作后，HMaster 应当会立即启动。 

### <a name="error-no-server-address-listed-in"></a>错误：未列出服务器地址 

区域 xxx 的 hbase: meta 中未列出服务器地址

### <a name="detailed-description"></a>详细说明：

客户在 hbase: meta 表未联机的 Linux 群集上遇到问题。 运行 hbck 时报告了“在任何区域中都未发现 hbase: meta 表复制标识 0”。 重启 HBase 后，症状变为 hmaster 无法初始化。 在 hmaster 日志中，它报告了“区域 hbase: backup <region name> 的 hbase: meta 中未列出服务器地址”。  

### <a name="resolution-steps"></a>解决步骤：

- 在 HBase shell 中键入以下命令（根据情况更改实际值），  

```apache
> scan 'hbase:meta'  
```

```apache
> delete 'hbase:meta','hbase:backup <region name>','<column name>'  
```

- 删除 hbase: namespace 的条目，因为扫描 hbase: namespace 表时可能会报告相同的错误。

- 从 Ambari UI 重启主动 HMaster 以使 HBase 恢复运行状态。  

- 在 HBase shell 中运行以下命令来使所有脱机表联机：

```apache 
hbase hbck -ignorePreCheckPermission -fixAssignments 
```

### <a name="further-reading"></a>延伸阅读：

[无法处理 HBase 表](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>错误：

HMaster 超时且出现严重异常，例如 java.io.IOException: 等待分配命名空间表时超时 300000 毫秒

### <a name="detailed-description"></a>详细说明：

当明显有大量表和区域且它们在重启 HMaster 服务后未刷新时，客户遇到此问题 重启失败且出现以上消息。 未发现其他错误。  

### <a name="probable-cause"></a>可能的原因：

这是 HMaster 的一个已知缺陷（因为尚未分配命名空间表，一般群集启动任务可能会花费很长时间并且 HMaster 会关闭），只有存在大量未刷新的数据且五分钟的超时时间不够用时才会出现此问题。
  
### <a name="resolution-steps"></a>解决步骤：

- 访问 Ambari UI，转到“HBase”->“配置”，在自定义 hbase-site.xml 中添加以下设置： 

```apache
Key: hbase.master.namespace.init.timeout Value: 2400000  
```

- 重启所需的服务（主要是 HMaster，可能还有其他 HBase 服务）。  



## <a name="what-causes-a-restart-failure-on-a-region-server"></a>导致在区域服务器上重启失败的原因

### <a name="probable-cause"></a>可能的原因：

首先，可以通过以下最佳做法防止此类情况。 在计划重启 HBase 区域服务器时，建议暂停繁重的工作负荷活动。 如果在关闭过程中应用程序继续与区域服务器进行连接，则这会将区域服务器重启操作拖慢几分钟。 另外，建议用户参考 [HDInsight HBase: How to Improve HBase cluster restart time by Flushing tables](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/)（HDInsight HBase：如何通过刷新表来加快 HBase 群集重启时间）来刷新所有表。

如果用户通过 Ambari UI 在 HBase 区域服务器上启动重启操作， 则他将立即看到区域服务器关闭，但太长时间没有重启。 

下面是在幕后发生的情况： 

1. Ambari 代理将向区域服务器发送停止请求。
1. 然后，Ambari 代理等待 30 秒让区域服务器正常关闭。 
1. 如果客户的应用程序继续与区域服务器进行连接，则区域服务器不会立即关闭，因此，30 秒的超时将很快到期。 
1. 在 30 秒到期后，Ambari 代理将向区域服务器发送强制终止命令 (kill -9)。 可以在 ambari-agent 日志（位于相应辅助角色节点的 /var/log/ 目录中）查看此情况，如下所示：

```apache
         2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
         est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
         f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
         -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
         2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
         /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
         PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
         nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
         2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
         2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
```
由于此突然关闭，尽管区域服务器进程已被终止，但与该进程关联的端口可能没有释放。 此状况可能会导致在启动区域服务器时发生 AddressBindException，如以下日志中所示。 可以在区域服务器启动失败的辅助角色节点上的 /var/log/hbase 目录中的 region-server.log 中对此进行验证。 

```apache

       2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
       java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
       at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
       at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
       at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
       at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
       at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
       
       Caused by: java.lang.reflect.InvocationTargetException
       at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
       at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
       at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
       at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
       ... 5 more
       
       Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
       at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
       at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
       at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
       at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
       at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
       ... 10 more
       
       Caused by: java.net.BindException: Address already in use
       at sun.nio.ch.Net.bind0(Native Method)
       at sun.nio.ch.Net.bind(Net.java:463)
       at sun.nio.ch.Net.bind(Net.java:455)
       at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
       at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
       at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
       ... 15 more
```

### <a name="resolution-steps"></a>解决步骤：

出现这类情况时，可以尝试以下解决方法： 

- 在启动重启之前尝试降低 HBase 区域服务器上的负载。 

- 另外（如果以上步骤没有帮助），请尝试使用以下命令手动重启辅助角色节点上的区域服务器：

```apache
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
      sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
```



