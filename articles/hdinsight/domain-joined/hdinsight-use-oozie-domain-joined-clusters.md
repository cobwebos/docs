---
title: 已加入域的 HDInsight 群集中的 Hadoop Oozie 工作流
description: 使用基于 Linux 并已加入域的 HDInsight 企业安全性套餐中的 Hadoop Oozie。 了解如何定义 Oozie 工作流，并提交 Oozie 作业。
services: hdinsight
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: omidm
ms.openlocfilehash: 928f6adbb348683a110f7da9b20efaae998290ca
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972207"
---
#<a name="run-apache-oozie-in-domain-joined-hdinsight-hadoop-clusters"></a>在已加入域的 HDInsight Hadoop 群集中运行 Apache Oozie
Oozie 是一个管理 Hadoop 作业的工作流和协调系统。 Oozie 与 Hadoop 堆栈集成，并支持以下作业：
- Apache MapReduce
- Apache Pig
- Apache Hive
- Apache Sqoop

还可以使用 Oozie 来计划特定于某系统的作业，例如 Java 程序或 shell 脚本

##<a name="prerequisites"></a>先决条件：
- 已加入域的 HDInsight Hadoop 群集。 请参阅[配置已加入域的 HDInsight 群集](./apache-domain-joined-configure-using-azure-adds.md)

    > [!NOTE]
    > 若要查看在未加入域的群集上使用 Oozie 的详细说明，请参阅[此文](../hdinsight-use-oozie-linux-mac.md)

##<a name="connecting-to-domain-joined-cluster"></a>连接到已加入域的群集
有关 SSH 的详细信息，请参阅[身份验证：已加入域的 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)。
- 使用 SSH 连接到 HDInsight 群集：
     ```bash
    ssh [DomainUserName]@<clustername>-ssh.azurehdinsight.net
    ```
若要验证 Kerberos 身份验证是否成功，请使用 `klist` 命令。 如果不成功，请使用 `kinit` 启动 Kerberos 身份验证。

- 登录到 HDInsight 网关，注册访问 Azure Data Lake Store (ADLS) 所需的 oAuth 令牌
     ```bash
     curl -I -u [DomainUserName@Domain.com]:[DomainUserPassword] https://<clustername>.azurehdinsight.net
     ```

    状态响应代码 _200 OK_ 表示注册成功。 如果收到未经授权的响应 (401)，请检查用户名和密码。

## <a name="define-the-workflow"></a>定义工作流
Oozie 工作流定义以 Hadoop 过程定义语言（缩写为 hPDL，一种 XML 过程定义语言）编写。 使用以下步骤定义工作流：

-   设置域用户的工作区：
 ```bash
hdfs dfs -mkdir /user/<DomainUser>
cd /home/<DomainUserPath>
cp /usr/hdp/<ClusterVersion>/oozie/doc/oozie-examples.tar.gz .
tar -xvf oozie-examples.tar.gz
hdfs dfs -put examples /user/<DomainUser>/
 ```
将 `DomainUser` 替换为域用户名。 将 `DomainUserPath` 替换为域用户的主目录路径。 将 `ClusterVersion` 替换为你的群集 HDP 版本。

-   使用以下语句创建并编辑新文件：
 ```bash
nano workflow.xml
 ```

