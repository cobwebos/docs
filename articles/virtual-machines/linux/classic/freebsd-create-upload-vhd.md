---
title: "创建和上传 FreeBSD VM 映像 | Microsoft Docs"
description: "了解如何创建和上传包含 FreeBSD 操作系统的虚拟硬盘 (VHD) 以创建 Azure 虚拟机。"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>创建 FreeBSD VHD 并将其上传到 Azure
本文说明如何创建和上传包含 FreeBSD 操作系统的虚拟硬盘 (VHD)。 将其上传后，可以使用它作为你自己的映像在 Azure 中创建虚拟机 (VM)。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。 有关使用 Resource Manager 模型上传 VHD 的信息，请参阅[此处](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="prerequisites"></a>先决条件
本文假设拥有以下项目：

* **Azure 订阅** - 如果没有帐户，只需几分钟即可创建一个。 如果有 MSDN 订阅，请参阅 [Visual Studio 订户的每月 Azure 信用额度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 否则，请了解如何[创建一个免费试用帐户](https://azure.microsoft.com/pricing/free-trial/)。  
* **Azure PowerShell 工具** - 必须安装 Azure PowerShell 模块并将其配置为使用 Azure 订阅。 若要下载该模块，请参阅 [Azure 下载](https://azure.microsoft.com/downloads/)。 可在此处获取介绍如何安装和配置该模块的教程。 使用 [Azure 下载](https://azure.microsoft.com/downloads/) cmdlet 上传 VHD。
* **安装在 .vhd 文件中的 FreeBSD 操作系统** - 必须将受支持的 FreeBSD 操作系统安装到虚拟硬盘中。 可使用多个工具创建 .vhd 文件。 例如，可使用虚拟化解决方案（如 Hyper-V）创建 .vhd 文件并安装操作系统。 有关如何安装和使用 Hyper-V 的说明，请参阅[安装 Hyper-V 和创建虚拟机](http://technet.microsoft.com/library/hh846766.aspx)。

> [!NOTE]
> Azure 不支持更新的 VHDX 格式。 可使用 Hyper-V 管理器或 [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx) cmdlet 将磁盘转换为 VHD 格式。 此外，MSDN 上还有[有关如何将 FreeBSD 与 Hyper-V 配合使用的教程](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)。
>
>

此任务包括以下四个步骤：

## <a name="step-1-prepare-the-image-for-upload"></a>步骤 1：准备要上传的映像
在安装了 FreeBSD 操作系统的虚拟机中，完成以下过程：

1. 启用 DHCP。

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. 启用 SSH。

    请确保已安装 SSH 服务器且已将其配置为在引导时启动。 从 FreeBSD 光盘安装后，将默认启用它。 
3. 设置串行控制台。

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. 安装 sudo。

    在 Azure 中禁用根帐户。 这意味着需要通过无特权用户利用 sudo 使用提升的权限运行命令。

        # pkg install sudo
   
5. Azure 代理的先决条件。

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. 安装 Azure 代理。

    始终可以在 [github](https://github.com/Azure/WALinuxAgent/releases) 上找到 Azure 代理的最新版本。 2.0.10 + 版正式支持 FreeBSD 10 和 10.1，2.1.4 + 版（包括 2.2.x）正式支持 FreeBSD 10.2 和更高版本。

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    对于 2.0，让我们使用 2.0.16 作为示例：

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    对于 2.1，让我们使用 2.1.4 作为示例：

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > 安装 Azure 代理之后，验证它是否正在运行是一个好主意：
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. 取消预配系统。

    取消预配系统以清除系统并使其适用于重新预配。 以下命令还会删除上次预配的用户帐户和关联数据：

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    现在，可以关闭 VM 了。

## <a name="step-2-prepare-the-connection-to-azure"></a>步骤 2：准备连接到 Azure
请确保在经典部署模型中使用 Azure CLI (`azure config mode asm`)，并登录帐户：

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>步骤 3：上传 .vhd 文件

需要一个存储帐户，以便向其上传 VHD 文件。 可以选取现有存储帐户，也可以[创建新的存储帐户](../../../storage/common/storage-create-storage-account.md)。

在上传 .vhd 文件时，可以将该文件放置在 Blob 存储中的任何位置。 以下是上传该文件时将使用的一些术语：

* **BlobStorageURL** 是在步骤 2 中创建的存储帐户的 URL。
* **YourImagesFolder** 是 Blob 存储中用于存储映像的容器。
* **VHDName** 是显示在 Azure 经典门户中用于标识虚拟硬盘的标签。
* **PathToVHDFile** 是 .vhd 文件的完整路径和名称。

从你在上一步中使用的 Azure PowerShell 窗口中，键入：

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>步骤 4：使用上传的 .vhd 文件创建 VM
上传 .vhd 文件后，可以将其作为映像添加到与订阅关联的自定义映像列表中，并使用此自定义映像创建虚拟机。

1. 从你在上一步中使用的 Azure PowerShell 窗口中，键入：

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > 使用 Linux 作为 OS 类型。 当前的 Azure PowerShell 版本只接受“Linux”或“Windows”作为参数。
   >
   >
2. 完成前面的步骤后，在 Azure 经典门户上选择“映像”选项卡时，将列出新映像。  

    ![选择映像](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. 从库中创建虚拟机。 此新映像现在会显示在“我的映像”下。
4. 选择此新映像。 接下来，按照提示完成设置主机名、密码、SSH 密钥等操作。

    ![自定义映像](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. 完成预配后，会看到 FreeBSD VM 在 Azure 中运行。

    ![azure 中的 FreeBSD 映像](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
