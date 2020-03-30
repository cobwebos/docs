---
title: 在 Azure 虚拟机上安装 TmaxSoft 开放帧
description: 使用 Azure 虚拟机 （VM） 上的 TmaxSoft OpenFrame 环境重新托管 IBM z/OS 主机工作负载。
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436048"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>在 Azure 上安装 TmaxSoft 开放帧

了解如何在 Azure 上设置适合开发、演示、测试或生产工作负载的 OpenFrame 环境。 本教程将引导您完成每一步。

OpenFrame 包括多个组件，这些组件在 Azure 上创建大型机仿真环境。 例如，OpenFrame 在线服务将大型机中间件（如 IBM 客户信息控制系统 （CICS））和 OpenFrame Batch）与其 TJES 组件替换 IBM 主机的作业输入子系统 （JES）。

OpenFrame 适用于任何关系数据库，包括 Oracle 数据库、微软 SQL 服务器、IBM Db2 和 MySQL。 OpenFrame 的安装使用 TmaxSoft Tibero 关系数据库。 OpenFrame 和 Tibero 都在 Linux 操作系统上运行。 本教程安装 CentOS 7.3，尽管您可以使用其他支持的 Linux 发行版。OpenFrame 应用程序服务器和 Tibero 数据库安装在一台虚拟机 （VM） 上。

本教程将引导您完成 OpenFrame 套件组件的安装。 有些必须单独安装。

主要开放框架组件：

- 所需的安装包。
- 蒂贝罗数据库。
- OpenFrame 中的应用程序使用开放数据库连接 （ODBC） 与 Tibero 数据库进行通信。
- OpenFrame Base，管理整个系统的中间件。
- OpenFrame 批处理，替换大型机的批处理系统的解决方案。
- TACF，一个控制用户访问系统和资源的服务模块。
- ProSort，批量事务的排序工具。
- OFCOBOL，一个解释大型机COBOL程序的编译器。
- OFASM，一个解释大型机的汇编程序的编译器。
- OpenFrame 服务器类型 C （OSC ），取代大型机中间件和 IBM CICS 的解决方案。
- Java企业用户解决方案 （JEUS），一种经过 Java 企业版 6 认证的 Web 应用程序服务器。
- OFGW，提供 3270 侦听器的 OpenFrame 网关组件。
- OFManager，一种在 Web 环境中提供 OpenFrame 操作和管理功能的解决方案。

其他必需的 OpenFrame 组件：

- OSI，取代大型机中间件和IMS DC的解决方案。
- TJES，提供大型机 JES 环境的解决方案。
- OFTSAM，使 （V） SAM 文件能够在打开的系统中使用的解决方案。
- OFHiDB，取代大型机的IMS DB的解决方案。
- OFPLI，一个解释大型机PL/I程序的编译器。
- PROTRIEVE，一个执行大型机语言CA-Easytrieve的解决方案。
- OFMiner，一种分析大型机资产，然后将它们迁移到 Azure 的解决方案。

## <a name="architecture"></a>体系结构

下图概述了本教程中安装的 OpenFrame 7.0 体系结构组件：

