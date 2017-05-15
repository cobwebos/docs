---
title: "在 Azure VM 上创建 Oracle 12c 数据库 | Microsoft Docs"
description: "快速启动 Oracle 12c 数据库并在 Azure 环境中运行。"
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
ms.sourcegitcommit: db034a8151495fbb431f3f6969c08cb3677daa3e
ms.openlocfilehash: ba55e2e4449737c4b759211cf0c429d42b961a38
ms.contentlocale: zh-cn
ms.lasthandoff: 04/29/2017

---

# <a name="create-an-oracle-12c-database-on-azure-vm"></a>在 Azure VM 上创建 Oracle 12c 数据库

此脚本使用 Azure CLI 创建 Oracle 12c 数据库。

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详述了如何使用 Azure CLI 通过应用商店库映像部署 Oracle 12c 数据库。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="log-in-to-azure"></a>登录 Azure 

使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 `westus` 位置创建名为 `myResourceGroup` 的资源组。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 命令创建 VM。 

下面的示例创建一个名为 `myVM` 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 将显示类似于以下示例的信息：记下 `publicIpAddress`。 此地址用于访问 VM。

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

## <a name="connect-to-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的 SSH 会话。 将 IP 地址替换为你的虚拟机的 `publicIpAddress`。

```bash 
ssh <publicIpAddress>
```

## <a name="create-database"></a>创建数据库

Oracle 软件已在应用商店映像上安装，因此下一步是安装数据库。 第一步是以“oracle”超级用户的身份运行并初始化用于日志记录的侦听器：

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

下一步是创建数据库：

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

## <a name="preparing-for-connectivity"></a>准备进行连接 
为了确保已正确初始化数据库，我们将测试本地连接。 执行此操作的最简单方法是使用 `sqlplus` 连接。  在连接之前，我们需要先设置一些环境变量 - 特别是 *ORACLE_HOME* 和 *ORACLE_SID* 环境变量。

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME

ORACLE_SID=cdb1; export ORACLE_SID
```

（可选）可以将 ORACLE_HOME 和 ORACLE_SID 添加到 .bashrc 文件，以便为将来登录保存这些设置。

```
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1

# add oracle sid
export ORACLE_SID=cdb1

```

## <a name="setup-connectivity-to-oracle-em-express"></a>设置与 Oracle EM Express 的连接

我们将允许 Oracle EM Express 使用 GUI 管理工具浏览数据库。  若要连接到 Oracle EM Express，必须先在 Oracle 中配置端口。

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
## <a name="automating-database-startup-and-shutdown"></a>自动执行数据库启动和关闭

创建 Oracle 实例后，它并未设置为在计算机启动时自动启动。  若要完成这些任务，需要以 root 用户身份登录，然后创建/更新某些系统文件。

```bash
# sudo su -
```

将“/etc/oratab”文件从默认值“N”更新为“Y”

```
cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
```

接下来，创建“/etc/init.d/dbora”文件

```bash
#!/bin/sh
# chkconfig: 345 99 10
# description: Oracle auto start-stop script.
#
# Set ORA_HOME to be equivalent to the $ORACLE_HOME
ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
ORA_OWNER=oracle

case "$1" in
'start')
    # Start the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    # Remove "&" if you don't want startup as a background process.
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
    touch /var/lock/subsys/dbora
    ;;

'stop')
    # Stop the Oracle databases:
    # The following command assumes that the oracle login
    # will not prompt the user for any values
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
    rm -f /var/lock/subsys/dbora
    ;;
esac
```

更改权限

```bash
# chgrp dba /etc/init.d/dbora
# chmod 750 /etc/init.d/dbora
```
创建用于启动和关闭的符号链接

```bash
# ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
# ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
```

重启 VM 以进行测试
```bash
# reboot
```

## <a name="opening-the-ports-for-connectivity"></a>打开用于连接的端口

最后一步是配置一些外部终结点。 为了配置 Azure 网络安全组以保护 VM，应退出 VM 上的 SSH 会话。 为了打开用于远程访问 Oracle DB 的终结点，将执行以下命令。 

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

结果应类似于以下响应：

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

为了打开用于远程访问 Oracle EM Express 的终结点，将执行以下命令。

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVmNSG --name allow-oracle-EM\
    --protocol tcp --direction inbound --priority 1001 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 5502 --access allow
```

结果应类似于以下响应：

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

从浏览器连接 EM Express
```
https://<VM hostname>:5502/em
```
可以使用 SYS 帐户和安装过程中指定的密码登录


## <a name="delete-virtual-machine"></a>删除虚拟机

如果不再需要资源组、VM 和所有相关的资源，可以使用以下命令将其删除。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[创建高可用性虚拟机教程](../../linux/create-cli-complete.md)

[浏览 VM 部署 CLI 示例](../../linux/cli-samples.md)

