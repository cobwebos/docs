---
title: 有关如何在 VMware 中预配 Azure Data Box Gateway 的教程 | Microsoft Docs
description: 有关如何部署 Azure Data Box Gateway 的第二个教程介绍如何在 VMware 中预配虚拟设备。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/01/2018
ms.author: alkohli
ms.openlocfilehash: ea4203c45f482b990122a966fc2ec13b3fb41c84
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167148"
---
# <a name="tutorial-provision-azure-data-box-gateway-in-vmware-preview"></a>教程：在 VMware（预览版）中预配 Azure Data Box Gateway

## <a name="overview"></a>概述

本教程介绍如何在运行 VMware ESXi 6.0 或 6.5 的主机系统上预配 Data Box Gateway。 

需要管理员权限才能预配和连接虚拟设备。 完成预配和初始设置可能需要大约 10 分钟。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 确保主机满足最低设备要求
> * 在 VMware 中预配虚拟设备
> * 启动虚拟设备并获取 IP 地址

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!IMPORTANT]
> - Data Box Gateway 为预览版。 在订购和部署此解决方案之前，请查看 [Azure 预览版服务的条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

在运行 VMware ESXi 6.0 或 6.5 的主机系统上预配虚拟设备的先决条件如下所示。

### <a name="for-the-data-box-gateway-resource"></a>对于 Data Box Gateway 资源

在开始之前，请确保：

* 已完成[为 Data Box Gateway 准备门户](data-box-gateway-deploy-prep.md)中的所有步骤。
* 已按照[为 Data Box Gateway 准备门户](data-box-gateway-deploy-prep.md)中的说明从 Azure 门户下载 VMware 的虚拟设备映像。

  > [!IMPORTANT]
  > 在 Data Box Gateway 上运行的软件只能结合 Data Box Gateway 资源使用。

### <a name="for-the-data-box-gateway-virtual-device"></a>对于 Data Box Gateway 虚拟设备

在部署虚拟设备之前，请确保：

* 有权访问运行 VMware（ESXi 6.0 或 6.5）的主机系统，以便预配设备。
* 主机系统能够将以下资源专用于预配虚拟设备：

  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。
  * 一个网络接口。
  * 一个 250 GB 的 OS 磁盘。
  * 一个 2 TB 的用于系统数据的虚拟磁盘。

### <a name="for-the-network-in-datacenter"></a>对于数据中心的网络

开始之前：

- 查看部署 Data Box Gateway 的网络要求，并根据要求配置数据中心网络。 有关详细信息，请参阅 [Data Box Gateway 网络要求](data-box-gateway-system-requirements.md#networking-requirements)。
- 请确保最小 Internet 带宽为 20 Mbps，以便实现设备的理想运行。

## <a name="check-the-host-system"></a>检查主机系统

若要创建虚拟设备，需满足以下条件：

* 有权访问运行 VMware ESXi Server 6.0 或 6.5 的主机系统。 主机系统能够将以下资源专用于虚拟设备：
 
  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。 
  * 一个连接到网络的网络接口，可以将流量路由到 Internet。 
  * 一个 250 GB 的 OS 磁盘。
  * 一个 2 TB 的用于数据的虚拟磁盘。
* 在系统上存在用于管理 ESXi 主机的 VMware vSphere 客户端。


## <a name="provision-a-virtual-device-in-hypervisor"></a>预配虚拟机监控程序中的虚拟设备

执行以下步骤，预配虚拟机监控程序中的虚拟设备。

1. 复制系统中的虚拟设备映像。 已通过 Azure 门户下载该虚拟映像（两个文件）。 记下复制映像的位置，因为在以后的过程中将使用此映像。

2. 使用 vSphere Web 客户端登录到 ESXi 服务器。 需要有管理员权限才能创建虚拟机。

   ![](./media/data-box-gateway-deploy-provision-vmware/image1.png)
  
3. 将 VMDK 上传到 ESXi 服务器。 在“导航器”窗格中，选择“存储”。

   ![](./media/data-box-gateway-deploy-provision-vmware/image2.png)

4. 在右窗格的“数据存储”下，选择要将 VMDK 上传到其中的数据存储。 

    - 数据存储必须为 VMFS5 类型。 
    - 数据存储还必须为 OS 和数据磁盘留有足够的可用空间。
   
5. 右键单击，并选择“浏览数据存储”。

   ![](./media/data-box-gateway-deploy-provision-vmware/image3.png)

6. 此时会显示“数据存储浏览器”窗口。

   ![](./media/data-box-gateway-deploy-provision-vmware/image4.png)

7. 在工具栏中，单击“创建目录”图标创建新的文件夹。 指定文件夹名称，将其记录下来。 稍后创建虚拟机时需用到此文件夹名称（建议的最佳做法）。 单击“创建目录”。

   ![](./media/data-box-gateway-deploy-provision-vmware/image5.png)

8. 新文件夹会显示在“数据存储浏览器”的左窗格中。 单击“上传”图标，然后选择“上传文件”。

    ![](./media/data-box-gateway-deploy-provision-vmware/image6.png)

9. 浏览并指向已下载的 VMDK 文件。 有两个文件。 选择要上传的文件。

    ![](./media/data-box-gateway-deploy-provision-vmware/image7.png)

10. 单击“打开”。 此时会开始将 VMDK 文件上传到指定的数据存储。 上传文件可能需要数分钟的时间。
11. 上传完成后，可以在数据存储的已创建文件夹中看到该文件。 现在将第二个 VMDK 文件上传到同一数据存储。 两个文件在上传后会合并成单个文件。 然后会在目录中看到一个文件。

    ![](./media/data-box-gateway-deploy-provision-vmware/image8.png)

12. 返回到 vSphere 客户端窗口。 在“导航器”窗格中，选择“虚拟机”。 在右窗格上，单击“创建/注册 VM”。

    ![](./media/data-box-gateway-deploy-provision-vmware/image9.png)

13. 此时会显示“新建虚拟机”。 在“选择创建类型”下选择“新建虚拟机”，然后单击“下一步”。
    ![](./media/data-box-gateway-deploy-provision-vmware/image10.png)

14. 在“选择名称以及 OS 名称和位置”页上，指定虚拟机的“名称”。 该名称应与此前在步骤 7 中指定的文件夹名称配置（建议的最佳做法）。 选择“Windows”作为“来宾 OS 系列”，选择“Microsoft Windows Server 2016 (64 位)”作为“来宾 OS 版本”。 单击“下一步”。

    ![](./media/data-box-gateway-deploy-provision-vmware/image11.png)

15. 在“选择存储”页上，选择要用于预配 VM 的数据存储。 单击“下一步”。

    ![](./media/data-box-gateway-deploy-provision-vmware/image12.png)
16. 在“自定义设置”页上，将“CPU”设置为 4，将“内存”设置为 8192 MB（或更高），将“硬盘 1”设置为 2 TB（或更高）。 选择要添加的 **SCSI 硬盘**。 本例中为“LSI 逻辑 SAS”。 **静态 IDE 磁盘不受支持。** “硬盘 1”为虚拟数据磁盘。 请注意，不能压缩已预配的磁盘。

    ![](./media/data-box-gateway-deploy-provision-vmware/image13.png)

    在同一页上单击“添加硬盘”，然后选择“现有硬盘”。 在数据存储中选择 VMDK 文件。 这将添加 OS 磁盘。 

     ![](./media/data-box-gateway-deploy-provision-vmware/image14.png)

    向下滚动，直到看到“新建硬盘”，然后将其展开以查看设置。 将“虚拟设备节点”设置为“IDE 控制器 0”。 单击“下一步”。

     ![](./media/data-box-gateway-deploy-provision-vmware/image15.png)

27. 在“准备完成”页上，查看与新虚拟机关联的所有设置。 验证 CPU 是否为 4，内存是否为 8192 MB，网络接口是否为 1，以及硬盘 2 是否有 IDE 控制器 0。 单击“完成”。 
   
    ![](./media/data-box-gateway-deploy-provision-vmware/image16.png)
    ![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

虚拟机现已预配好。 此时会看到指示此内容的通知，而新虚拟机则会添加到 VM 列表中。 

![](./media/data-box-gateway-deploy-provision-vmware/image17.png)

下一步是启动该虚拟机并获取 IP 地址。

> [!NOTE]
> 建议不要在虚拟设备上安装 VMware 工具（如上述设置）。 安装 VMware 工具将产生不支持的配置。

## <a name="start-the-virtual-device-and-get-the-ip"></a>启动虚拟设备并获取 IP

执行以下步骤，启动虚拟设备并与其进行连接。

#### <a name="to-start-the-virtual-device"></a>启动虚拟设备
1. 启动虚拟设备。 在右窗格的 VM 列表中选择设备，然后右键单击以显示上下文菜单。 选择“电源”，并选择“启动”。 此时会启动虚拟机。 可以在 Web 客户端的底部窗格中查看状态。

    ![](./media/data-box-gateway-deploy-provision-vmware/image19.png)

2. 请再一次选择 VM。 右键单击并选择“控制台”，然后选择“在新窗口中打开”。

    ![](./media/data-box-gateway-deploy-provision-vmware/image20.png)

3. 此时虚拟机控制台会在新窗口中打开。 

    ![](./media/data-box-gateway-deploy-provision-vmware/image21.png)

4. 设备运行以后，请在控制台窗口的中上部分指向并单击选项卡上的光标。 选择“来宾 OS”>“发送密钥”>“Ctrl+Alt+Del”。 这样会解锁 VM。

   ![](./media/data-box-gateway-deploy-provision-vmware/image22.png)

5. 提供用于登录计算机的密码。 默认密码为 Password1。

   ![](./media/data-box-gateway-deploy-provision-vmware/image23.png)

6. 步骤 5-7 仅适用于在非 DHCP 环境中启动的情况。 如果是在 DHCP 环境中，则请跳过这些步骤，转到步骤 8。 如果已在非 DHCP 环境中启动设备，则会看到指示此内容的消息：“使用 Set-HcsIPAddress cmdlet 来配置网络”。 
   
7. 若要配置网络，请在命令提示符处使用 `Get-HcsIpAddress` 命令列出在虚拟设备上启用的网络接口。 如果设备启用了单个网络接口，则分配到该接口的默认名称为 `Ethernet`。

8. 使用 `Set-HcsIpAddress` cmdlet 配置网络。 下面显示了一个示例：

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

9. 完成初始设置并启动设备以后，会显示设备横幅文本。 记下显示在横幅文本中的 IP 地址和 URL，以便管理设备。 将使用该 IP 地址连接到虚拟设备的 Web UI 并完成本地设置和激活。

   ![](./media/data-box-gateway-deploy-provision-vmware/image24.png)

如果设备不符合最低配置要求，则会在横幅文本中显示错误（如下所示）。 需修改设备配置，使之有足够的资源来满足最低要求。 然后即可重新启动设备并与之进行连接。 请参阅[检查主机系统是否满足最小虚拟设备要求](#check-the-host-system)中的最低配置要求。

<!---If you face any other error during the initial configuration using the local web UI, refer to the following workflows:

* Run diagnostic tests to [troubleshoot web UI setup](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors).
* [Generate log package and view log files](storsimple-ova-web-ui-admin.md#generate-a-log-package).-->

## <a name="next-steps"></a>后续步骤

在本教程中，我们已了解有关 Data Box Gateway 的主题，例如：

> [!div class="checklist"]
> * 确保主机满足最低设备要求
> * 在 VMware 中预配虚拟设备
> * 启动虚拟设备并获取 IP 地址

请继续学习下一教程，了解如何连接、设置和激活虚拟设备。

* [在 Data Box Gateway 上设置共享并与其连接](data-box-gateway-deploy-connect-setup-activate.md)

