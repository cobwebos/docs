---
title: "在 Azure Linux 虚拟机上备份和恢复 Oracle Database 12c 数据库 | Microsoft Docs"
description: "了解怎样在 Azure 环境中备份和恢复 Oracle Database 12c 数据库。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 5/17/2017
ms.author: rclaus
ms.openlocfilehash: 9a2293f13b90e9a4cb11b4169fad969dd622a9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虚拟机上备份和恢复 Oracle Database 12c 数据库

可从命令提示符使用 Azure CLI 或通过脚本来创建和管理 Azure 资源。 在本文中，我们使用 Azure CLI 脚本从 Azure 应用商店库映像部署 Oracle Database 12c 数据库。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prepare-the-environment"></a>准备环境

### <a name="step-1-prerequisites"></a>步骤 1：先决条件

*   要执行备份和恢复过程，首先必须创建已安装 Oracle Database 12c 实例的 Linux VM。 用来创建 VM 的 Marketplace 映像是“Oracle:Oracle-Database-Ee:12.1.0.2:latest”。

    要了解如何创建 Oracle 数据库，请参阅 [Oracle 创建数据库快速入门](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)。


### <a name="step-2-connect-to-the-vm"></a>步骤 2：连接到 VM

*   若要与 VM 建立安全外壳 (SSH) 会话，请使用以下命令。 将 IP 地址和主机名组合替换为 VM 的 `publicIpAddress` 值。

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>步骤 3：准备数据库

1.  此步骤假定已有在名为“myVM”的 VM 上运行的 Oracle 实例 (cdb1)。

    运行“oracle”超级用户根，然后初始化侦听器：

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  （可选）请确保数据库处于存档日志模式：

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  （可选）创建表格以测试提交：

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  验证或更改备份文件的位置和大小：

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. 使用 Oracle 恢复管理器 (RMAN) 备份数据库：

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>步骤 4：Linux VM 的应用程序一致性备份

应用程序一致性备份是 Azure 备份中的一项新功能。 可以创建和选择要在 VM 快照前后执行的脚本（快照前和快照后）。

1. 下载 JSON 文件。

    从 https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig 下载 VMSnapshotScriptPluginConfig.json。 该文件的内容类似于：

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. 在 VM 上创建 /etc/azure 文件夹：

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. 复制 JSON 文件。

    将 VMSnapshotScriptPluginConfig.json 复制到 /etc/azure 文件夹。

4. 编辑 JSON 文件。

    编辑 VMSnapshotScriptPluginConfig.json 文件，在其中加入 `PreScriptLocation` 和 `PostScriptlocation` 参数。 例如：

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. 创建快照前和快照后脚本文件。

    以下是“冷备份”（关闭和重启时脱机备份）的快照前和快照后脚本示例：

    对于 /etc/azure/pre_script.sh：

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    对于 /etc/azure/post_script.sh：

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    以下是“热备份”（联机备份）的快照前和快照后脚本示例：

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    对于 /etc/azure/post_script.sh：

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    对于 /etc/azure/pre_script.sql，请按照需求修改文件内容：

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    对于 /etc/azure/post_script.sql，请按照需求修改文件内容：

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. 更改文件权限：

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. 测试脚本。

    要测试脚本，请首先以 root 身份登录。 然后，请确保没有任何错误：

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

