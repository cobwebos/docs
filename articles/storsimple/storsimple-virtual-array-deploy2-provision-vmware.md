---
title: "在 VMware 中预配 StorSimple 虚拟阵列 | Microsoft 文档"
description: "此教程为 StorSimple 虚拟阵列部署系列的第二个教程，介绍如何在 VMware 中预配虚拟设备。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 0425b2a9-d36f-433d-8131-ee0cacef95f8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/15/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 118521a127b2e4b765efabdbdde71605440d81c7
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-storsimple-virtual-array---provision-in-vmware"></a>部署 StorSimple 虚拟阵列 - 在 VMware 中预配
![](./media/storsimple-virtual-array-deploy2-provision-vmware/vmware4.png)

## <a name="overview"></a>概述
本教程介绍如何在运行 VMware ESXi 5.5 及更高版本的主机系统上预配和连接 StorSimple 虚拟阵列。 本文适用于在 Azure 门户和 Microsoft Azure 政府云中部署 StorSimple 虚拟阵列的情况。

需要管理员权限才能预配和连接虚拟设备。 完成预配和初始设置可能需要大约 10 分钟。

## <a name="provisioning-prerequisites"></a>预配先决条件
在运行 VMware ESXi 5.5 及更高版本的主机系统上预配虚拟设备的先决条件如下所示。

### <a name="for-the-storsimple-device-manager-service"></a>对于 StorSimple Device Manager 服务
在开始之前，请确保：

* 已完成[为 StorSimple 虚拟阵列准备门户](storsimple-virtual-array-deploy1-portal-prep.md)中的所有步骤。
* 已从 Azure 门户下载 VMware 的虚拟设备映像。 有关详细信息，请参阅[为 StorSimple 虚拟阵列准备门户指南](storsimple-virtual-array-deploy1-portal-prep.md)中的**步骤 3：下载虚拟设备映像**。

### <a name="for-the-storsimple-virtual-device"></a>对于 StorSimple 虚拟设备
在部署虚拟设备之前，请确保：

* 有权访问运行 Hyper-V（2008 R2 或更高版本）的主机系统，以便预配设备。
* 主机系统能够将以下资源专用于预配虚拟设备：

  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。 如果打算将虚拟阵列配置为文件服务器，8 GB 最多支持 200 万个文件。 需要 16 GB 的 RAM 才能支持 200-400 万个文件。
  * 一个网络接口。
  * 一个 500 GB 的用于系统数据的虚拟磁盘。

### <a name="for-the-network-in-datacenter"></a>对于数据中心的网络
在开始之前，请确保：

* 已查看部署 StorSimple 虚拟设备的网络要求，并已根据要求配置数据中心网络。 

## <a name="step-by-step-provisioning"></a>分步预配
若要预配和连接到虚拟设备，需执行以下步骤：

1. 请确保主机系统有足够的资源来满足最低的虚拟设备要求。
2. 预配虚拟机监控程序中的虚拟设备。
3. 启动虚拟设备并获取 IP 地址。

## <a name="step-1-ensure-host-system-meets-minimum-virtual-device-requirements"></a>步骤 1：确保主机系统满足最小虚拟设备要求
若要创建虚拟设备，需满足以下条件：

* 有权访问运行 VMware ESXi Server 5.5 及更高版本的主机系统。
* 在系统上存在用于管理 ESXi 主机的 VMware vSphere 客户端。

  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。 如果打算将虚拟阵列配置为文件服务器，8 GB 最多支持 200 万个文件。 需要 16 GB 的 RAM 才能支持 200-400 万个文件。
  * 一个连接到网络的网络接口，可以将流量路由到 Internet。 为了确保设备的理想运行，最小 Internet 带宽应为 5 Mbps。
  * 一个 500 GB 的用于数据的虚拟磁盘。

## <a name="step-2-provision-a-virtual-device-in-hypervisor"></a>步骤 2：预配虚拟机监控程序中的虚拟设备
执行以下步骤，预配虚拟机监控程序中的虚拟设备。

1. 复制系统中的虚拟设备映像。 通过 Azure 门户已下载该虚拟映像。

   1. 确保已下载最新映像文件。 如果以前下载过此映像，请再下载一次，确保其为最新映像。 最新映像有两个文件（而不是一个）。
   2. 记下复制映像的位置，因为在以后的过程中将使用此映像。