- 打开 nano 编辑器后，输入以下 XML 作为文件内容：
 ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <workflow-app xmlns="uri:oozie:workflow:0.4" name="map-reduce-wf">
       <credentials>
          <credential name="metastore_token" type="hcat">
             <property>
                <name>hcat.metastore.uri</name>
                <value>thrift://hn0-<clustername>.<Domain>.com:9083</value>
             </property>
             <property>
                <name>hcat.metastore.principal</name>
                <value>hive/_HOST@<Domain>.COM</value>
             </property>
          </credential>
          <credential name="hs2-creds" type="hive2">
             <property>
                <name>hive2.server.principal</name>
                <value>${jdbcPrincipal}</value>
             </property>
             <property>
                <name>hive2.jdbc.url</name>
                <value>${jdbcURL}</value>
             </property>
          </credential>
       </credentials>
       <start to="mr-test" />
       <action name="mr-test">
          <map-reduce>
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <prepare>
                <delete path="${nameNode}/user/${wf:user()}/examples/output-data/mrresult" />
             </prepare>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
                <property>
                   <name>mapred.mapper.class</name>
                   <value>org.apache.oozie.example.SampleMapper</value>
                </property>
                <property>
                   <name>mapred.reducer.class</name>
                   <value>org.apache.oozie.example.SampleReducer</value>
                </property>
                <property>
                   <name>mapred.map.tasks</name>
                   <value>1</value>
                </property>
                <property>
                   <name>mapred.input.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/input-data/text</value>
                </property>
                <property>
                   <name>mapred.output.dir</name>
                   <value>/user/${wf:user()}/${examplesRoot}/output-data/mrresult</value>
                </property>
             </configuration>
          </map-reduce>
          <ok to="myHive2" />
          <error to="fail" />
       </action>
       <action name="myHive2" cred="hs2-creds">
          <hive2 xmlns="uri:oozie:hive2-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <jdbc-url>${jdbcURL}</jdbc-url>
             <script>${hiveScript2}</script>
             <param>hiveOutputDirectory2=${hiveOutputDirectory2}</param>
          </hive2>
          <ok to="myHive" />
          <error to="fail" />
       </action>
       <action name="myHive" cred="metastore_token">
          <hive xmlns="uri:oozie:hive-action:0.2">
             <job-tracker>${jobTracker}</job-tracker>
             <name-node>${nameNode}</name-node>
             <configuration>
                <property>
                   <name>mapred.job.queue.name</name>
                   <value>${queueName}</value>
                </property>
             </configuration>
             <script>${hiveScript1}</script>
             <param>hiveOutputDirectory1=${hiveOutputDirectory1}</param>
          </hive>
          <ok to="end" />
          <error to="fail" />
       </action>
       <kill name="fail">
          <message>Oozie job failed, error message[${wf:errorMessage(wf:lastErrorNode())}]</message>
       </kill>
       <end name="end" />
    </workflow-app>
 ```
将 `clustername` 替换为群集的名称。 

若要保存文件，请按 Ctrl+X，输入 `Y`，再按 **Enter**。

工作流分为两部分：
*   凭据部分：凭据部分接收用于验证 oozie 操作的凭据：

    在此示例中，对 Hive 操作进行身份验证。 若要了解详细信息，请参阅[此文]( https://oozie.apache.org/docs/4.2.0/DG_ActionAuthentication.html)。

    凭据服务允许 oozie 操作模拟用户访问 Hadoop 服务。

*   操作部分：此处有三个操作 — map-reduce、hive server 2 操作和 hive server 1 操作：

    map-reduce 针对输出聚合字数统计的映射化简运行来自 oozie 包的示例。

    hive server 2 和 hive server 1 操作在随 HDInsight 提供的 hivesample 表上执行简单查询。

    hive 操作借助操作元素中的关键字 `cred`，使用凭据部分中定义的凭据进行身份验证

- 使用以下命令将 `workflow.xml` 文件复制到 `/user/<domainuser>/examples/apps/map-reduce/workflow.xml`：
     ```bash
    hdfs dfs -put workflow.xml /user/<domainuser>/examples/apps/map-reduce/workflow.xml
     ```

    将 `domainuser` 替换为你的域用户名。

## <a name="define-the-properties-file-for-the-oozie-job"></a>定义 Oozie 作业的属性文件

1.  使用以下语句为作业属性创建并编辑新文件：
     ```bash
    nano job.properties
     ```

2.   打开 nano 编辑器后，使用以下 XML 作为该文件的内容：

    ```bash
        nameNode=adl://home
        jobTracker=headnodehost:8050
        queueName=default
        examplesRoot=examples
        oozie.wf.application.path=${nameNode}/user/[domainuser]/examples/apps/map-reduce/workflow.xml
        hiveScript1=${nameNode}/user/${user.name}/countrowshive1.hql
        hiveScript2=${nameNode}/user/${user.name}/countrowshive2.hql
        oozie.use.system.libpath=true
        user.name=[domainuser]
        jdbcPrincipal=hive/hn0-<ClusterShortName>.<Domain>.com@<Domain>.COM
        jdbcURL=[jdbcurlvalue]
        hiveOutputDirectory1=${nameNode}/user/${user.name}/hiveresult1
        hiveOutputDirectory2=${nameNode}/user/${user.name}/hiveresult2
    ```
    

   * 将 `domainuser` 替换为你的域用户名。
   * 将 `ClusterShortName` 替换为群集的短名称。 如果 clustername 为 https://sechadoopcontoso.azurehdisnight.net，则 `clustershortname` 为群集的前六个字母：sechad
   * 将 `jdbcurlvalue` 替换为 hive config 中的 JDBC url。例如，jdbc:hive2://headnodehost:10001/;transportMode=http。
    
   * 若要保存文件，请按 Ctrl+X，输入 `Y`，再按 **Enter**。

   * 运行 oozie 作业时，必须在本地提供此属性文件

## <a name="creating-custom-hive-scripts-for-the-oozie-job"></a>为 Oozie 作业创建自定义 hive 脚本
可以为 hive server 1 和 hive server 2 创建 2 个 hive 脚本，如下所示：
-   Hive Server 1 文件：
1.  使用以下语句为 hive server 1 操作创建并编辑文件：
    ```bash
    nano countrowshive1.hql
    ```

2.  创建脚本
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemake from hivesampletable limit 2;
    ```

