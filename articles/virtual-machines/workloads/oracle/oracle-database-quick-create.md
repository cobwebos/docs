---
title: "在 Azure VM 上创建 Oracle 数据库 | Microsoft Docs"
description: "在 Azure 环境中快速创建并运行 Oracle Database 12c 数据库。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/17/2017
ms.author: rclaus
ms.openlocfilehash: 4f760165fa8a93bbb7646539af748b647fe63bba
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>在 Azure VM 上创建 Oracle 数据库

本指南详述了如何使用 Azure CLI 通过从 [Oracle 应用商店库映像](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview)部署 Azure 虚拟机来创建 Oracle 12c 数据库。 部署服务器后，若要配置 Oracle 数据库，请先通过 SSH 进行连接。 

如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，此快速入门教程要求运行 Azure CLI 2.0.4 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>创建资源组

使用 [az group create](/cli/azure/group#az_group_create) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>创建虚拟机

若要创建虚拟机 (VM)，请使用 [az vm create](/cli/azure/vm#az_vm_create) 命令。 

以下示例创建一个名为 `myVM` 的 VM。 此外，它还在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 请记下 `publicIpAddress` 的值。 到时需要使用此地址来访问 VM。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>连接到 VM

若要与 VM 建立 SSH 会话，请使用以下命令。 请将 IP 地址替换为 VM 的 `publicIpAddress` 值。

```bash 
ssh <publicIpAddress>
```

## <a name="create-the-database"></a>创建数据库

该 Oracle 软件已安装在 Marketplace 映像中。 如下所述创建一个示例数据库。 

1.  切换到 *oracle* 超级用户，然后初始化用于日志记录的侦听器：

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    输出与下面类似：

    ```bash
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
    dbca -silent \
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

    创建数据库需要几分钟的时间。

3. 设置 Oracle 变量

在连接之前，需要设置两个环境变量：*ORACLE_HOME* 和 *ORACLE_SID*。

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
还可以将 ORACLE_HOME 和 ORACLE_SID 变量添加到 .bashrc 文件。 这会保存环境变量供将来登录时使用。使用所选编辑器确认下列语句是否已被添加至 `~/.bashrc` 文件。

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express 连接

若要获取可以用来浏览数据库的 GUI 管理工具，请设置 Oracle EM Express。 若要连接到 Oracle EM Express，必须先在 Oracle 中设置端口。 

1. 使用 sqlplus 连接到数据库：

    ```bash
    sqlplus / as sysdba
    ```

2. 在连接后，为 EM Express 设置端口 5502

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. 打开容器 PDB1（如果尚未打开），但首先请检查状态：

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    输出与下面类似：

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. 如果 `PDB1` 的 OPEN_MODE 不是 READ WRITE，则运行以下命令来打开 PDB1：

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

需要键入 `quit` 结束 sqlplus 会话，键入 `exit` 注销 Oracle 用户。

## <a name="automate-database-startup-and-shutdown"></a>自动执行数据库启动和关闭

默认情况下，当重启 VM 时，Oracle 数据库不会自动启动。 若要将 Oracle 数据库设置为自动启动，请首先以 root 身份登录。 然后，创建并更新一些系统文件。

1. 以 root 身份登录
    ```bash
    sudo su -
    ```

2.  使用喜欢的编辑器来编辑文件 `/etc/oratab`，并将默认值 `N` 更改为 `Y`：

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  创建名为 `/etc/init.d/dbora` 的文件并粘贴下列内容：

    ```
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

4.  使用 *chmod* 更改对文件的权限，如下所示：

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  创建用于启动和关闭的符号链接，如下所示：

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  若要测试所做的更改，请重新启动 VM：

    ```bash
    reboot
    ```

## <a name="open-ports-for-connectivity"></a>打开用于连接的端口

最后一个任务是配置一些外部终结点。 若要设置用于保护 VM 的 Azure 网络安全组，请首先在 VM 中退出 SSH 会话（在前面的步骤中重新引导时，应当已退出了 SSH）。 

1.  若要打开用来远程访问 Oracle 数据库的终结点，请使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 创建一个网络安全组，如下所示： 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  若要打开用来远程访问 Oracle EM Express 的终结点，请使用 [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) 创建一个网络安全组，如下所示：

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. 如果需要，使用 [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) 获取 VM 的公共 IP 地址，如下所示：

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  从浏览器连接 EM Express。 确保浏览器与 EM Express 兼容（需要安装 Flash）： 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

可以使用 **SYS** 帐户登录，并选中“以 sysdba 身份”复选框。 使用你在安装期间设置的密码 **OraPasswd1**。 

![Oracle OEM Express 登录页面的屏幕截图](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>清理资源

当在 Azure 中了解完首个 Oracle 数据库，且不再需要 VM 时，可以使用 [az group delete](/cli/azure/group#az_group_delete) 命令来删除资源组、VM 和一切相关资源。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

了解其他 [Azure 上的 Oracle 解决方案](oracle-considerations.md)。 

尝试[安装和配置 Oracle Automated Storage Management](configure-oracle-asm.md) 教程。