2. 使用 vSphere 客户端登录到 ESXi 服务器。 需要有管理员权限才能创建虚拟机。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image1.png)
3. 在 vSphere 客户端的左窗格的清单部分，选择“ESXi 服务器”。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image2.png)
4. 将 VMDK 上传到 ESXi 服务器。 导航到右窗格中的“配置”选项卡。 在“硬件”下选择“存储”。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image3.png)
5. 在右窗格的“数据存储”下，选择要将 VMDK 上传到其中的数据存储。 数据存储必须为 OS 和数据磁盘留有足够的可用空间。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image4.png)
6. 右键单击，并选择“浏览数据存储”。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image5.png)
7. 此时会显示“数据存储浏览器”窗口。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image6.png)
8. 在工具栏中，单击 ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image7.png) 图标创建新的文件夹。 指定文件夹名称，将其记录下来。 稍后创建虚拟机时需用到此文件夹名称（建议的最佳做法）。 单击“确定”。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image8.png)
9. 新文件夹会显示在“数据存储浏览器”的左窗格中。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image9.png)
10. 单击上载图标 ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image10.png)，并选择“上载文件”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image11.png)
11. 浏览并指向已下载的 VMDK 文件。 有两个文件。 选择要上传的文件。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image12m.png)
12. 单击“打开”。 此时会开始将 VMDK 文件上传到指定的数据存储。 上传文件可能需要数分钟的时间。
13. 上传完成后，可以在数据存储的已创建文件夹中看到该文件。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image14.png)

    现在将第二个 VMDK 文件上传到同一数据存储。
14. 返回到 vSphere 客户端窗口。 选中 ESXi 服务器后，右键单击并选择“新建虚拟机”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image15.png)
15. 此时会显示“创建新的虚拟机”窗口。 在“配置”页上选择“自定义”选项。 单击“下一步”。
    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image16.png)
16. 在“名称和位置”页上，指定虚拟机的名称。 该名称应与此前在步骤 8 中指定的文件夹名称配置（建议的最佳做法）。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image17.png)
17. 在“存储”页上，选择要用于预配 VM 的数据存储。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image18.png)
18. 在“虚拟机版本”页上，选择“虚拟机版本: 8”。 版本 8 到 11 均受支持。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image19.png)
19. 在“来宾操作系统”页上，选择“Windows”作为“来宾操作系统”。 从下拉列表中选择“Microsoft Windows Server 2012 (64 位)”作为“版本”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image20.png)
20. 在“CPU”页上调整“虚拟插槽数”和“每个虚拟插槽的核心数”，使“总核心数”为 4（或 4 以上）。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image21.png)
21. 在“内存”页上，将 RAM 指定为 8 GB（或 8 GB 以上）。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image22.png)
22. 在“网络”页上，指定网络接口的数目。 最低要求是一个网络接口。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image23.png)
23. 在“SCSI 控制器”页上，接受默认的“LSI 逻辑 SAS 控制器”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image24.png)
24. 在“选择磁盘”页上，选择“使用现有虚拟磁盘”。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image25.png)
25. 在“选择现有磁盘”页的“磁盘文件路径”下，单击“浏览”。 此时会打开“浏览数据存储”对话框。 导航到上传 VMDK 的位置。 此时在数据存储中只会看到一个文件，因为最初上传的两个文件已合并。 选择该文件，并单击“确定”。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image26.png)
26. 在“高级选项”页上，接受默认值，并单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image27.png)
27. 在“准备完成”页上，查看与新虚拟机关联的所有设置。 选中“在完成前编辑虚拟机设置”。 单击“继续”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image28.png)
28. 在“虚拟机属性”页的“硬件”选项卡中，找到设备硬件。 选择“新建硬盘”。 单击 **“添加”**。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image29.png)
29. 此时会显示“添加硬件”窗口。 在“设备类型”页的“选择要添加的设备类型”下选择“硬盘”，并单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image30.png)
30. 在“选择磁盘”页上，选择“创建新的虚拟磁盘”。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image31.png)
31. 在“创建磁盘”页上，将“磁盘大小”更改为 500 GB（或 500 GB 以上）。 500 GB 为最低要求，可以预配更大的磁盘。 请注意，不能扩展或压缩已预配的磁盘。 如需详细了解要预配的磁盘的大小，请查看[最佳实践文档](storsimple-ova-best-practices.md)的“调整大小”部分。 在“磁盘预配”下，选择“精简预配”。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image32.png)
32. 在“高级选项”页上，接受默认值。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image33.png)
33. 在“准备完成”页上，查看磁盘选项。 单击“完成” 。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image34.png)
34. 返回到“虚拟机属性”页。 新硬盘已添加到虚拟机。 单击“完成” 。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image35.png)
35. 在右窗格中选中虚拟机以后，导航到“摘要”选项卡。 查看虚拟机的设置。

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image36.png)

