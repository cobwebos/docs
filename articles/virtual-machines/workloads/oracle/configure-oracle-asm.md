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
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 39f6acd0def9fa5c2de470268cda6666aa572e5d
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017

---

# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>在 Azure Linux 虚拟机上设置 Oracle ASM  

本文介绍如何在 Azure 中的 Oracle Linux 虚拟机 (VM) 上安装和设置 Oracle 自动存储管理 (Oracle ASM)。

在开始之前，请确保已安装 Azure CLI。 有关详细信息，请参阅 [Azure CLI 安装指南](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="prepare-the-environment"></a>准备环境

### <a name="sign-in-to-azure"></a>登录 Azure 

若要在 Azure CLI 中登录到 Azure 订阅，请使用 [az login](/cli/azure/#login) 命令。 然后，遵照屏幕说明进行操作。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>创建资源组

若要创建资源组，请使用 [az group create](/cli/azure/group#create) 命令。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在 westus 位置创建名为 myResourceGroup 的资源组。

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-a-vm"></a>创建 VM

若要创建可用于 Oracle ASM 的 VM，请完成以下步骤：

#### <a name="1--to-create-a-virtual-machine-use-the-az-vm-createcliazurevmcreate-command"></a>1.若要创建虚拟机，请使用 [az vm create](/cli/azure/vm#create) 命令。 

  以下示例创建名为 myVM 的 VM。 此外，它还在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  

  ```azurecli
  az vm create --resource-group myResourceGroup --name myVM --image Oracle:Oracle-Database-Ee:12.1.0.2:latest --size Standard_DS2_v2 --generate-ssh-keys
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

#### <a name="2--add-disks-to-use-for-your-oracle-asm-configuration"></a>2.添加用于 Oracle ASM 配置的磁盘：

  ```azurecli
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk2 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk3 --new --size-gb 50
  az vm disk attach -g myResourceGroup --vm-name myVM --disk myDataDisk4 --new --size-gb 50
  ```

### <a name="connect-to-the-vm"></a>连接到 VM

若要与 VM 建立 SSH 会话，请使用以下命令。 请将 IP 地址替换为 VM 的 `publicIpAddress` 值。

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>安装 Oracle ASM

若要安装 Oracle ASM，请完成以下步骤。 

有关安装 Oracle ASM 的详细信息，请参阅 [适用于 Oracle Linux 6 的 Oracle ASMLib 下载内容](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)。  

### <a name="1-run-yum-list"></a>1.运行 `yum list`：

  ```bash
  $ sudo su -
  # yum list
  ```
  首次运行 `yum list` 时，它可能需要几分钟时间来加载。

### <a name="2--run-these-additional-commands"></a>2.运行以下附加命令：

  ```bash
  # yum list | grep oracleasm
  # yum -y install kmod-oracleasm.x86_64
  # yum -y install oracleasm-support.x86_64
  # wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm
  # rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
  ```

### <a name="3--verify-that-oracle-asm-is-installed"></a>3.验证是否已安装 Oracle ASM：

  ```bash
  # rpm -qa |grep oracleasm
  oracleasm-support-2.1.10-4.el6.x86_64
  kmod-oracleasm-2.0.8-15.el6_9.x86_64
  oracleasmlib-2.0.12-1.el6.x86_64
  ```

### <a name="4--add-users-and-groups"></a>4.添加用户和组：

  ```bash
  # groupadd -g 54345 asmadmin
  # groupadd -g 54346 asmdba
  # groupadd -g 54347 asmoper
  # useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid
  # usermod -g oinstall -G dba,asmdba,asmadmin oracle
  ```

### <a name="5--verify-users-and-groups"></a>5.验证用户和组：

  ```bash
  # id grid
  uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
  ```

### <a name="6--create-a-folder-and-change-owner"></a>6.创建一个文件夹并更改所有者：

  ```bash
  # mkdir /u01/app/grid
  # chown grid:oinstall /u01/app/grid
  ```

## <a name="set-up-oracle-asm"></a>设置 Oracle ASM

在本教程中，默认用户为 *grid*，默认组为 *asmadmin*。 请确保 *oracle* 用户属于 asmadmin 组。 若要设置 Oracle ASM 安装，请完成以下步骤：

### <a name="1--set-the-oracle-asm-library-driver"></a>1.设置 Oracle ASM 库驱动程序：

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

### <a name="2--view-the-disk-configuration"></a>2.查看磁盘配置：
  ```bash
  # cat /proc/partitions
  ```

### <a name="3--format-the-disk"></a>3.格式化磁盘：

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

### <a name="4--repeat-the-preceding-step-for-devsdd-devsde-and-devsdf"></a>4.针对 /dev/sdd、/dev/sde 和 /dev/sdf 重复前面的步骤。

### <a name="5--check-the-disk-configuration"></a>5.检查磁盘配置：

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

### <a name="6--check-the-oracle-asm-service-status"></a>6.检查 Oracle ASM 服务状态：

```bash
# service oracleasm status
Checking if ASM is loaded: no
Checking if /dev/oracleasm is mounted: no
```

### <a name="7--start-the-oracle-asm-service"></a>7.启动 Oracle ASM 服务：

```bash
# service oracleasm start
Initializing the Oracle ASMLib driver:                     [  OK  ]
Scanning the system for Oracle ASMLib disks:               [  OK  ]
```

### <a name="8--create-oracle-asm-disks"></a>8.创建 Oracle ASM 磁盘：

```bash
# service oracleasm createdisk ASMSP /dev/sdc1
Marking disk "ASMSP" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk DATA /dev/sdd1
Marking disk "DATA" as an ASM disk:                        [  OK  ]

# service oracleasm createdisk DATA1 /dev/sde1
Marking disk "DATA1" as an ASM disk:                       [  OK  ]

# service oracleasm createdisk FRA /dev/sdf1
Marking disk "FRA" as an ASM disk:                         [  OK  ]
```

### <a name="9--list-oracle-asm-disks"></a>9.列出 Oracle ASM 磁盘：

```bash
# service oracleasm listdisks
ASMSP
DATA
DATA1
FRA
```

### <a name="10-change-the-passwords-for-the-root-oracle-and-grid-users-you-use-the-passwords-later-during-installation"></a>10.更改 root、oracle 和 grid 用户的密码（稍后在安装过程中需要使用这些密码）：

```bash
# passwd oracle
# passwd grid
# passwd root
```

### <a name="11-change-the-folder-permission"></a>11.更改文件夹权限：

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

### <a name="1--download-oracle-grid-infrastructure-from-the-oracle-asm-download-pagehttpwwworaclecomtechnetworkdatabaseenterprise-editiondownloadsdatabase12c-linux-download-2240591html"></a>1.从 [Oracle ASM 下载页](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html)下载 Oracle Grid Infrastructure。 

  在下载标题“Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64”的下面，应已列出两个可供下载的 .zip 文件。

### <a name="2--after-you-download-the-zip-files-to-your-client-computer-you-can-use-secure-copy-protocol-scp-to-copy-the-files-to-your-vm"></a>2.将 .zip 文件下载到客户端计算机后，可以使用安全复制协议 (SCP) 将文件复制到 VM。

```bash
scp *.zip <publicIpAddress>:<folder>
```

### <a name="3--move-the-zip-files-to-the-opt-folder-then-change-the-owner-of-the-files"></a>3.将 .zip 文件移到 /opt 文件夹。 然后更改文件的所有者：

```bash
# mv <folder>/*.zip /opt
# cd /opt
# chown grid:oinstall linuxamd64_12102_grid_1of2.zip
# chown grid:oinstall linuxamd64_12102_grid_2of2.zip
```
### <a name="4--unzip-the-files-install-the-linux-unzip-utility-if-its-not-already-installed"></a>4.解压缩文件（如果尚未安装 Linux 解压缩实用工具，请安装）：
```bash
# yum install unzip
# unzip linuxamd64_12102_grid_1of2.zip
# unzip linuxamd64_12102_grid_2of2.zip
```
### <a name="5--change-permission"></a>5.更改权限：
```bash
# chown -R grid:oinstall /opt/grid
```
### <a name="6--turn-off-the-firewall"></a>6.关闭防火墙：
```bash
# service iptables status
# service iptables stop
```

### <a name="7--check-available-swap-space-you-need-at-lease-68-gb-of-swap-space-to-install-grid-by-default-linux-azure-vms-will-not-have-swap-enabled-and-configured-by-default"></a>7.检查可用的交换空间。 至少需具备 6.8 GB 的交换空间才可安装 Grid；默认情况下，Linux Azure VM 不会启用和配置交换操作。

强烈建议使用 waagent 来配置交换空间，以便始终在临时磁盘中创建该空间；有关步骤的详细信息，请查看以下链接： 

* [如何在 Linux Azure 虚拟机中添加交换文件](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)

## <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>准备要运行 x11 的客户端和 VM（仅适用于 Windows 客户端）
这是一个可选步骤；如果正在使用 Linux 客户端或已具备 x11设置，可跳过此步骤。

### <a name="1--download-putty-and-xming-to-your-windows-computer"></a>1.将 PuTTY 和 Xming 下载到 Windows 计算机：

  * [下载 PuTTY](http://www.putty.org/)
  * [下载 Xming](https://xming.en.softonic.com/)

### <a name="2--after-you-install-putty-in-the-putty-folder-for-example-cprogram-filesputty-run-puttygenexe-putty-key-generator"></a>2.安装 PuTTY 后，请在 PuTTY 文件夹（例如 C:\Program Files\PuTTY）中运行 puttygen.exe（PuTTY 密钥生成器）。

### <a name="3--in-putty-key-generator"></a>3.在 PuTTY 密钥生成器中：

- 若要生成密钥，请选择“生成”按钮。
- 复制密钥的内容 (Ctrl+C)。
- 选择“保存私钥”按钮。
- 忽略显示的警告，然后选择“确定”。

  ![PuTTY 密钥生成器页屏幕截图](./media/oracle-asm/puttykeygen.png)

### <a name="4--in-your-vm-run-these-commands"></a>4.在 VM 中运行以下命令：

```bash
# sudo su - grid
$ mkdir .ssh (if not already created)
$ cd .ssh
```

### <a name="5--create-a-file-named-authorizedkeys-paste-the-contents-of-the-key-in-this-file-and-then-save-the-file"></a>5.创建名为 authorized_keys 的文件。 在此文件中粘贴密钥的内容，然后保存该文件。

> [!NOTE]
> 该密钥必须包含字符串 `ssh-rsa`。 此外，密钥的内容必须是单行文本。
>  

### <a name="6--start-putty-in-the-category-pane-go-to-connection--ssh--auth-in-the-private-key-file-for-authentication-box-browse-to-the-key-that-you-generated-earlier"></a>6.启动 PuTTY。 在“类别”窗格中，转到“连接” > “SSH” > “身份验证”。 在“用于身份验证的私钥文件”框中，浏览到前面生成的密钥。

  ![“设置私钥”页屏幕截图](./media/oracle-asm/setprivatekey.png)

### <a name="7--in-the-category-pane-go-to-connection--ssh--x11-select-the-enable-x11-forwarding-box"></a>7.在“类别”窗格中，转到“连接” > “SSH” > “X11”。 选中“启用 X11 转发”框。

  ![“启用 X11”页屏幕截图](./media/oracle-asm/enablex11.png)

### <a name="8-in-the-category-pane-go-to-session-enter-the-host-information-and-then-select-open"></a>8.在“类别”窗格中，转到“会话”。 输入主机信息，然后选择“打开”。

  ![“会话”页屏幕截图](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>安装 Oracle Grid Infrastructure

若要安装 Oracle Grid Infrastructure，请完成以下步骤：

### <a name="1-sign-in-as-grid-you-should-be-able-to-sign-in-without-being-prompted-for-a-password"></a>1.以 grid 身份登录。 （应该可以直接登录，系统不会提示输入密码。） 

> [!NOTE]
> 在开始安装之前，请确保 Xming 正在运行。

```bash
$ cd /opt/grid
$ ./runInstaller
```

  此时将打开 Oracle Grid Infrastructure 12c Release 1 安装程序。 （安装程序可能需要花费几分钟时间来启动。）

### <a name="2-on-the-select-installation-option-page-select-install-and-configure-oracle-grid-infrastructure-for-a-standalone-server"></a>2.在“选择安装选项”页上，选择“安装并配置适用于独立服务器的 Oracle Grid Infrastructure”。

  ![安装程序中的“选择安装选项”页屏幕截图](./media/oracle-asm/install01.png)

### <a name="3-on-the-select-product-languages-page-select-english-or-the-language-that-you-want"></a>3.在“选择产品语言”页上，选择“英语”或所需的语言。

  ![安装程序中的“选择产品语言”页屏幕截图](./media/oracle-asm/install02.png)

### <a name="4-on-the-create-asm-disk-group-page"></a>4.在“创建 ASM 磁盘组”页上：
- 输入磁盘组的名称。
- 在“冗余”下面，选择“外部”。
- 在“分配单元大小”下面，选择“4”。
- 在“添加磁盘”下面，选择“ORCLASMSP”。

  ![安装程序中的“创建 ASM 磁盘组”页屏幕截图](./media/oracle-asm/install03.png)

### <a name="5-on-the-specify-asm-password-page-select-the-use-same-passwords-for-these-accounts-option-and-enter-a-password"></a>5.在“指定 ASM 密码”页上，选择“对这些帐户使用相同的密码”选项，然后输入密码。

  ![安装程序中的“指定 ASM 密码”页屏幕截图](./media/oracle-asm/install04.png)

### <a name="6-optional-on-the-specify-management-options-page-select-the-register-with-enterprise-manager-em-cloud-control-box"></a>6.（可选）在“指定管理选项”页上，选中“使用企业管理器(EM)云控件注册”框。

  ![安装程序中的“指定管理选项”页屏幕截图](./media/oracle-asm/install05.png)

### <a name="7-on-the-privileged-operating-system-groups-page-use-the-default-settings"></a>7.在“特权操作系统组”页上，请使用默认设置。

  ![安装程序中的“特权操作系统组”页屏幕截图](./media/oracle-asm/install06.png)

### <a name="8-on-the-specify-installation-location-page-use-default-settings"></a>8.在“指定安装位置”页面上，请用默认设置。

  ![安装程序中的“指定安装位置”页屏幕截图](./media/oracle-asm/install07.png)

### <a name="9-on-the-create-inventory-page-enter-or-browse-to-the-folder-location"></a>9.在“创建清单”页上，输入或浏览到相应的文件夹位置。

  ![安装程序中的“创建清单”页屏幕截图](./media/oracle-asm/install08.png)

### <a name="10-on-the-root-script-execution-configuration-page-select-the-automatically-run-configuration-scripts-box-then-select-the-use-root-user-credential-option-and-enter-the-root-user-password"></a>10.在“Root 脚本执行配置”页上，选中“自动运行配置脚本”框。 然后，选择“使用 "root" 用户凭据”选项，并输入 root 用户密码。

  ![安装程序中的“Root 脚本执行配置”页屏幕截图](./media/oracle-asm/install09.png)

### <a name="11-on-the-perform-prerequisite-checks-page-select-fix--check-again"></a>11.在“执行先决条件检查”页上，选择“修复并再次检查”。

  ![安装程序中的“执行先决条件检查”页屏幕截图](./media/oracle-asm/install10.png)

### <a name="12-on-the-fixup-script-page-select-ok"></a>12.在“修复脚本”页上，选择“确定”。

  ![安装程序中的“修复脚本”页屏幕截图](./media/oracle-asm/install11.png)

### <a name="13-on-the-summary-page-review-your-settings-selections-and-then-select-install"></a>13.在“摘要”页上复查设置选项，然后选择“安装”。

  ![安装程序中的“摘要”页屏幕截图](./media/oracle-asm/install12.png)

### <a name="14-a-warning-dialog-box-appears-select-yes-to-continue"></a>14.此时将显示一个警告对话框。 选择“是”继续。

  ![警告对话框屏幕截图](./media/oracle-asm/install14.png)

### <a name="15-on-the-finish-page-select-close-to-finish-the-installation"></a>15.在“完成”页上，选择“关闭”完成安装。

  ![安装程序中的“完成”页屏幕截图](./media/oracle-asm/install16.png)

## <a name="set-up-your-oracle-asm-installation"></a>设置 Oracle ASM 安装

若要设置 Oracle ASM 安装，请完成以下步骤：

### <a name="1--sign-in-as-grid-from-your-x11-session"></a>1.通过 X11 会话以 grid 身份登录：

  ```bash
  $ cd /u01/app/grid/product/12.1.0/grid/bin
  $ ./asmca
  ```

  此时将打开 Oracle ASM 配置助手。

### <a name="2--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>2.在“配置 ASM: 磁盘组”页上选择“创建”按钮，然后选择“显示高级选项”。

  ![“配置 ASM: 磁盘组”页屏幕截图](./media/oracle-asm/asm01.png)

### <a name="3--on-the-create-disk-group-page"></a>3.在“创建磁盘组”页上：

- 输入磁盘组名称。
- 在“选择成员磁盘”下面，选择“ORCL_DATA”和“ORCL_DATA1”。
- 在“分配单元大小”下面，选择“4”。

  ![“创建磁盘组”页屏幕截图](./media/oracle-asm/asm02.png)

### <a name="4--on-the-configure-asm-disk-groups-page-select-the-create-button-and-then-select-show-advanced-options"></a>4.在“配置 ASM: 磁盘组”页上选择“创建”按钮，然后选择“显示高级选项”。

  ![“配置 ASM: 磁盘组”页屏幕截图](./media/oracle-asm/asm03.png)

### <a name="5--on-the-create-disk-group-page"></a>5.在“创建磁盘组”页上：

- 输入磁盘组名称。
- 在“冗余”下面，选择“外部”。
- 在“选择成员磁盘”下面，选择“ORCL_FRA”。
- 在“分配单元大小”下面，选择“4”。

  ![“创建磁盘组”页屏幕截图](./media/oracle-asm/asm04.png)

### <a name="6--select-exit-to-close-asm-configuration-assistant"></a>6.选择“退出”关闭 ASM 配置助手。

  ![包含“退出”按钮的“配置 ASM: 磁盘组”页屏幕截图](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>创建数据库

该 Oracle 软件已安装在 Azure 应用商店映像中。 若要安装数据库，请完成以下步骤：

### <a name="1--switch-users-to-the-oracle-superuser-and-then-initialize-the-listener-for-logging"></a>1.将用户切换到 oracle superuser，然后初始化用于日志记录的侦听器：

  ```bash
  $ su - oracle
  Password:
  $ cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
  $ ./dbca
   ```
   此时将打开配置助手。

### <a name="2--on-the-database-operation-page-select-create-database"></a>2.在“数据库操作”页上，选择“创建数据库”。

  ![“数据库操作”页屏幕截图](./media/oracle-asm/createdb01.png)
  
### <a name="3-on-the-creation-mode-page"></a>3.在“创建模式”页上：

- 输入数据库的名称。
- 对于“存储类型”，请选择“自动存储管理(ASM)”。
- 对于“数据库文件位置”，请浏览到要使用的文件夹。
- 对于“快速恢复区域”，请浏览到要使用的文件夹。

  ![“创建模式”页屏幕截图](./media/oracle-asm/createdb02.png)

### <a name="4--on-the-summary-page-review-your-settings-selections-and-then-select-finish-to-create-the-database"></a>4.在“摘要”页上复查设置选项，然后选择“完成”创建数据库。

  ![“摘要”页屏幕截图](./media/oracle-asm/createdb03.png)

### <a name="5---optional-on-the-finish-page-to-change-the-passwords-select-password-management"></a>5. （可选）若要在“完成”页上更改密码，请选择“密码管理”。

  ![“完成”页屏幕截图](./media/oracle-asm/createdb04.png)


## <a name="delete-the-vm"></a>删除 VM

如果不再需要 VM，可以使用以下命令删除资源组、VM 和所有相关的资源：

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

[教程：创建高可用性虚拟机](../../linux/create-cli-complete.md)

[浏览 VM 部署 Azure CLI 示例](../../linux/cli-samples.md)

