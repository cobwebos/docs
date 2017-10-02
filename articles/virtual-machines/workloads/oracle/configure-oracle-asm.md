---
title: "在 Azure Linux 虚拟机上设置 Oracle ASM | Microsoft Docs"
description: "在 Azure 环境中快速启动并运行 Oracle ASM。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 117212a2e7e3da7c3e249798eec804a652e0ef58
ms.contentlocale: zh-cn
ms.lasthandoff: 07/31/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虚拟机上设置 Oracle ASM  

Azure 虚拟机提供完全可配置的灵活计算环境。 本教程介绍基本的 Azure 虚拟机部署以及 Oracle 自动存储管理 (ASM) 的安装和配置。  学习如何：

> [!div class="checklist"]
> * 创建并连接到 Oracle 数据库 VM
> * 安装并配置 Oracle 自动存储管理
> * 安装并配置 Oracle Grid 基础结构
> * 初始化 Oracle ASM 安装
> * 创建由 ASM 管理的 Oracle DB


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.4 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="prepare-the-environment"></a>准备环境

### <a name="create-a-resource-group"></a>创建资源组

若要创建资源组，请使用 [az group create](/cli/azure/group#create) 命令。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 在此示例中，*eastus* 区域包含一个名为 *myResourceGroup* 的资源组。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>创建 VM

若要创建基于 Oracle 数据库映像的虚拟机并将其配置为使用 Oracle ASM，请使用 [az vm create](/cli/azure/vm#create) 命令。 

以下示例创建名为 myVM 的 VM，其大小为 Standard_DS2_v2，并包含四个 50 GB 的附加数据磁盘。 如果默认密钥位置中不存在 SSH 密钥，此示例还会创建这些密钥。  若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

创建 VM 后，Azure CLI 会显示类似于以下示例的信息。 请记下 `publicIpAddress` 的值。 需要使用此地址来访问 VM。

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>连接到 VM

若要与 VM 建立 SSH 会话并配置其他设置，请使用以下命令。 请将 IP 地址替换为 VM 的 `publicIpAddress` 值。

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>安装 Oracle ASM

若要安装 Oracle ASM，请完成以下步骤。 

有关安装 Oracle ASM 的详细信息，请参阅 [适用于 Oracle Linux 6 的 Oracle ASMLib 下载内容](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)。  

1. 需要以 root 身份登录才能继续安装 ASM：

   ```bash
   sudo su -
   ```
   
2. 运行以下附加命令来安装 Oracle ASM 组件：

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. 验证是否已安装 Oracle ASM：

   ```bash
   rpm -qa |grep oracleasm
   ```

    此命令的输出应列出以下组件：

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM 需要特定的用户和角色才能正常工作。 以下命令创建必备的用户帐户和组： 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. 验证是否正确创建了用户和组：

   ```bash
   id grid
   ```

    此命令的输出应列出以下用户和组：

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. 为用户 *grid* 创建一个文件夹并更改所有者：

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>设置 Oracle ASM

在本教程中，默认用户为 *grid*，默认组为 *asmadmin*。 请确保 *oracle* 用户属于 asmadmin 组。 若要设置 Oracle ASM 安装，请完成以下步骤：

1. 设置 Oracle ASM 库驱动程序的过程涉及到定义默认用户 (grid) 和默认组 (asmadmin)，以及将驱动器配置为在引导时启动（选择 y）并在引导时扫描磁盘（选择 y）。 需要回答以下命令的提示：

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   此命令的输出应如下所示，出现待回答的提示时停止。

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. 查看磁盘配置：
   ```bash
   cat /proc/partitions
   ```

   此命令的输出应如下所示，其中列出了可用的磁盘

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. 运行以下命令格式化磁盘 */dev/sdc*，并使用以下值回答提示：
   - *n* 表示新分区
   - *p* 表示主分区
   - *1* 表示选择第一个分区
   - 按下默认的第一个柱面对应的 `enter`
   - 按下默认的最后一个柱面对应的 `enter`
   - 按 *w* 可将更改写入分区表  

   ```bash
   fdisk /dev/sdc
   ```
   
   如果使用上面提供的回答，fdisk 命令的输出应如下所示：

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. 针对 `/dev/sdd`、`/dev/sde` 和 `/dev/sdf` 重复上述 fdisk 命令。

5. 检查磁盘配置：

   ```bash
   cat /proc/partitions
   ```

   此命令的输出应如下所示：

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. 检查 Oracle ASM 服务状态并启动 Oracle ASM 服务：

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   该命令的输出应如下所示：
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. 创建 Oracle ASM 磁盘：

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   该命令的输出应如下所示：

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. 列出 Oracle ASM 磁盘：

   ```bash
   service oracleasm listdisks
   ```   

   该命令的输出应列出以下 Oracle ASM 磁盘：

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. 更改 root 用户、oracle 用户和 grid 用户的密码。 **记下这些新密码**，因为稍后在安装过程中需要用到。

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. 更改文件夹权限：

   ```bash
   chmod -R 775 /opt 
   chown grid:oinstall /opt 
   chown oracle:oinstall /dev/sdc1 
   chown oracle:oinstall /dev/sdd1 
   chown oracle:oinstall /dev/sde1 
   chown oracle:oinstall /dev/sdf1 
   chmod 600 /dev/sdc1 
   chmod 600 /dev/sdd1 
   chmod 600 /dev/sde1 
   chmod 600 /dev/sdf1
   ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>下载并准备 Oracle Grid Infrastructure

若要下载并准备 Oracle Grid Infrastructure 软件，请完成以下步骤：

1. 从 [Oracle ASM 下载页](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)下载 Oracle Grid Infrastructure。 

   在下载标题“Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64”的下面，下载这两个 .zip 文件。

2. 将 .zip 文件下载到客户端计算机后，可以使用安全复制协议 (SCP) 将文件复制到 VM：

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. 通过 SSH 重新连接到 Azure 中的 Oracle VM，以将 .zip 文件移入 /opt 文件夹。 然后更改文件的所有者：

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. 解压缩文件。 （如果尚未安装 Linux 解压缩工具，请安装。）
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. 更改权限：
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. 更新配置的交换空间。 Oracle Grid 组件至少需要 6.8 GB 交换空间来安装 Grid。 Azure 中 Oracle Linux 映像的默认交换文件大小仅为 2048MB。 需要在 `/etc/waagent.conf` 文件中增大 `ResourceDisk.SwapSizeMB` 并重启 WALinuxAgent 服务，才能让更新的设置生效。 由于这是一个只读的文件，因此需要更改文件权限以启用写入访问。

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   搜索 `ResourceDisk.SwapSizeMB` 并将值更改为 **8192**。 需要按 `insert` 进入插入模式，键入值 **8192**，再按 `esc` 返回到命令模式。 若要写入更改并退出文件，请键入 `:wq` 并按 `enter`。
   
   > [!NOTE]
   > 我们强烈建议始终使用 `WALinuxAgent` 来配置交换空间，以便始终在临时磁盘上进行创建，从而获得最佳性能。 有关详细信息，请参阅[如何在 Linux Azure 虚拟机中添加交换文件](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)。

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>准备用于运行 x11 的本地客户端和 VM
配置 Oracle ASM 需要使用一个图形界面来完成安装和配置。 我们将使用 x11 协议来帮助完成此项安装。 如果所用的客户端系统（Mac 或 Linux）已启用并配置 X11 功能，则可以下面所述的仅适用于 Windows 计算机的配置和设置。 

1. 将 [PuTTY](http://www.putty.org/) 和 [Xming](https://xming.en.softonic.com/) 下载到 Windows 计算机。 在继续之前，需要使用默认值完成安装这两个应用程序。

2. 安装 PuTTY 后，请打开命令提示符，切换到 PuTTY 文件夹（例如 C:\Program Files\PuTTY），运行 `puttygen.exe` 以生成密钥。

3. 在 PuTTY 密钥生成器中：
   
   1. 选择 `Generate` 按钮生成密钥。
   2. 复制密钥的内容 (Ctrl+C)。
   3. 选择 `Save private key` 按钮。
   4. 忽略有关使用通行短语保护密钥的警告，选择 `OK`。

   ![PuTTY 密钥生成器屏幕截图](./media/oracle-asm/puttykeygen.png)

4. 在 VM 中运行以下命令：

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. 创建名为 `authorized_keys` 的文件。 在此文件中粘贴密钥的内容，然后保存该文件。

   > [!NOTE]
   > 该密钥必须包含字符串 `ssh-rsa`。 此外，密钥的内容必须是单行文本。
   >  

6. 在客户端系统上启动 PuTTY。 在“类别”窗格中，转到“连接” > “SSH” > “身份验证”。在“用于身份验证的私钥文件”框中，浏览到前面生成的密钥。

   ![SSH 身份验证选项屏幕截图](./media/oracle-asm/setprivatekey.png)

7. 在“类别”窗格中，转到“连接” > “SSH” > “X11”。 选择“启用 X11 转发”复选框。

   ![SSH X11 转发选项屏幕截图](./media/oracle-asm/enablex11.png)

8. 在“类别”窗格中，转到“会话”。 在主机名称对话框中输入 Oracle ASM VM `<publicIPaddress>`，填写新的 `Saved Session` 名称，单击 `Save`。  保存后，单击 `open` 连接到 Oracle ASM 虚拟机。  首次连接时，会出现远程系统未在注册表中缓存的警告。 单击 `yes` 添加系统并继续。

   ![PuTTY 会话选项屏幕截图](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>安装 Oracle Grid Infrastructure

若要安装 Oracle Grid Infrastructure，请完成以下步骤：

1. 以 **grid** 身份登录。 （应该可以直接登录，系统不会提示输入密码。） 

   > [!NOTE]
   > 如果运行的是 Windows，请确保在开始安装之前已启动 Xming。

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   此时会打开 Oracle Grid Infrastructure 12c Release 1 安装程序。 （安装程序可能需要花费几分钟时间来启动。）

2. 在“选择安装选项”页上，选择“安装并配置适用于独立服务器的 Oracle Grid Infrastructure”。

   ![安装程序中的“选择安装选项”页屏幕截图](./media/oracle-asm/install01.png)

3. 在“选择产品语言”页上，确保已选择“英语”或所需的语言。  单击`next`。

4. 在“创建 ASM 磁盘组”页上：
   - 输入磁盘组的名称。
   - 在“冗余”下面，选择“外部”。
   - 在“分配单元大小”下面，选择“4”。
   - 在“添加磁盘”下面，选择“ORCLASMSP”。
   - 单击`next`。

5. 在“指定 ASM 密码”页上，选择“对这些帐户使用相同的密码”选项，并输入密码。

   ![安装程序中的“指定 ASM 密码”页屏幕截图](./media/oracle-asm/install04.png)

6. 在“指定管理选项”页上，可以选择配置 EM 云控件。 我们将跳过此选项 - 请单击 `next` 继续。 

7. 在“特权操作系统组”页上，请使用默认设置。 单击 `next` 继续。

8. 在“指定安装位置”页上，请使用默认设置。 单击 `next` 继续。

9. 在“创建清单”页上，将“清单目录”更改为 `/u01/app/grid/oraInventory`。 单击 `next` 继续。

   ![安装程序中的“创建清单”页屏幕截图](./media/oracle-asm/install08.png)

10. 在“Root 脚本执行配置”页上，选中“自动运行配置脚本”复选框。 然后，选择“使用 "root" 用户凭据”选项，并输入 root 用户密码。

    ![安装程序中的“Root 脚本执行配置”页屏幕截图](./media/oracle-asm/install09.png)

11. 在“执行先决条件检查”页上，当前安装会失败并出错。 这是预期的行为。 选择 `Fix & Check Again`。

12. 在“修正脚本”对话框中，单击 `OK`。

13. 在“摘要”页上查看选定设置，单击 `Install`。

    ![安装程序中的“摘要”页屏幕截图](./media/oracle-asm/install12.png)

14. 此时会出现警告对话框，告知需要以特权用户的身份运行配置脚本。 单击 `Yes` 继续。

15. 在“完成”页上，单击 `Close` 完成安装。

## <a name="set-up-your-oracle-asm-installation"></a>设置 Oracle ASM 安装

若要设置 Oracle ASM 安装，请完成以下步骤：

1. 确保通过 X11 会话保持以 **grid** 身份登录。 可能需要按 `enter` 来恢复终端。 然后启动 Oracle 自动存储管理配置助手：

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   此时会打开 Oracle ASM 配置助手。

2. 在“配置 ASM: 磁盘组”对话框中单击 `Create` 按钮，再单击 `Show Advanced Options`。

3. 在“创建磁盘组”对话框中：

   - 输入磁盘组名称 **DATA**。
   - 在“选择成员磁盘”下面，选择“ORCL_DATA”和“ORCL_DATA1”。
   - 在“分配单元大小”下面，选择“4”。
   - 单击 `ok` 创建磁盘组。
   - 单击 `ok` 关闭确认窗口。

   ![“创建磁盘组”对话框屏幕截图](./media/oracle-asm/asm02.png)

4. 在“配置 ASM: 磁盘组”对话框中单击 `Create` 按钮，再单击 `Show Advanced Options`。

5. 在“创建磁盘组”对话框中：

   - 输入磁盘组名称 **FRA**。
   - 在“冗余”下面，选择“外部(无)”。
   - 在“选择成员磁盘”下面，选择“ORCL_FRA”。
   - 在“分配单元大小”下面，选择“4”。
   - 单击 `ok` 创建磁盘组。
   - 单击 `ok` 关闭确认窗口。

   ![“创建磁盘组”对话框屏幕截图](./media/oracle-asm/asm04.png)

6. 选择“退出”关闭 ASM 配置助手。

   ![包含“退出”按钮的“配置 ASM: 磁盘组”对话框屏幕截图](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>创建数据库

Oracle 数据库软件已安装在 Azure Marketplace 映像中。 若要创建数据库，请完成以下步骤：

1. 将用户切换到 Oracle 超级用户，然后初始化用于日志记录的侦听器：

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   此时会打开配置助手。

2. 在“数据库操作”页上，单击 `Create Database`。

3. 在“创建模式”页上：

   - 输入数据库的名称。
   - 对于“存储类型”，请确保选择“自动存储管理(ASM)”。
   - 对于“数据库文件位置”，请使用 ASM 建议的默认位置。
   - 对于“快速恢复区域”，请使用 ASM 建议的默认位置。
   - 键入“管理密码”和“确认密码”。
   - 确保已选择 `create as container database`。
   - 键入 `pluggable database name` 值。

4. 在“摘要”页上查看选定的设置，并单击 `Finish` 创建数据库。

   ![“摘要”页屏幕截图](./media/oracle-asm/createdb03.png)

5. 数据库已创建。 在“完成”页上，可以选择解锁其他帐户以使用此数据库并更改密码。 如果想要这样做，请选择“密码管理”- 否则请单击 `close`。

## <a name="delete-the-vm"></a>删除 VM

现已成功在 Azure Marketplace 中的 Oracle DB 映像上已成功配置 Oracle 自动存储管理。  如果不再需要此 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[教程：配置 Oracle DataGuard](configure-oracle-dataguard.md)

[教程：配置 Oracle GoldenGate](Configure-oracle-golden-gate.md)

查看[设计 Oracle DB](oracle-design.md)

