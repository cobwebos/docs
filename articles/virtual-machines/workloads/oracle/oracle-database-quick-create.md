---
title: "在 Azure 虚拟机中创建 Oracle Database 12c 数据库 | Microsoft Docs"
description: "在 Azure 环境中快速创建并运行 Oracle Database 12c 数据库。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tonyguid
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/26/2017
ms.author: rclaus
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 38cb5c4c1beb0e50c8a6395afb118c63b2750210
ms.contentlocale: zh-cn
ms.lasthandoff: 05/05/2017

---

# <a name="create-an-oracle-database-12c-database-in-an-azure-virtual-machine"></a>在 Azure 虚拟机中创建 Oracle Database 12c 数据库

可以从命令行或在脚本中使用 Azure CLI 来创建和管理 Azure 资源。 在本文中，我们在脚本中使用 Azure CLI 从 Azure 应用商店库映像部署 Oracle Database 12c 数据库。

在开始之前，请确保已安装了 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="sign-in-to-azure"></a>登录 Azure 

若要在 Azure CLI 中登录到 Azure 订阅，请使用 [az login](/cli/azure/#login) 命令。 然后，遵照屏幕说明进行操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

若要创建资源组，请使用 [az group create](/cli/azure/group#create) 命令。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 `westus` 位置创建名为 `myResourceGroup` 的资源组：

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>创建 VM

若要创建虚拟机 (VM)，请使用 [az vm create](/cli/azure/vm#create) 命令。 

以下示例创建一个名为 `myVM` 的 VM。 此外，它还在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 将显示类似于以下示例的信息。 请记下 `publicIpAddress` 的值。 需要使用此地址来访问 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>连接到 VM

若要与 VM 建立 SSH 会话，请使用以下命令。 请将 IP 地址替换为你的 VM 的 `publicIpAddress` 值。

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>创建数据库

该 Oracle 软件已安装在应用商店映像中。 接下来，安装数据库。 

1.  运行*oracle* 超级用户，然后初始化用于日志记录的侦听器：

    ```bash
    sudo su - oracle
    [oracle@myVM /]$ lsnrctl start
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

2.  创建数据库：

    ```bash
    [oracle@myVM /]$ dbca -silent \
    -createDatabase \
    -templateName General_Purpose.dbc \
    -gdbname cdb1 \
    -sid cdb1 \
    -responseFile NO_VALUE \
    -characterSet AL32UTF8 \
    -sysPassword OraPasswd1 \
    -systemPassword OraPasswd1 \
    -createAsContainerDatabase true \
    -numberOfPDBs 1 \
    -pdbName pdb1 \
    -pdbAdminPassword OraPasswd1 \
    -databaseType MULTIPURPOSE \
    -automaticMemoryManagement false \
    -storageType FS \
    -ignorePreReqs

    Copying database files
    1% complete
    2% complete
    8% complete
    13% complete
    19% complete
    27% complete
    Creating and starting Oracle instance
    29% complete
    32% complete
    33% complete
    34% complete
    38% complete
    42% complete
    43% complete
    45% complete
    Completing Database Creation
    48% complete
    51% complete
    53% complete
    62% complete
    70% complete
    72% complete
    Creating Pluggable Databases
    78% complete
    100% complete
    Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for further details.
    ```

## <a name="prepare-for-connectivity"></a>为连接做准备 
若要确保数据库已正确初始化，请测试本地连接。 执行此操作的最简单方法是使用 `sqlplus` 连接。  

在连接之前，需要设置两个环境变量：ORACLE_HOME 和 ORACLE_SID。

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

还可以将 ORACLE_HOME 和 ORACLE_SID 添加到 .bashrc 文件。 这将保存环境变量供将来登录时使用。

```
# Add ORACLE_HOME.
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# Add ORACLE_SID.
export ORACLE_SID=cdb1

```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express 连接

若要获取可以用来浏览数据库的 GUI 管理工具，请设置 Oracle EM Express。 若要连接到 Oracle EM Express，必须先在 Oracle 中设置端口：

```bash
$ sudo su - oracle

sqlplus / as sysdba

SQL*Plus: Release 12.1.0.2.0 Production on Fri Apr 7 13:16:30 2017

Copyright (c) 1982, 2014, Oracle.  All rights reserved.


Connected to:
Oracle Database 12c Enterprise Edition Release 12.1.0.2.0 - 64bit Production
With the Partitioning, OLAP, Advanced Analytics and Real Application Testing options

SQL> select con_id, name, open_mode from v$pdbs;

    CON_ID NAME                           OPEN_MODE
---------- ------------------------------ ----------
         2 PDB$SEED                       READ ONLY
         3 PDB1                           MOUNT

SQL> alter session set container=pdb1;

Session altered.

SQL> alter database open;

database opened.

SQL> alter session set container=pdb1;

Session altered.

SQL> exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);

PL/SQL procedure successfully completed.
```

## <a name="automate-database-startup-and-shutdown"></a>自动执行数据库启动和关闭

默认情况下，当启动 VM 时，Oracle 数据库不会自动启动。 若要将 Oracle 数据库设置为在 VM 启动时启动，请首先以 root 身份登录。 然后，创建并更新一些系统文件。

1.  以 root 身份登录：

    ```bash
    # sudo su -
    ```

2.  将 /etc/oratab 文件从默认值 `N` 更改为 `Y`：

    ```
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  创建 /etc/init.d/dbora 文件：

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  更改权限：

    ```bash
    # chgrp dba /etc/init.d/dbora
    # chmod 750 /etc/init.d/dbora
    ```

5.  创建用于启动和关闭的符号链接：

    ```bash
    # ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    # ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  若要测试所做的更改，请重新启动 VM：

    ```bash
    # reboot
    ```

## <a name="open-ports-for-connectivity"></a>打开用于连接的端口

最后一个任务是配置一些外部终结点。 若要设置用于保护 VM 的 Azure 网络安全组，请首先在 VM 中退出 SSH 会话。 

1.  若要打开用来远程访问 Oracle 数据库的终结点，请运行以下命令： 

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle\
        --protocol tcp --direction inbound --priority 999 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 1521 --access allow
    ```

    结果应类似于以下示例：

    ```
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "1521",
    "direction": "Inbound",
    "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
    "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
    "name": "allow-oracle",
    "priority": 999,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

2.  若要打开用来远程访问 Oracle EM Express 的终结点，请运行以下命令：

    ```azurecli
    az network nsg rule create --resource-group myResourceGroup\
        --nsg-name myVmNSG --name allow-oracle-EM\
        --protocol tcp --direction inbound --priority 1001 \
        --source-address-prefix '*' --source-port-range '*' \
        --destination-address-prefix '*' --destination-port-range 5502 --access allow
    ```

    结果应类似于以下示例：

    ```azurecli
    {
    "access": "Allow",
    "description": null,
    "destinationAddressPrefix": "*",
    "destinationPortRange": "5502",
    "direction": "Inbound",
    "etag": "W/\"06c68b5e-1b3f-4ae0-bcf6-59b3b981d685\"",
    "id": "/subscriptions/2dad32d6-b188-49e6-9437-ca1d51cec4dd/resourceGroups/kennyRG/providers/Microsoft.Network/networkSecurityGroups/kennyVM1NSG/securityRules/allow-oracle-EM",
    "name": "allow-oracle-EM",
    "priority": 1001,
    "protocol": "Tcp",
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "sourceAddressPrefix": "*",
    "sourcePortRange": "*"
    }
    ```

3.  从浏览器连接 EM Express： 

    ```
    https://<VM hostname>:5502/em
    ```

可以使用 SYS 帐户以及你在安装期间设置的密码进行登录。


## <a name="delete-the-vm"></a>删除 VM

如果不再需要 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[教程：创建具有高可用性的 VM](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](../../linux/cli-samples.md)

