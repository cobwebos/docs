---
title: "在 Azure Linux VM 上实现 Oracle Golden Gate | Microsoft Docs"
description: "在 Azure 环境中快速建立 Oracle Golden Gate 并运行。"
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
ms.date: 05/19/2017
ms.author: rclaus
ms.openlocfilehash: a05711357d345267647c02e42336fd37c09e1bff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>在 Azure Linux VM 上实现 Oracle Golden Gate 

Azure CLI 用于从命令行或脚本创建和管理 Azure 资源。 本指南详述了如何使用 Azure CLI 通过 Azure 应用商店库映像部署 Oracle 12c 数据库。 

本文档逐步演示如何在 Azure VM 上创建、安装和配置 Oracle Golden Gate。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prepare-the-environment"></a>准备环境

若要执行 Oracle Golden Gate 安装，需要在同一可用性集中创建两个 Azure VM。 用于创建 VM 的 Marketplace 映像是 **Oracle:Oracle-Database-Ee:12.1.0.2:latest**。

还需要熟悉 Unix 编辑器 vi 并基本了解 x11 (X Windows)。

下面是环境配置的摘要：
> 
> |  | **主站点** | **复制站点** |
> | --- | --- | --- |
> | **Oracle 版本** |Oracle 12c 版本 2 – (12.1.0.2) |Oracle 12c 版本 2 – (12.1.0.2)|
> | **计算机名称** |myVM1 |myVM2 |
> | **操作系统** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **复制架构** |TEST|TEST |
> | **Golden Gate 所有者/复制** |C##GGADMIN |REPUSER |
> | **Golden Gate 进程** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>登录 Azure 

