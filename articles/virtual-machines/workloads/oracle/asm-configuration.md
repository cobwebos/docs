---
title: "在 Azure Linux 虚拟机上设置 Oracle ASM | Microsoft Docs"
description: "在 Azure 环境中快速启动并运行 Oracle ASM。"
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
ms.date: 05/2/2017
ms.author: v-shiuma
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: e3c98a3d4d7012f6cb6c7e1a1b8263e6ecdee57b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017

---

# <a name="set-up-oracle-asm-on-an-azue-linux-virtual-machine"></a>在 Azure Linux 虚拟机上设置 Oracle ASM 

本文介绍如何在 Azure 中的 Oracle Linux 虚拟机 (VM) 上安装和设置 Oracle 自动存储管理 (Oracle ASM)。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。 

## <a name="sign-in-to-azure"></a>登录 Azure 

若要在 Azure CLI 中登录到 Azure 订阅，请使用 [az login](/cli/azure/#login) 命令。 然后遵照屏幕说明操作。

```azurecli
az login
```

## <a name="create-a-resource-group"></a>创建资源组

若要创建资源组，请使用 [az group create](/cli/azure/group#create) 命令。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 westus 位置创建名为 myResourceGroup 的资源组。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-vm"></a>创建 VM

若要创建可用于 Oracle ASM 的 VM，请完成以下步骤：

1.  若要创建虚拟机，请使用 [az vm create](/cli/azure/vm#create) 命令。 

  以下示例创建名为 myVM 的 VM。 此外，它还在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
  ```

  创建 VM 后，Azure CLI 将显示类似于以下示例的信息。 请记下 `publicIpAddress` 的值。 到时需要使用此地址来访问 VM。

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

2.  添加用于 Oracle ASM 配置的磁盘：

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

## <a name="connect-to-the-vm"></a>连接到 VM

若要与 VM 建立 SSH 会话，请使用以下命令。 请将 IP 地址替换为 VM 的 `publicIpAddress` 值。

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>安装 Oracle ASM

若要安装 Oracle ASM，请完成以下步骤。 

有关安装 Oracle ASM 的详细信息，请参阅 [适用于 Oracle Linux 6 的 Oracle ASMLib 下载内容](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)。  

1. 运行 `yum list`：

  ```bash
  $ sudo su -
  # yum list
  ```
  首次运行 `yum list` 时，它可能需要几分钟时间来加载。

2.  运行以下附加命令：

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

3.  验证是否已安装 Oracle ASM：

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

4.  添加用户和组：

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

5.  验证用户和组：

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

6.  创建一个文件夹并更改所有者：

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>设置 Oracle ASM

在本教程中，默认用户为 *grid*，默认组为 *asmadmin*。 请确保 *oracle* 用户属于 asmadmin 组。 若要设置 Oracle ASM 安装，请完成以下步骤：

1.  设置 Oracle ASM 库驱动程序：

  ```bash
  # /usr/sbin/oracleasm configure -i

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

2.  查看磁盘配置：
  ```bash
  # cat /proc/partitions
  ```

3.  格式化磁盘：

  ```bash
  # fdisk /dev/sdc
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

4.  针对 /dev/sdd、/dev/sde 和 /dev/sdf 重复前面的步骤。

5.  检查磁盘配置：

  ```bash
  # cat /proc/partitions
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
    11        0    1048575 sr0
  ```

6.  检查 Oracle ASM 服务状态：

  ```bash
  # service oracleasm status
  Checking if ASM is loaded: no
  Checking if /dev/oracleasm is mounted: no
  ```

7.  启动 Oracle ASM 服务：

  ```bash
  # service oracleasm start
  Initializing the Oracle ASMLib driver:                     [  OK  ]
  Scanning the system for Oracle ASMLib disks:               [  OK  ]
  ```

8.  创建 Oracle ASM 磁盘：

  ```bash
  # service oracleasm createdisk ASMSP /dev/sdc1
  Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

  # service oracleasm createdisk DATA /dev/sdd1
  Marking disk "DATA" as an ASM disk:                        [  OK  ]

  # service oracleasm createdisk DATA1 /dev/sde1
  Marking disk "DATA1" as an ASM disk:                       [  OK  ]

  [root@myVM ~]# service oracleasm createdisk FRA /dev/sdf1
  Marking disk "FRA" as an ASM disk:                         [  OK  ]
  ```

9.  列出 Oracle ASM 磁盘：

  ```bash
  # service oracleasm listdisks
  ASMSP
  DATA
  DATA1
  FRA
  ```

10. 更改 root、oracle 和 grid 用户的密码（稍后在安装过程中需要使用这些密码）：

  ```bash
  # passwd oracle
  # passwd grid
  # passwd root
  ```

11. 更改文件夹权限：

  ```bash
  # chmod -R 775 /opt
  # chown grid:oinstall /opt
  # chown oracle:oinstall /dev/sdc1
  # chown oracle:oinstall /dev/sdd1
  # chown oracle:oinstall /dev/sde1
  # chown oracle:oinstall /dev/sdf1
  # chmod 600 /dev/sdc1
  # chmod 600 /dev/sdd1
  # chmod 600 /dev/sde1
  # chmod 600 /dev/sdf1
  ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>下载并准备 Oracle Grid Infrastructure

若要下载并准备 Oracle Grid Infrastructure 软件，请完成以下步骤：

1.  从 [Oracle ASM 下载页](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)下载 Oracle Grid Infrastructure。 

  在下载标题“Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64”的下面，应已列出两个可供下载的 .zip 文件。

2.  将 .zip 文件下载到客户端计算机后，可以使用安全复制协议 (SCP) 将文件复制到 VM。

    ```bash
    scp *.zip <publicIpAddress>:<folder>
    ```

3.  将 .zip 文件移到 /opt 文件夹。 然后更改文件的所有者：

    ```bash
    # mv <folder>/*.zip /opt
    # cd /opt
    # chown grid:oinstall linuxamd64_12102_grid_1of2.zip
    # chown grid:oinstall linuxamd64_12102_grid_2of2.zip
    ```

4.  解压缩文件（如果尚未安装 Linux 解压缩实用工具，请安装）：

    ```bash
    # yum install unzip
    # unzip linuxamd64_12102_grid_1of2.zip
    # unzip linuxamd64_12102_grid_2of2.zip
    ```

5.  更改权限：

    ```bash
    # chown -R grid:oinstall /opt/grid
    ```

6.  关闭防火墙：

    ```bash
    # service iptables status
    # service iptables stop
    ```

7.  检查可用的交换空间。 至少需要 6 GB 交换空间来安装 Oracle Grid Infrastructure：

    ```bash
    # swapon -s
    ```

    如果交换空间不到 6 GB，可以通过运行以下命令来增加交换空间：

    ```bash
    # dd if=/dev/zero of=/extraswap bs=1M count=6144
    6144+0 records in
    6144+0 records out
    6442450944 bytes (6.4 GB) copied, 141.245 s, 45.6 MB/s

    # mkswap /mnt/resource/extraswap
    mkswap: /mnt/resource/extraswap: warning: don't erase bootbits sectors
            on whole disk. Use -f to force.
    Setting up swapspace version 1, size = 6291452 KiB
    no label, UUID=80bd7816-b3a2-4eec-a824-733209644fc5
    # swapon /mnt/resource/extraswap

    ```

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>准备要运行 X11 的客户端和 VM（仅适用于 Windows 限客户端）

若要准备要运行 X11 的客户端和 VM，请完成以下步骤： 

1.  以 root 身份登录，然后编辑 /etc/ssh/ssh_config 文件。 将 Forwardx11 的设置更改为 `yes`：

  ```
  #   ForwardX11 no
  ForwardX11 yes

  ```

2.  将 PuTTY 和 Xming 下载到 Windows 计算机：

  * [下载 PuTTY](http://www.putty.org/)
  * [下载 Xming](https://xming.en.softonic.com/)

3.  安装 PuTTY 后，请在 PuTTY 文件夹（例如 C:\Program Files\PuTTY）中运行 puttygen.exe（PuTTY 密钥生成器）。

4.  在 PuTTY 密钥生成器中：

  1.  若要生成密钥，请选择“生成”按钮。 
  2.  复制密钥的内容 (Ctrl+C)。
  3.  选择“保存私钥”按钮。 
  4.  忽略显示的警告，然后选择“确定”。

  ![PuTTY 密钥生成器页屏幕截图](./media/asm-configuration/puttykeygen.png)

5.  在 VM 中运行以下命令：

  ```bash
  # sudo su - grid
  $ mkdir .ssh (if not already created)
  $ cd .ssh
  ```

6.  创建名为 authorized_keys 的文件。 在此文件中粘贴密钥的内容，然后保存该文件。

  > [!NOTE]
  > 该密钥必须包含字符串 `ssh-rsa`。 此外，密钥的内容必须是单行文本。
  >  

7.  启动 PuTTY。 在“类别”窗格中，转到“连接” > “SSH” > “身份验证”。 在“用于身份验证的私钥文件”框中，浏览到前面生成的密钥。

  ![“设置私钥”页屏幕截图](./media/asm-configuration/setprivatekey.png)

9.  在“类别”窗格中，转到“连接” > “SSH” > “X11”。 选中“启用 X11 转发”框。

  ![“启用 X11”页屏幕截图](./media/asm-configuration/enablex11.png)

10. 在“类别”窗格中，转到“会话”。 输入主机信息，然后选择“打开”。

  ![“会话”页屏幕截图](./media/asm-configuration/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>安装 Oracle Grid Infrastructure

若要安装 Oracle Grid Infrastructure，请完成以下步骤：

1. 以 grid 身份登录。 （应该可以直接登录，系统不会提示输入密码。） 

  > [!NOTE]
  > 在开始安装之前，请确保 Xming 正在运行。

    ```bash
    $ cd /opt/grid
    $ ./runInstaller
    ```

  此时将打开 Oracle Grid Infrastructure 12c Release 1 安装程序。 （安装程序可能需要花费几分钟时间来启动。）

2. 在“选择安装选项”页上，选择“安装并配置适用于独立服务器的 Oracle Grid Infrastructure”。

  ![安装程序中的“选择安装选项”页屏幕截图](./media/asm-configuration/install01.png)

3. 在“选择产品语言”页上，选择“英语”或所需的语言。

  ![安装程序中的“选择产品语言”页屏幕截图](./media/asm-configuration/install02.png)

4. 在“创建 ASM 磁盘组”页上：
  1.  输入磁盘组的名称。
  2.  在“冗余”下面，选择“外部”。
  3.  在“分配单元大小”下面，选择“4”。
  4.  在“添加磁盘”下面，选择“ORCLASMSP”。

  ![安装程序中的“创建 ASM 磁盘组”页屏幕截图](./media/asm-configuration/install03.png)

5. 在“指定 ASM 密码”页上，选择“对这些帐户使用相同的密码”选项，然后输入密码。

  ![安装程序中的“指定 ASM 密码”页屏幕截图](./media/asm-configuration/install04.png)

6. （可选）在“指定管理选项”页上，选中“使用企业管理器(EM)云控件注册”框。

  ![安装程序中的“指定管理选项”页屏幕截图](./media/asm-configuration/install05.png)

7. 在“特权操作系统组”页上，请使用默认设置。

  ![安装程序中的“特权操作系统组”页屏幕截图](./media/asm-configuration/install06.png)

8. 在“指定安装位置”页上，请使用默认设置。

  ![安装程序中的“指定安装位置”页屏幕截图](./media/asm-configuration/install07.png)

9. 在“创建清单”页上，输入或浏览到相应的文件夹位置。

  ![安装程序中的“创建清单”页屏幕截图](./media/asm-configuration/install08.png)

10. 在“Root 脚本执行配置”页上，选中“自动运行配置脚本”框。 然后，选择“使用 "root" 用户凭据”选项，并输入 root 用户密码。

  ![安装程序中的“Root 脚本执行配置”页屏幕截图](./media/asm-configuration/install09.png)

11. 在“执行先决条件检查”页上，选择“修复并再次检查”。

  ![安装程序中的“执行先决条件检查”页屏幕截图](./media/asm-configuration/install10.png)

12. 在“修复脚本”页上，选择“确定”。

  ![安装程序中的“修复脚本”页屏幕截图](./media/asm-configuration/install11.png)

13. 在“摘要”页上复查设置选项，然后选择“安装”。

  ![安装程序中的“摘要”页屏幕截图](./media/asm-configuration/install12.png)

14. 此时将显示一个警告对话框。 选择“是”继续。

  ![警告对话框屏幕截图](./media/asm-configuration/install14.png)

15. 在“完成”页上，选择“关闭”完成安装。

  ![安装程序中的“完成”页屏幕截图](./media/asm-configuration/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>设置 Oracle ASM 安装

若要设置 Oracle ASM 安装，请完成以下步骤：

1.  通过 X11 会话以 grid 身份登录：

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  此时将打开 Oracle ASM 配置助手。

2.  在“配置 ASM: 磁盘组”页上选择“创建”按钮，然后选择“显示高级选项”。

  ![“配置 ASM: 磁盘组”页屏幕截图](./media/asm-configuration/asm01.png)

3.  在“创建磁盘组”页上：

  1.  输入磁盘组名称。
  2.  在“选择成员磁盘”下面，选择“ORCL_DATA”和“ORCL_DATA1”。
  3.  在“分配单元大小”下面，选择“4”。 

  ![“创建磁盘组”页屏幕截图](./media/asm-configuration/asm02.png)

4.  在“配置 ASM: 磁盘组”页上选择“创建”按钮，然后选择“显示高级选项”。

  ![“配置 ASM: 磁盘组”页屏幕截图](./media/asm-configuration/asm03.png)

5.  在“创建磁盘组”页上：

  1.  输入磁盘组名称。
  2.  在“冗余”下面，选择“外部”。
  3.  在“选择成员磁盘”下面，选择“ORCL_FRA”。
  4.  在“分配单元大小”下面，选择“4”。

  ![“创建磁盘组”页屏幕截图](./media/asm-configuration/asm04.png)

6.  选择“退出”关闭 ASM 配置助手。

  ![包含“退出”按钮的“配置 ASM: 磁盘组”页屏幕截图](./media/asm-configuration/asm05.png)

## <a name="create-the-database"></a>创建数据库

该 Oracle 软件已安装在 Azure 应用商店映像中。 若要安装数据库，请完成以下步骤：

1.  将用户切换到 oracle superuser，然后初始化用于日志记录的侦听器：

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   此时将打开配置助手。

2.  在“数据库操作”页上，选择“创建数据库”。

  ![“数据库操作”页屏幕截图](./media/asm-configuration/createdb01.png)
  
3. 在“创建模式”页上：

    1.  输入数据库的名称。 
    2.  对于“存储类型”，请选择“自动存储管理(ASM)”。
    3.  对于“数据库文件位置”，请浏览到要使用的文件夹。
    4.  对于“快速恢复区域”，请浏览到要使用的文件夹。

  ![“创建模式”页屏幕截图](./media/asm-configuration/createdb02.png)

4.  在“摘要”页上复查设置选项，然后选择“完成”创建数据库。

  ![“摘要”页屏幕截图](./media/asm-configuration/createdb03.png)

5.   （可选）若要在“完成”页上更改密码，请选择“密码管理”。

  ![“完成”页屏幕截图](./media/asm-configuration/createdb04.png)


## <a name="delete-the-vm"></a>删除 VM

如果不再需要 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[教程：创建高可用性虚拟机](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](../../linux/cli-samples.md)