虚拟机现已预配好。 下一步是启动该虚拟机并获取 IP 地址。

## <a name="step-3-start-the-virtual-device-and-get-the-ip"></a>步骤 3：启动虚拟设备并获取 IP
执行以下步骤，启动虚拟设备并与其进行连接。

#### <a name="to-start-the-virtual-device"></a>启动虚拟设备
1. 启动虚拟设备。 在 vSphere 配置管理器的左窗格中选择设备，并右键单击以显示上下文菜单。 选择“电源”，并选择“启动”。 此时会启动虚拟机。 可以在 vSphere 客户端底部的“最新任务”窗格中查看状态。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image37.png)
2. 完成设置任务需要几分钟时间。 设备运行以后，导航到“控制台”选项卡。 发送 Ctrl+Alt+Delete 登录到设备。 也可将光标指向控制台窗口，然后按 Ctrl+Alt+Insert。 默认用户为 *StorSimpleAdmin*，默认密码为 *Password1*。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image38.png)
3. 出于安全原因，设备管理员密码在第一次登录后过期。 系统会提示用户更改密码。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image39.png)
4. 请输入至少包含 8 个字符的密码。 密码必须包含以下要求中 4 项内容中的 3 项：大写、小写、数字、特殊字符。 再次输入密码进行确认。 用户会收到密码已更改的通知。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image40.png)
5. 成功更改密码后，虚拟设备会重新启动。 等待重新启动完成。 会显示设备的 Windows PowerShell 控制台和进度栏。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image41.png)
6. 步骤 6-8 仅适用于在非 DHCP 环境中启动的情况。 如果是在 DHCP 环境中，则请跳过这些步骤，转到步骤 9。 如果已在非 DHCP 环境中启动设备，则会看到以下屏幕。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image42m.png)

   接下来，配置网络。
7. 使用 `Get-HcsIpAddress` 命令列出在虚拟设备上启用的网络接口。 如果设备启用了单个网络接口，则分配到该接口的默认名称为 `Ethernet`。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image43m.png)
8. 使用 `Set-HcsIpAddress` cmdlet 配置网络。 下面显示了一个示例：

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image44.png)
9. 完成初始设置并启动设备以后，会显示设备横幅文本。 记下显示在横幅文本中的 IP 地址和 URL，以便管理设备。 需使用该 IP 地址连接到虚拟设备的 Web UI 并完成本地设置和注册。

   ![](./media/storsimple-virtual-array-deploy2-provision-vmware/image45.png)
10. （可选）仅是在政府云中部署设备时，才执行此步骤。 此时会在设备上启用美国联邦信息处理标准 (FIPS) 模式。 FIPS 140 标准定义的加密算法已经过批准，可以用于美国联邦政府计算机系统来保护敏感数据。

    1. 若要启用 FIPS 模式，请运行以下 cmdlet：

        `Enable-HcsFIPSMode`
    2. 启用 FIPS 模式后请重新启动设备，以便加密验证生效。

       > [!NOTE]
       > 可以在设备上启用或禁用 FIPS 模式。 不支持在设备上交替使用 FIPS 模式和非 FIPS 模式。
       >
       >

如果设备不符合最低配置要求，则会在横幅文本中显示错误（如下所示）。 需修改设备配置，使之有足够的资源来满足最低要求。 然后，你可以重新启动，并连接到设备。 请参阅[步骤 1：确保主机系统满足最小虚拟设备要求](#step-1-ensure-host-system-meets-minimum-virtual-device-requirements)中的最低配置要求。

![](./media/storsimple-virtual-array-deploy2-provision-vmware/image46.png)

若在使用本地 Web UI 进行初始配置期间遇到其他错误，请参阅以下工作流：

* 运行诊断测试，[排除 Web UI 设置故障](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)。
* [生成日志包并查看日志文件](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## <a name="next-steps"></a>后续步骤
* [将 StorSimple 虚拟阵列设置为文件服务器](storsimple-virtual-array-deploy3-fs-setup.md)
* [将 StorSimple 虚拟阵列设置为 iSCSI 服务器](storsimple-virtual-array-deploy3-iscsi-setup.md)