使用 [az login](/cli/azure/#login) 命令登录 Azure 订阅。 然后，遵照屏幕指令进行操作。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#create) 命令创建资源组。 Azure 资源组是在其中部署 Azure 资源以及可以从中管理这些资源的逻辑容器。 

以下示例在 `westus` 位置创建名为 `myResourceGroup` 的资源组。

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>创建可用性集

以下步骤是可选的，但建议执行。 有关详细信息，请参阅 [Azure 可用性集指南](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>创建虚拟机

使用 [az vm create](/cli/azure/vm#create) 命令创建 VM。 

以下示例创建两个 VM，分别名为 `myVM1` 和 `myVM2`。 如果默认密钥位置中不存在 SSH 密钥，则创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。

#### <a name="create-myvm1-primary"></a>创建 myVM1（主）：
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

创建 VM 之后，Azure CLI 将显示类似于以下示例的信息。 （记下 `publicIpAddress`。 此地址用于访问 VM。）

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

#### <a name="create-myvm2-replicate"></a>创建 myVM2（复制）：
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

创建之后也记下 `publicIpAddress`。

### <a name="open-the-tcp-port-for-connectivity"></a>为连接打开 TCP 端口

下一步是配置外部终结点，通过这些终结点可以远程访问 Oracle 数据库。 若要配置外部终结点，请运行以下命令。

#### <a name="open-the-port-for-myvm1"></a>为 myVM1 打开端口：

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

效果应类似于以下响应：

```bash
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

#### <a name="open-the-port-for-myvm2"></a>为 myVM2 打开端口：

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

使用以下命令创建与虚拟机的 SSH 会话。 将 IP 地址替换为虚拟机的 `publicIpAddress`。

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>在 myVM1（主）上创建数据库

Oracle 软件已在应用商店映像上安装，因此下一步是安装数据库。 

以“oracle”超级用户身份运行软件：

```bash
sudo su - oracle
```

创建数据库：

```bash
$ dbca -silent \
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
```
输出应类似于以下响应：

```bash
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
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

设置 ORACLE_SID and ORACLE_HOME 变量。

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=gg1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

（可选）可以将 ORACLE_HOME 和 ORACLE_SID 添加到 .bashrc 文件，以便为将来登录保存这些设置：

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=gg1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>启动 Oracle 侦听器
```bash
$ sudo su - oracle
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>在 myVM2（复制）上创建数据库

```bash
sudo su - oracle
```
创建数据库：

```bash
$ dbca -silent \
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
```
设置 ORACLE_SID and ORACLE_HOME 变量。

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

（可选）可以将 ORACLE_HOME 和 ORACLE_SID 添加到 .bashrc 文件，以便为将来登录保存这些设置。

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>启动 Oracle 侦听器
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>配置 Golden Gate 
若要配置 Golden Gate，请执行本部分中的步骤。

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>在 myVM1（主）上启用存档日志模式

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
```
启用强制日志记录，并确保至少存在一个日志文件。

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>下载 Golden Gate 软件
若要下载并准备 Oracle Golden Gate 软件，请完成以下步骤：

1. 从 [Oracle Golden Gate 下载页面](http://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html)下载 **fbo_ggs_Linux_x64_shiphome.zip** 文件。 在下载标题 **Oracle GoldenGate 12.x.x.x for Oracle Linux x86-64** 下， 应存在一组供下载的 .zip 文件。

2. 将 .zip 文件下载到客户端计算机后，使用安全复制协议 (SCP) 将文件复制到 VM：

  ```bash
  $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
  ```

3. 将 .zip 文件移到 **/opt** 文件夹。 然后按如下所示更改文件的所有者：

  ```bash
  $ sudo su -
  # mv <folder>/*.zip /opt
  ```

4. 解压缩文件（如果尚未安装 Linux 解压缩实用工具，请安装）：

  ```bash
  # yum install unzip
  # cd /opt
  # unzip fbo_ggs_Linux_x64_shiphome.zip
  ```

5. 更改权限：

  ```bash
  # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
  ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>准备要运行 x11 的客户端和 VM（仅适用于 Windows 客户端）
这是可选步骤。 如果正在使用 Linux 客户端或者已经有 x11设置，则可以跳过此步骤。

1. 将 PuTTY 和 Xming 下载到 Windows 计算机：

  * [下载 PuTTY](http://www.putty.org/)
  * [下载 Xming](https://xming.en.softonic.com/)

2.  安装 PuTTY 后，请在 PuTTY 文件夹（例如 C:\Program Files\PuTTY）中运行 puttygen.exe（PuTTY 密钥生成器）。

3.  在 PuTTY 密钥生成器中：

  - 若要生成密钥，请选择“生成”按钮。
  - 复制密钥的内容 (**Ctrl+C**)。
  - 选择“保存私钥”按钮。
  - 忽略显示的警告，并选择“确定”。

    ![PuTTY 密钥生成器页屏幕截图](./media/oracle-golden-gate/puttykeygen.png)

4.  在 VM 中运行以下命令：

  ```bash
  # sudo su - oracle
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

5. 创建名为 **authorized_keys** 的文件。 在此文件中粘贴密钥的内容，然后保存该文件。

  > [!NOTE]
  > 该密钥必须包含字符串 `ssh-rsa`。 此外，密钥的内容必须是单行文本。
  >  

6. 启动 PuTTY。 在“类别”窗格中，选择“连接” > “SSH” > “身份验证”。在“用于身份验证的私钥文件”框中，浏览到前面生成的密钥。

  ![“设置私钥”页屏幕截图](./media/oracle-golden-gate/setprivatekey.png)

7. 在“类别”窗格中，选择“连接” > “SSH” > “X11”。 然后选中“启用 X11 转发”框。

  ![“启用 X11”页屏幕截图](./media/oracle-golden-gate/enablex11.png)

8. 在“类别”窗格中，转到“会话”。 输入主机信息，并选择“打开”。

  ![会话页屏幕截图](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>安装 Golden Gate 软件

若要安装 Oracle Golden Gate，请完成以下步骤：

1. 以 oracle 身份登录。 （应该可以直接登录，系统不会提示输入密码。）在开始安装之前，请确保 Xming 正在运行。
 
  ```bash
  $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
  $ ./runInstaller
  ```
2. 选择“Oracle GoldenGate for Oracle Database 12c”。 然后选择“下一步”继续。

  ![安装程序中的“选择安装”页屏幕截图](./media/oracle-golden-gate/golden_gate_install_01.png)

3. 更改软件位置。 然后选中“启动管理器”框，并输入数据库位置。 选择“下一步”继续。

  ![“选择安装”页屏幕截图](./media/oracle-golden-gate/golden_gate_install_02.png)

4. 更改清单目录，然后选择“下一步”继续。

  ![“选择安装”页屏幕截图](./media/oracle-golden-gate/golden_gate_install_03.png)

5. 在“摘要”屏幕上，选择“安装”以继续。

  ![安装程序中的“选择安装”页屏幕截图](./media/oracle-golden-gate/golden_gate_install_04.png)

6. 系统可能会提示以“root”身份运行脚本。 如果是这样，则打开单独的会话，通过 ssh 连接到 VM，执行 sudo 操作到 root，然后运行脚本。 选择“确定”继续。

  ![“选择安装”页屏幕截图](./media/oracle-golden-gate/golden_gate_install_05.png)

7. 完成安装后，选择“关闭”完成过程。

  ![“选择安装”页屏幕截图](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>在 myVM1（主）上设置服务

1. 创建或更新 tnsnames.ora 文件：

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. 创建 Golden Gate 所有者和用户帐户。

  > [!NOTE]
  > 所有者帐户必须具有 C## 前缀。
  >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. 创建 Golden Gate 测试用户帐户：

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> @demo_ora_insert
  SQL> EXIT;
  ```

4. 配置提取参数文件。

 启动 Golden Gate 命令行接口 (ggsci)：

  ```bash
  $ sudo su - oracle
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
  Successfully logged into database  pdb1
  GGSCI>  ADD SCHEMATRANDATA pdb1.test
  2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
  2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

  GGSCI> EDIT PARAMS EXTORA
  ```
5. 将以下内容添加到 EXTRACT 参数文件（使用 vi 命令）。 按 Esc 键、“:wq!” 以保存文件。 

  ```bash
  EXTRACT EXTORA
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTRAIL ./dirdat/rt  
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT 
  LOGALLSUPCOLS
  UPDATERECORDFORMAT COMPACT
  TABLE pdb1.test.TCUSTMER;
  TABLE pdb1.test.TCUSTORD;
  ```
6. 注册提取 -- 集成提取：

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci

  GGSCI> dblogin userid C##GGADMIN, password ggadmin
  Successfully logged into database CDB$ROOT.

  GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

  2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

  GGSCI> exit
  ```
7. 设置提取检查点并启动实时提取：

  ```bash
  $ ./ggsci
  GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
  EXTRACT (Integrated) added.

  GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
  RMTTRAIL added.

  GGSCI>  START EXTRACT EXTORA

  Sending START request to MANAGER ...
  EXTRACT EXTORA starting

  GGSCI > info all

  Program     Status      Group       Lag at Chkpt  Time Since Chkpt

  MANAGER     RUNNING
  EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
  ```
在此步骤中，可找到启动 SCN（以后会在其他部分中使用）：

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> SELECT current_scn from v$database;
  CURRENT_SCN
  -----------
      1857887
  SQL> EXIT;
  ```

  ```bash
  $ ./ggsci
  GGSCI> EDIT PARAMS INITEXT
  ```

  ```bash
  EXTRACT INITEXT
  USERID C##GGADMIN, PASSWORD ggadmin
  RMTHOST 10.0.0.5, MGRPORT 7809
  RMTTASK REPLICAT, GROUP INITREP
  TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
  ```

  ```bash
  GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
  ```

### <a name="set-up-service-on-myvm2-replicate"></a>在 myVM2（复制）上设置服务


1. 创建或更新 tnsnames.ora 文件：

  ```bash
  $ cd $ORACLE_HOME/network/admin
  $ vi tnsnames.ora

  cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

  pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
  ```

2. 创建复制帐户：

  ```bash
  $ sqlplus / as sysdba
  SQL> alter session set container = pdb1;
  SQL> create user repuser identified by rep_pass container=current;
  SQL> grant dba to repuser;
  SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
  SQL> connect repuser/rep_pass@pdb1 
  SQL> EXIT;
  ```

3. 创建 Golden Gate 测试用户帐户：

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ sqlplus system/OraPasswd1@pdb1
  SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
  SQL> GRANT connect, resource, dba TO test;
  SQL> ALTER USER test QUOTA 100M on USERS;
  SQL> connect test/test@pdb1
  SQL> @demo_ora_create
  SQL> EXIT;
  ```

4. 用于复制更改的 REPLICAT 参数文件： 

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS REPORA  
  ```
  REPORA 参数文件的内容：

  ```bash
  REPLICAT REPORA
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
  DDL INCLUDE MAPPED
  DDLOPTIONS REPORT
  DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;
  ```

5. 设置复制检查点：

  ```bash
  GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
  GGSCI> EDIT PARAMS INITREP

  ```

  ```bash
  REPLICAT INITREP
  ASSUMETARGETDEFS
  DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
  USERID repuser@pdb1, PASSWORD rep_pass
  MAP pdb1.test.*, TARGET pdb1.test.*;   
  ```

  ```bash
  GGSCI> ADD REPLICAT INITREP, SPECIALRUN
  ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>设置复制（myVM1 和 myVM2）

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1.在 myVM2（复制）上设置复制

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
使用以下内容更新文件：

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
然后重新启动管理器服务：

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2.在 myVM1（主）上设置复制

启动初始加载，并检查错误：

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3.在 myVM2（复制）上设置复制

使用之前获取的编号更改 SCN 编号：

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
复制已开始，可以通过向 TEST 表插入新记录来测试它。


### <a name="view-job-status-and-troubleshooting"></a>查看作业状态和故障排除

#### <a name="view-reports"></a>查看报告
若要在 myVM1 上查看报告，请运行以下命令：

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
若要在 myVM2 上查看报告，请运行以下命令：

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>查看状态和历史记录
若要在 myVM1 上查看状态和历史记录，请运行以下命令：

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

若要在 myVM2 上查看状态和历史记录，请运行以下命令：

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
这会在 Oracle linux 上完成 Golden Gate 的安装和配置。


## <a name="delete-the-virtual-machine"></a>删除虚拟机

如果不再需要资源组、VM 和所有相关的资源，可以使用以下命令将其删除。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[创建高可用性虚拟机教程](../../linux/create-cli-complete.md)

[浏览 VM 部署 CLI 示例](../../linux/cli-samples.md)