![开放框架组件](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure 系统要求

下表列出了在 Azure 上安装的要求。
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>要求</th><th>描述</th></tr>
</thead>
<tbody>
<tr><td>Azure 上支持 Linux 发行版
</td>
<td>
Linux x86 2.6 （32 位， 64 位）<br/>
红帽 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>硬件
</td>
<td>核心： 2 （最小）<br/>
内存：4 GB（最小）<br/>
交换空间：1 GB（最小）<br/>
硬盘：100 GB（最小）<br/>
</td>
</tr>
<tr><td>适用于 Windows 用户的可选软件
</td>
<td>PuTTY：本指南用于配置 VM 功能<br/>
WinSCP：一个流行的SFTP客户端和FTP客户端，您可以使用<br/>
用于 Windows 的 Eclipse：TmaxSoft 支持的开发平台<br/>
（目前不支持微软视觉工作室）
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>先决条件

计划花几天时间组装所有必需的软件并完成所有手动流程。

在开始之前，执行以下操作：

- 从 TmaxSoft 获取 OpenFrame 安装媒体。 如果您是现有 TmaxSoft 客户，请联系您的 TmaxSoft 代表以获取许可副本。 否则，请从[TmaxSoft](https://www.tmaxsoft.com/contact/)请求试用版。

- 通过向 发送电子邮件请求<support@tmaxsoft.com>OpenFrame 文档。

- 如果尚未订阅 Azure 订阅，则获取 Azure 订阅。 您还可以在开始之前创建[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 可选。 设置站点到站点 VPN 隧道或跳转框，将 Azure VM 的访问限制给组织中允许的用户。 此步骤不是必需的，但它是最佳做法。

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>在 Azure 上为 OpenFrame 和 Tibero 设置 VM

您可以使用各种部署模式设置 OpenFrame 环境，但以下过程演示如何在一个 VM 上部署 OpenFrame 应用程序服务器和 Tibero 数据库。 在较大的环境和大型工作负载中，最佳做法是在自己的 VM 上单独部署数据库，以获得最佳性能。

**创建 VM**

1. 转到 "在"处<https://portal.azure.com>转到 Azure 门户并登录到您的帐户。

2. 单击“虚拟机”****。

    ![Azure 门户中的资源列表](media/vm-01.png)

3. 单击 **“添加”**。

    ![在 Azure 门户中添加选项](media/vm-02.png)

4. 在**操作系统**的右侧，单击 **"更多**"。

     ![Azure 门户中的更多选项](media/vm-03.png)

5. 单击**基于 CentOS 的 7.3**以准确遵循此演练，或者您可以选择其他受支持的 Linux 发行版。

     ![Azure 门户中的操作系统选项](media/vm-04.png)

6. 在 **"基本"** 设置中，**输入名称**、**用户名**、**身份验证类型**、**订阅**（即用即付即用即付即用即付）和 **"资源组**"（使用现有用户名或创建 TmaxSoft 组）。

7. 完成后（包括**身份验证类型的**公钥/私钥对），单击"**提交**"。

> [!NOTE]
> 如果使用 SSH 公钥进行**身份验证类型**，请参阅下一节中的步骤以生成公钥/私钥对，然后在此处继续这些步骤。

### <a name="generate-a-publicprivate-key-pair"></a>生成公钥/私钥对

如果使用 Windows 操作系统，则需要 PuTTYgen 来生成公钥/私钥对。

公钥可以自由共享，但私钥应完全保密，绝不应与另一方共享。 生成密钥后，必须将**SSH 公钥**粘贴到配置中 — 实际上，将其上载到 Linux VM。 它存储在用户帐户主目录\_\~的 /.ssh 目录中的授权密钥中。 然后在 SSH 客户端（在我们的案例中为 PuTTY）中提供关联的**SSH 私钥**后，Linux VM 能够识别和验证连接。

让新个人访问 VM 时： 

- 每个新人使用 PuTTYgen 生成自己的公共/私钥。
- 个人单独存储自己的私钥，并将公钥信息发送给 VM 的管理员。
- 管理员将公钥的内容粘贴到\~/.ssh/授权\_密钥文件。
- 新的个人通过PuTTY连接。

**生成公钥/私钥对**

1.  从<https://www.putty.org/>下载 PuTTYgen 并使用所有默认设置安装它。

2.  要打开 PuTTYgen，请在 C 中找到 PuTTY 安装目录：\\程序文件\\PuTTY。

    ![普蒂接口](media/puttygen-01.png)

3.  单击 **“生成”**。

    ![PuTTY 密钥生成器对话框](media/puttygen-02.png)

4.  生成后，保存公钥和私钥。 将公钥的内容粘贴到 **"创建虚拟机\>基础知识"** 窗格的**SSH 公钥**部分（如上一节的步骤 6 和 7 所示）。

    ![PuTTY 密钥生成器对话框](media/puttygen-03.png)

### <a name="configure-vm-features"></a>配置 VM 功能

1. 在 Azure 门户中，在 **"选择大小**边栏选项卡"中，选择所需的 Linux 计算机硬件设置。 安装 Tibero 和 OpenFrame*的最低*要求为 2 个 CPU 和 4 GB RAM，如以下示例安装所示：

    ![创建虚拟机 - 基础知识](media/create-vm-01.png)

2. 单击 **"3 设置"** 并使用默认设置配置可选功能。
3. 查看您的付款详细信息。

    ![创建虚拟机 - 购买](media/create-vm-02.png)

4. 提交您的选择。 Azure 开始部署 VM。 此过程通常需要几分钟时间。

5. 部署 VM 时，将显示其仪表板，显示配置期间选择的所有设置。 记下公共 IP**地址**。

    ![Azure 仪表板上的 tmax](media/create-vm-03.png)

6. 打开 PuTTY。

7. 对于**主机名**，键入您的用户名和您复制的公共 IP 地址。 例如，**\@用户名公开 ip**。

    ![PuTTY 配置对话框](media/putty-01.png)

8. 在 **"类别"** 框中，**单击\>"连接\>SSH Auth**"。提供**私钥**文件的路径。

    ![PuTTY 配置对话框](media/putty-02.png)

9. 单击 **"打开**"以启动 PuTTY 窗口。 如果成功，您将连接到在 Azure 上运行的新 CentOS VM。

10. 要以根用户身份登录，请键入**sudo bash**。

    ![根用户登录命令窗口](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>设置环境和包

现在，VM 已创建并登录，您必须执行一些设置步骤并安装所需的预安装包。

1. 使用 vi**ofdemo**编辑主机文件 （）`vi /etc/hosts`将demo 的名称映射到本地 IP 地址。 假设我们的 IP 为 192.168.96.148，则此更改之前：

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     这是在更改之后：

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. 创建组和用户：

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. 更改用户 oframe7 的密码：

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. 更新 /etc/sysctl.conf 中的内核参数：

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. 动态刷新内核参数而不重新启动：

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. 获取所需的包：确保服务器已连接到 Internet，下载以下程序包，然后安装它们：

     - 多斯2unix
     - glibc
     - 格利bc.i686 glibc.x86\_64
     - 利巴约
     - 恩诅咒

          > [!NOTE]
          > 安装 ncurses 包后，创建以下符号链接：
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - 利巴约-德维尔.x86\_64
     - 跟踪
     - ltrace
     - gdb

7. 在 Java RPM 安装的情况下，执行以下操作：

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>安装蒂贝罗数据库

Tibero 在 Azure 上的 OpenFrame 环境中提供了几个关键功能：

- Tibero 用作 OpenFrame 内部数据存储，用于各种系统功能。
- VSAM 文件（包括 KSDS、RRDS 和 ESDS）在内部使用 Tibero 数据库进行数据存储。
- TACF 数据存储库存储在蒂贝罗。
- OpenFrame 目录信息存储在 Tibero 中。
- Tibero 数据库可用作 IBM Db2 的替换来存储应用程序数据。

**安装蒂贝罗**

1. 验证 Tibero 二进制安装程序文件是否存在并查看版本号。
2. 将 Tibero 软件复制到 Tibero 用户帐户 （oframe）。 例如：

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. 在 vi\_（`vi .bash_profile`） 中打开 .bash 配置文件，并在其中粘贴以下内容：

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. 要执行 bash 配置文件，请按命令提示符类型执行：

    ```
    source .bash_profile
    ```

5. 生成提示文件（Tibero 的配置文件），然后在 vi 中打开它。 例如：

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. 修改\$\_TB HOME/客户端/配置/tbdsn.tbr 并放置 127.0.0.1 而不是本地主机，如下所示：

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. 创建数据库。 将显示以下输出：

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. 要回收 Tibero，首先使用 命令`tbdown`将其关闭。 例如：

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. 现在启动蒂贝罗使用`tbboot`。 例如：

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. 要创建表空间，请使用 SYS 用户 （sys/tmax） 访问数据库，然后为默认卷和 TACF 创建必要的表空间：

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. 现在键入以下 SQL 命令：

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. 启动 Tibero 并验证 Tibero 进程是否正在运行：

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

输出：

![蒂贝罗输出](media/tibero-01.png)

## <a name="install-odbc"></a>安装 ODBC

OpenFrame 中的应用程序使用开源 unixODBC 项目提供的 ODBC API 与 Tibero 数据库通信。

要安装 ODBC：

1. 验证 unixODBC-2.3.4.tar.gz 安装程序文件是否存在，或使用 该`wget unixODBC-2.3.4.tar.gz`命令。 例如：

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. 解压缩二进制文件。 例如：

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. 导航到 unixODBC-2.3.4 目录，并使用检查机信息生成 Makefile。 例如：

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     默认情况下，unixODBC 安装在 /usr /local 中`--prefix`，因此传递值以更改位置。 同样，默认情况下，配置文件安装在 /etc 中，因此`--sysconfdir`传递所需位置的值。

4. 执行 Makefile：`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. 编译后，在程序目录中复制可执行文件。 例如：

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. 使用 vi 编辑 bash`vi ~/.bash_profile`配置文件 （ ） 并添加以下内容：

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. 应用 ODBC。 相应地编辑以下文件。 例如：

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. 创建符号链接并验证 Tibero 数据库连接：

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

显示以下输出：

![显示连接到 SQL 的 ODBC 输出](media/odbc-01.png)

## <a name="install-openframe-base"></a>安装开放框架底座

基本应用程序服务器安装在 OpenFrame 用于在 Azure 上管理系统的各个服务之前，包括事务处理服务器进程。

**安装 OpenFrame 底座**

1. 确保 Tibero 安装成功，然后验证以下\_OpenFrame Base7\_\_0\_Linux\_x86 64.bin 安装程序文件和 base.属性配置文件是否存在。

2. 使用以下特定于 Tibero 的信息更新 bash 配置文件：

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. 执行 bash 配置文件：`[oframe7@ofdemo ~]$ . .bash_profile`
4. 确保 Tibero 进程正在运行。 例如：

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![基本](media/base-01.png)

     > [!IMPORTANT]
     > 请确保在安装之前启动 Tibero。

5. 在[technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do)生成许可证，并将 OpenFrame 基础、批处理、TACF、OSC 许可证放入相应的文件夹中：

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. 下载 OpenFrame 基础二进制文件和 base.属性文件：

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. 使用 base.属性文件执行安装程序。 例如：

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    完成后，将播放"安装完成"消息。

8. 使用`ls -ltr`命令验证 OpenFrame 基础目录结构。 例如：

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. 启动开放框架库：

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot 命令输出](media/base-02.png)

10. 使用 si 中的 tmadmin 命令验证进程状态是否就绪。 RDY 显示在每个进程**的状态**列中：

     ![tmadmin 命令输出](media/base-03.png)

11. 关闭打开框架底座：

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>安装打开帧批处理

OpenFrame Batch 由多个组件组成，这些组件模拟大型机批处理环境，并用于在 Azure 上运行批处理作业。

**安装批处理**

1. 确保基本安装成功，\_然后验证 OpenFrame Batch7\_0\_Fix2\_MVS\_\_\_Linux x86 64.bin 安装程序文件和 batch.属性配置文件是否存在：

2. 在命令提示符下，`vi batch.properties`键入以使用 vi 编辑 batch.属性文件。

3. 修改参数如下：

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. 要执行批处理安装程序，请执行命令提示符类型：

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. 安装完成后，通过在命令提示`tmboot`符处键入启动已安装的 OpenFrame 套件。

    ![tmboot 输出](media/tmboot-01.png)

6. 在`tmadmin`命令提示符处键入以检查 OpenFrame 过程。

    ![Tmax 管理屏幕](media/tmadmin-01.png)

7. 执行以下命令：

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. 使用`tmdown`命令启动和关闭批处理：

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>安装 TACF

TACF 管理器是一个 OpenFrame 服务模块，通过 RACF 安全性控制用户对系统和资源的访问。

**安装 TACF**

1. 验证\_OpenFrame Tacf7\_\_0\_Fix2\_Linux\_x86 64.bin 安装程序文件和 tacf.属性配置文件是否存在。
2. 确保批处理安装成功，然后使用 vi 打开 tacf.属性文件 （）。`vi tacf.properties`
3. 修改 TACF 参数：

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. 完成 TACF 安装程序后，应用 TACF 环境变量。 在命令提示符处，键入：

     ```
     source \~/.bash\_profile
     ```

5. 执行 TACF 安装程序。 在命令提示符处，键入：

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     输出的内容与以下类似：

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. 在命令提示符下，`tmboot`键入以重新启动 OpenFrame。 输出的内容与以下类似：

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. 验证在`tmadmin``si`命令中使用进程状态是否就绪。 例如：

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     在**状态**列中，将显示 RDY：

    ![状态列中的 RDY](media/tmboot-02.png)

8. 执行以下命令：

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. 使用`tmdown`命令关闭服务器。 输出的内容与以下类似：

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>安装 ProSort

ProSort 是一种用于批处理事务的实用程序，用于对数据进行排序。

**安装 ProSort**

1. 确保批处理安装成功，然后验证**\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz**安装程序文件是否存在。

2. 使用属性文件执行安装程序。 在命令提示符处，键入：

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. 将 prosort 目录移动到主位置。 在命令提示符处，键入：

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. 创建许可证子目录并在那里复制许可证文件。 例如：

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. 在 vi 中打开`vi .bash_profile`bash.profile ， 并更新如下：

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. 要执行 bash 配置文件，在命令提示符处，请键入：`. .bash_profile`

7. 创建配置文件。 例如：

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. 创建符号链接。 例如：

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. 通过执行`prosort -h`命令验证 ProSort 安装。 例如：

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>安装 OFCOBOL

OFCOBOL 是解释大型机 COBOL 程序的 OpenFrame 编译器。 

**安装 OFCOBOL**

1. 确保批处理/\_联机安装成功，然后验证 OpenFrame COBOL3\_0\_40\_Linux\_x86\_64.bin 安装程序文件是否存在。

2. 要执行 OFCOBOL 安装程序，在命令提示符下，键入：

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. 阅读许可协议，然后按 Enter 继续。

4. 接受许可协议。 安装完成后，将显示以下内容：

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. 打开 vi 中的 bash`vi .bash_profile`配置文件 ， 并验证是否更新了 OFCOBOL 变量。
6. 执行 bash 配置文件。 在命令提示符处，键入：

     ```
      source ~/.bash_profile
     ```

7. 将 OFCOBOL 许可证复制到已安装的文件夹。 例如：
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. 转到 OpenFrame tjclrun.conf 配置文件，并在 vi 中打开该文件。 例如：
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   以下是更改前的 SYSLIB 部分：
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   以下是更改后的 SYSLIB 部分：
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. 查看 vi\_中的\_OpenFrame COBOL 安装日志文件，并验证是否存在错误。 例如：
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. 使用`ofcob --version`命令并查看版本号以验证安装。 例如：

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. 使用 命令`tmdown/tmboot`重新启动打开帧。

## <a name="install-ofasm"></a>安装 OFASM

OFASM 是解释大型机的汇编程序的 OpenFrame 编译器。

**安装 OFASM**

1. 确保批处理/联机安装成功，然后验证\_OpenFrame ASM3\_0\_Linux\_x86\_64.bin 安装程序文件是否存在。

2. 执行安装程序。 例如：

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. 阅读许可协议，然后按 Enter 继续。
4. 接受许可协议。
5. 使用 OFASM 变量验证 bash 配置文件已更新。 例如：

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. 在 vi 中打开 OpenFrame tjclrun.conf 配置文件，然后按照如下方式对其进行编辑：

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     以下是更改*前的*[SYSLIB] 部分：

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     下面是更改*后的*[SYSLIB] 部分：

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. 在 vi\_中打开\_OpenFrame ASM 安装日志.日志文件，并验证是否存在错误。 例如：

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. 通过发出以下命令之一重新启动 OpenFrame：

     ```
     tmdown / tmboot
     ```

     — 或 —

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>安装 OSC

OSC 是与 IBM CICS 类似的开放框架环境，支持高速 OLTP 事务和其他管理功能。

**安装 OSC**

1. 确保\_基本安装成功，然后验证 OpenFrame OSC7\_0\_Fix2\_\_Linux x86\_64.bin 安装程序文件和 osc.属性配置文件是否存在。
2. 在 osc.属性文件中编辑以下参数：
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. 使用属性文件执行安装程序，如下所示：

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     完成后，将显示"安装完成"消息。

4. 验证 bash 配置文件是否使用 OSC 变量更新。
5. 查看 OpenFrame\_\_OSC7\_0\_修复2 安装日志文件。 其外观应与下图相似：

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. 使用 vi 打开 syssys.seq 配置文件。 例如：

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. 在\#BASE\#和 BATCH 部分中，如所示编辑参数。

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. 复制许可证文件。 例如：

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. 要启动和关闭 OSC，请通过`osctdlinit OSCOIVP1`键入命令提示符来初始化 CICS 区域共享内存。

10. 运行`oscboot`以启动 OSC。 输出的内容与以下类似：

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. 要验证进程状态是否就绪，请使用 si`tmadmin`中的命令。 所有进程都应在**状态**列中显示 RDY。

    ![显示 RDY 的过程](media/tmadmin-02.png)

12. 使用`oscdown`命令关闭 OSC。

## <a name="install-jeus"></a>安装 JEUS

JEUS（Java 企业用户解决方案）提供 OpenFrame Web 应用程序服务器的表示层。

在安装 JEUS 之前，请安装 Apache Ant 包，该包提供安装 JEUS 所需的库和命令行工具。

**安装阿帕奇蚂蚁**

1. 使用`wget`命令下载 Ant 二进制文件。 例如：

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. 使用实用程序`tar`提取二进制文件并将其移动到适当的位置。 例如：

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. 为了提高效率，请创建一个符号链接：

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. 打开 vi 中的 bash`vi .bash_profile`配置文件 ，然后使用以下变量更新它：

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  应用修改后的环境变量。 例如：

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**安装 JEUS**

1. 使用`tar`实用程序扩展安装程序。 例如：

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. 创建一个**jeus**文件夹 （`mkdir jeus7`）， 并解压缩二进制文件。
3. 更改为**设置**目录（或使用 JEUS 参数进行您自己的环境）。 例如：

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. 在执行`ant clean-all`生成之前执行。 输出的内容与以下类似：

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  备份域配置模板.属性文件。 例如：

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. 打开域配置模板.属性文件，在 vi 中：

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. 将 `jeus.password=jeusadmin nodename=Tmaxsoft` 更改为 `jeus.password=tmax1234 nodename=ofdemo`

8. 执行命令`ant install`以生成 JEUS。
9.  使用 JEUS\_变量更新 .bash 配置文件文件，如下所示：

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. 执行 bash 配置文件。 例如：

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *可选*。 创建别名，以便轻松关闭和启动 JEUS 组件：

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. 要验证安装，请启动域管理员服务器，如下所示：

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. 使用语法通过 Web 登录进行验证：

     ```
     http://<IP>:<port>/webadmin/login
     ```

     例如，<http://192.168.92.133:9736/webadmin/login.>显示登录屏幕：
    
     ![JEUS WebAdmin 登录屏幕](media/jeus-01.png)

     > [!NOTE]
     > 如果遇到端口安全问题，请打开端口 9736 或禁用防火墙 （）。`systemctl stop firewall`

14. 要更改服务器 1 的主机名，请单击 **"锁定&编辑**"，然后单击**服务器 1**。 在"服务器"窗口中，更改主机名，如下所示：

    1.  将**节点名称**更改为**ofdemo**
    2.  单击窗口右侧的 **"确定**"。
    3.  单击窗口左下角的 **"应用更改**"，有关说明，请输入*主机名更改*。

    ![JEUS 网络管理员屏幕](media/jeus-02.png)

15. 在确认屏幕中验证配置是否成功。

    ![jeus_domain 服务器屏幕](media/jeus-03.png)

16. 使用以下命令启动托管服务器进程"server1"：

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>安装OFGW

OFGW 是 OpenFrame 网关，支持 3270 终端仿真器与 OSI 基座之间的通信，并管理终端仿真器和 OSI 之间的会话。

**安装 OFGW**

1. 确保 JEUS 已成功安装，然后验证 OFGW7\_0\_1\_泛型.bin 安装程序文件是否存在。
2. 执行安装程序。 例如：

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. 对相应的提示使用以下位置：
     -   JEUS 主页目录
     -   JEUS 域名
     -   JEUS 服务器名称
     -   蒂贝罗司机
     -   德莫的 Tmax 节点 ID

4. 接受其余的默认值，然后按 Enter 退出安装程序。

5. 验证 OFGW 的 URL 是否按预期工作：

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     将出现以下屏幕：

    ![开放框架网络终端](media/ofgw-01.png)

## <a name="install-ofmanager"></a>安装管理器

OFManager 为 Web 环境中的 OpenFrame 提供操作和管理功能。

**安装管理管理器**

1. 验证 OFManager7\_泛型.bin 安装程序文件是否存在。
2. 执行安装程序。 例如：

     ```
     OFManager7_Generic.bin
     ```

3.  按 Enter 继续，然后接受许可协议。
4.  选择安装文件夹。
5.  接受默认值。
6.  选择 Tibero 作为数据库。
7.  按 Enter 退出安装程序。
8.  验证 OFManager 的 URL 是否按预期工作：

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

将显示"开始"屏幕：

![Tmax OpenFrame 管理器登录屏幕](media/ofmanager-01.png)

这完成了 OpenFrame 组件的安装。

## <a name="next-steps"></a>后续步骤

如果您正在考虑大型机迁移，我们不断扩大的合作伙伴生态系统可以帮助您。 有关选择合作伙伴解决方案的详细指南，请参阅[平台现代化联盟](https://datamigration.microsoft.com/)。

-   [Azure 入门](https://docs.microsoft.com/azure/)
-   [Host Integration Server (HIS) 文档](https://docs.microsoft.com/host-integration-server/)
-   [Azure 虚拟数据中心提升和移动指南](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