3.  将文件保存到 Hdfs
    ```bash
    hdfs dfs -put countrowshive1.hql countrowshive1.hql
    ```

-   Hive Server 2 文件：
1.  使用以下语句为 hive server 2 操作创建并编辑字段：
    ```bash
    nano countrowshive2.hql
    ```

2.  创建脚本
    ```sql
    INSERT OVERWRITE DIRECTORY '${hiveOutputDirectory1}' 
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    select devicemodel from hivesampletable limit 2;
    ```

3.  将文件保存到 Hdfs
    ```bash
    hdfs dfs -put countrowshive2.hql countrowshive2.hql
    ```

## <a name="submission-of-oozie-jobs"></a>提交 Oozie 作业
为已加入域的群集提交 oozie 作业与在未加入域的群集中提交 oozie 作业类似。

有关详细信息，请参阅[提交和管理作业](../hdinsight-use-oozie-linux-mac.md)。

## <a name="results-from-oozie-job-submission"></a>Oozie 作业提交结果
由于 oozie 作业是代表用户运行的，因此，Yarn 和 Ranger 审核日志均显示作业以模拟用户身份执行。 oozie 作业的 CLI 输出如下所示：


```bash
    Job ID : 0000015-180626011240801-oozie-oozi-W
    ------------------------------------------------------------------------------------------------
    Workflow Name : map-reduce-wf
    App Path      : adl://home/user/alicetest/examples/apps/map-reduce/wf.xml
    Status        : SUCCEEDED
    Run           : 0
    User          : alicetest
    Group         : -
    Created       : 2018-06-26 19:25 GMT
    Started       : 2018-06-26 19:25 GMT
    Last Modified : 2018-06-26 19:30 GMT
    Ended         : 2018-06-26 19:30 GMT
    CoordAction ID: -
    
    Actions
    ------------------------------------------------------------------------------------------------
    ID                      Status  Ext ID          ExtStatus   ErrCode
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@:start:    OK  -           OK      -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@mr-test    OK  job_1529975666160_0051  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive2    OK  job_1529975666160_0053  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@myHive OK  job_1529975666160_0055  SUCCEEDED   -
    ------------------------------------------------------------------------------------------------
    0000015-180626011240801-oozie-oozi-W@end    OK  -           OK      -
    -----------------------------------------------------------------------------------------------
```

*    hive server 2 操作的 Ranger 审核日志显示 oozie 代表用户执行操作。 Ranger 和 Yarn 视图仅对群集管理员可见。

## <a name="configuration-of-user-authorization-in-oozie"></a>在 Oozie 中配置用户授权
Oozie 本身具有用户授权配置，可以阻止用户停止、终止其他用户的作业。 通过将 `oozie.service.AuthorizationService.security.enabled` 设置为 `true`，即可启用此配置。 

有关更多详细信息，请参阅 Oozie 文档部分 — [User Authorization Configuration]( https://oozie.apache.org/docs/3.2.0-incubating/AG_Install.html)（用户授权配置）：

对于不提供/不支持 Ranger 插件的组件（如 hive server 1），只能进行粗粒度的 hdfs 授权。 细粒度授权只能通过 Ranger 插件进行。

## <a name="oozie-web-ui"></a>Oozie Web UI
Oozie Web UI 提供基于 Web 的视图来显示群集上 Oozie 作业的状态。 在已加入域的群集中，需要：

1. 添加[边缘节点](../hdinsight-apps-use-edge-node.md)并启用 [SSH Kerberos 身份验证](../hdinsight-hadoop-linux-use-ssh-unix.md)

2. 按照 [Oozie Web UI](../hdinsight-use-oozie-linux-mac.md) 步骤启用 SSH 隧道以连接到边缘节点并访问 Web UI。

## <a name="next-steps"></a>后续步骤
* [在基于 Linux 的 Azure HDInsight 中将 Oozie 与 Hadoop 配合使用以定义和运行工作流](../hdinsight-use-oozie-linux-mac.md)
* [使用基于时间的 Oozie 协调器](../hdinsight-use-oozie-coordinator-time.md)
* [在已加入域的 HDInsight 群集上使用 SSH 运行 Hive 查询](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)。