有关详细信息，请参阅 [Linux VM 的应用程序一致性备份](https://azure.microsoft.com/en-us/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)。


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>步骤 5：使用 Azure 恢复服务保管库备份 VM

1.  在 Azure 门户中，搜索恢复服务保管库。

    ![恢复服务保管库页](./media/oracle-backup-recovery/recovery_service_01.png)

2.  要添加新保管库，请在“恢复服务保管库”边栏选项卡上单击“添加”。

    ![恢复服务保管库“添加”页](./media/oracle-backup-recovery/recovery_service_02.png)

3.  若要继续，请单击“myVault”。

    ![恢复服务保管库“详细信息”页](./media/oracle-backup-recovery/recovery_service_03.png)

4.  在“myVault”边栏选项卡上，单击“备份”。

    ![恢复服务保管库“备份”页](./media/oracle-backup-recovery/recovery_service_04.png)

5.  在“备份目标”边栏选项卡上，使用默认值“Azure”和“虚拟机”。 单击 **“确定”**。

    ![恢复服务保管库“详细信息”页](./media/oracle-backup-recovery/recovery_service_05.png)

6.  对于“备份策略”，使用“DefaultPolicy”或选择“创建新策略”。 单击 **“确定”**。

    ![恢复服务保管库“备份策略详细信息”页](./media/oracle-backup-recovery/recovery_service_06.png)

7.  在“选择虚拟机”边栏选项卡中，选中“myVM1”复选框，然后单击“确定”。 单击“启用备份”按钮。

    ![恢复服务保管库“备份项详细信息”页](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > 在单击“启用备份”后，备份过程将不会在计划时间到期前启动。 若要设置即时备份，请完成下一步。

8.  在“myVault - 备份项”边栏选项卡中，在“备份项计数”下选择备份项计数。

    ![恢复服务保管库“myVault 详细信息”页](./media/oracle-backup-recovery/recovery_service_08.png)

9.  在“备份项(Azure 虚拟机)”边栏选项卡中，单击页面右侧的省略号 (...) 按钮，然后单击“立即备份”。

    ![恢复服务保管库“立即备份”命令](./media/oracle-backup-recovery/recovery_service_09.png)

10. 单击“备份”按钮。 等待备份过程结束。 然后转到[步骤 6：删除数据库文件](#step-6-remove-the-database-files)。

    若要查看备份作业的状态，请单击“作业”。

    ![恢复服务保管库“作业”页](./media/oracle-backup-recovery/recovery_service_10.png)

    下图中显示了备份作业的状态：

    ![显示有状态的恢复服务保管库作业页](./media/oracle-backup-recovery/recovery_service_11.png)

11. 对于应用程序一致性备份，请解决日志文件中的所有错误。 该日志文件位于 /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0。

### <a name="step-6-remove-the-database-files"></a>步骤 6：删除数据库文件 
本文的稍后部分中将介绍如何测试恢复过程。 能够测试恢复过程之前，需要删除数据库文件。

1.  删除表空间和备份文件：

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  （可选）关闭 Oracle 实例：

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>从恢复服务保管库还原已删除文件
若要还原已删除文件，请完成以下步骤：

1. 在 Azure 门户中，搜索“myVault”恢复服务保管库项。 在“概述”边栏选项卡中，在“备份项”下选择项的数目。

    ![恢复服务保管库 myVault 备份项](./media/oracle-backup-recovery/recovery_service_12.png)

2. 在“备份项计数”下，选择项的数目。

    ![恢复服务保管库 Azure 虚拟机备份项计数](./media/oracle-backup-recovery/recovery_service_13.png)

3. 在“myvm1”边栏选项卡中，单击“文件恢复(预览)”。

    ![恢复服务保管库“文件恢复”页的屏幕快照](./media/oracle-backup-recovery/recovery_service_14.png)

4. 在“文件恢复(预览)”窗格中，单击“下载脚本”。 然后，将下载的 (.sh) 文件保存到客户端计算机上的文件夹中。

    ![下载脚本文件保存选项](./media/oracle-backup-recovery/recovery_service_15.png)

5. 将 .sh 文件复制到 VM。

    以下示例演示如何使用安全复制 (scp) 命令将文件移动到 VM。 还可先将内容复制到剪贴板，再粘贴到 VM 上设置的新文件中。

    > [!IMPORTANT]
    > 在以下示例中，请确保更新 IP 地址和文件夹值。 这些值必须映射到保存文件的文件夹。

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. 更改文件，使其属于根路径。

    在以下示例中，更改文件，使其属于根路径。 然后更改权限。

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    以下示例显示运行上一脚本后会看到什么。 当系统提示继续时，请输入“Y”。

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. 确认访问已安装的卷。

    若要退出，请输入“q”，然后搜索已安装的卷。 若要创建已添加卷的列表，在命令提示符处输入“df -k”。

    ![df -k 命令](./media/oracle-backup-recovery/recovery_service_16.png)

8. 使用以下脚本将缺失文件复制回文件夹：

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. 在以下脚本中，使用 RMAN 恢复数据库：

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. 卸载磁盘。

    在 Azure 门户中的“文件恢复(预览)”边栏选项卡上，单击“卸载磁盘”。

    ![卸载磁盘命令](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>还原整个 VM

可以还原整个 VM，而不是从恢复服务保管库还原已删除文件。

### <a name="step-1-delete-myvm"></a>步骤 1：删除 myVM

*   在 Azure 门户，转到“myVM1”保管库，然后选择“删除”。

    ![保管库“删除”命令](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>步骤 2：恢复 VM

1.  转到“恢复服务保管库”，然后选择“myVault”。

    ![myVault 条目](./media/oracle-backup-recovery/recover_vm_02.png)

2.  在“概述”边栏选项卡中，在“备份项”下选择项的数目。

    ![myVault 备份项](./media/oracle-backup-recovery/recover_vm_03.png)

3.  在“备份项(Azure 虚拟机)”边栏选项卡上，选择“myvm1”。

    ![恢复 VM 页](./media/oracle-backup-recovery/recover_vm_04.png)

4.  在“myvm1”边栏选项卡中，单击省略号 (...) 按钮，然后单击“还原 VM”。

    ![还原 VM 命令](./media/oracle-backup-recovery/recover_vm_05.png)

5.  在“选择还原点”边栏选项卡上，选择想要还原的项，然后单击“确定”。

    ![选择还原点](./media/oracle-backup-recovery/recover_vm_06.png)

    如果已启用应用程序一致性备份，将出现一个竖直蓝条。

6.  在“还原配置”边栏选项卡中，选择虚拟机名称和资源组，然后单击“确定”。

    ![还原配置值](./media/oracle-backup-recovery/recover_vm_07.png)

7.  要还原 VM，请单击“还原”按钮。

8.  若要查看还原过程的状态，请依次单击“作业”和“备份作业”。

    ![备份作业状态命令](./media/oracle-backup-recovery/recover_vm_08.png)

    下图显示还原过程的状态：

    ![还原过程的状态](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>步骤 3：设置公共 IP 地址
还原 VM 后，请设置公共 IP 地址。

1.  在搜索框中输入“公共 IP 地址”。

    ![公共 IP 地址列表](./media/oracle-backup-recovery/create_ip_00.png)

2.  在“公共 IP 地址”边栏选项卡中，单击“添加”。 在“创建公共 IP 地址”边栏选项卡的“名称”下，选择公共 IP 名称。 对于“资源组”，请选择“使用现有资源组”。 然后单击“创建”。

    ![创建 IP 地址](./media/oracle-backup-recovery/create_ip_01.png)

3.  若要将公共 IP 地址与 VM 的网络接口关联，请搜索并选择“myVMip”。 然后单击“关联”。

    ![关联 IP 地址](./media/oracle-backup-recovery/create_ip_02.png)

4.  对于“资源类型”，选择“网络接口”。 选择 myVM 实例使用的网络接口，然后单击“确定”。

    ![选择资源类型和 NIC 值](./media/oracle-backup-recovery/create_ip_03.png)

5.  搜索并打开从门户移植的 myVM 实例。 与 VM 关联的 IP 地址显示在 myVM“概述”边栏选项卡上。

    ![IP 地址值](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>步骤 4：连接到 VM

*   要连接到 VM，请使用以下脚本：

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>步骤 5：测试该数据库是否可访问
*   要测试可访问性，请使用以下脚本：

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > 如果数据库“启动”命令生成错误，请参阅[步骤 6：使用 RMAN 恢复数据库](#step-6-optional-use-rman-to-recover-the-database)，恢复数据库。

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>步骤 6：（可选）使用 RMAN 恢复数据库
*   要恢复数据库，请使用以下脚本：

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

现已完成在 Azure Linux VM 上备份和恢复 Oracle Database 12c 数据库。

## <a name="delete-the-vm"></a>删除 VM

如果不再需要 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](../../linux/cli-samples.md)



