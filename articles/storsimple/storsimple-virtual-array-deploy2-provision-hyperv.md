---
title: "在 Hyper-V 中预配 StorSimple 虚拟阵列 | Microsoft 文档"
description: "此教程为 StorSimple 虚拟阵列部署的第二个教程，介绍如何在 Hyper-V 中预配虚拟阵列。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 4354963c-e09d-41ac-9c8b-f21abeae9913
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 8209eb8df45d8c7c42e303ab3377dbde3255154a
ms.openlocfilehash: 924b3621e03b678e158cd1240864a903db6c8510
ms.lasthandoff: 03/01/2017

---
# <a name="deploy-storsimple-virtual-array---provision-in-hyper-v"></a>部署 StorSimple 虚拟阵列 - 在 Hyper-V 中预配
![](./media/storsimple-virtual-array-deploy2-provision-hyperv/hyperv4.png)

## <a name="overview"></a>概述
本教程介绍如何在主机系统上预配 StorSimple 虚拟阵列，此类主机系统在 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 上运行 Hyper-V。 本文适用于在 Azure 门户和 Microsoft Azure 政府云中部署 StorSimple 虚拟阵列的情况。

需要管理员权限才能预配和配置虚拟阵列。 完成预配和初始设置可能需要大约 10 分钟。

## <a name="provisioning-prerequisites"></a>预配先决条件
此部分介绍在主机系统上预配虚拟阵列的先决条件，此类主机系统在 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 上运行 Hyper-V。

### <a name="for-the-storsimple-device-manager-service"></a>对于 StorSimple Device Manager 服务
在开始之前，请确保：

* 已完成[为 StorSimple 虚拟阵列准备门户](storsimple-virtual-array-deploy1-portal-prep.md)中的所有步骤。
* 已从 Azure 门户下载 Hyper-V 的虚拟阵列映像。 有关详细信息，请参阅[为 StorSimple 虚拟阵列准备门户指南](storsimple-virtual-array-deploy1-portal-prep.md)中的**步骤 3：下载虚拟阵列映像**。

  > [!IMPORTANT]
  > 在 StorSimple 虚拟阵列上运行的软件只能结合 StorSimple Device Manager 服务使用。
  >
  >

### <a name="for-the-storsimple-virtual-array"></a>对于 StorSimple 虚拟阵列
在部署虚拟阵列之前，请确保：

* 有权访问在 Windows Server 2008 R2 或更高版本上运行 Hyper-V 的主机系统，以便预配设备。
* 主机系统能够将以下资源专用于预配虚拟阵列：

  * 至少 4 个核心。
  * 至少 8 GB 的 RAM。 如果打算将虚拟阵列配置为文件服务器，8 GB 最多支持 200 万个文件。 需要 16 GB 的 RAM 才能支持 200-400 万个文件。
  * 一个网络接口。
  * 一个 500 GB 的用于数据的虚拟磁盘。

### <a name="for-the-network-in-the-datacenter"></a>对于数据中心中的网络
在开始之前请查看网络要求，以便正确部署 StorSimple 虚拟阵列并配置数据中心网络。 有关详细信息，请参阅 [StorSimple 虚拟阵列网络要求](storsimple-ova-system-requirements.md#networking-requirements)。

## <a name="step-by-step-provisioning"></a>分步预配
若要预配和连接到虚拟阵列，需执行以下步骤：

1. 请确保主机系统有足够的资源来满足最低的虚拟阵列要求。
2. 在虚拟机监控程序中预配虚拟阵列。
3. 启动虚拟阵列并获取 IP 地址。

以下部分介绍其中的每个步骤。

## <a name="step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements"></a>步骤 1：确保主机系统满足最低虚拟阵列要求
若要创建虚拟阵列，需要：

* 在 Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 SP1 上安装 Hyper-V 角色。
* 将 Microsoft Windows 客户端上的 Microsoft Hyper-V 管理器连接到主机。

确保要在其上创建虚拟阵列的基础硬件（主机系统）能够将以下资源专用于虚拟阵列：

* 至少 4 个核心。
* 至少 8 GB 的 RAM。 如果打算将虚拟阵列配置为文件服务器，8 GB 最多支持 200 万个文件。 需要 16 GB 的 RAM 才能支持 200-400 万个文件。
* 一个网络接口。
* 一个 500 GB 的用于系统数据的虚拟磁盘。

## <a name="step-2-provision-a-virtual-array-in-hypervisor"></a>步骤 2：在虚拟机监控程序中预配虚拟阵列
执行以下步骤，预配虚拟机监控程序中的设备。

#### <a name="to-provision-a-virtual-array"></a>预配虚拟阵列
1. 在 Windows Server 主机上，将虚拟阵列映像复制到本地驱动器。 通过 Azure 门户已下载该映像（VHD 或 VHDX）。 记下复制映像的位置，因为在以后的过程中将使用此映像。
2. 打开“服务器管理器”。 单击右上角的“工具”，然后选择“Hyper-V 管理器”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image1.png)  

   如果运行的是 Windows Server 2008 R2，请打开 Hyper-V 管理器。 在服务器管理器中，单击“角色 > Hyper-V > Hyper-V 管理器”。
3. 在“Hyper-V 管理器”的作用域窗格中，右键单击系统节点打开上下文菜单，然后单击“新建” > “虚拟机”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image2.png)
4. 在新建虚拟机向导的“准备工作”页上，单击“下一步”。
5. 在“指定名称和位置”页上，提供虚拟阵列的“名称”。 单击“下一步”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image4.png)
6. 在“指定代数”页上选择设备映像类型，然后单击“下一步”。 如果使用的是 Windows Server 2008 R2，则不显示此页。

   * 如果已为 Windows Server 2012 或更高版本下载了 .vhdx 映像，请选择“第 2 代”。
   * 如果已为 Windows Server 2008 R2 或更高版本下载了 .vhd 映像，请选择“第 1 代”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image5.png)
7. 在“分配内存”页上，将“启动内存”指定为至少“8192 MB”，且不启用动态内存，然后单击“下一步”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image6.png)  
8. 在“配置网络”页上，指定连接到 Internet 的虚拟交换机，然后单击“下一步”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image7.png)
9. 在“连接虚拟硬盘”页上，选择“使用现有虚拟硬盘”，指定虚拟阵列映像（.vhdx 或 .vhd）的位置，然后单击“下一步”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image8m.png)
10. 查看“摘要”，然后单击“完成”创建虚拟机。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image9.png)
11. 满足最低要求需 4 个核心。 若要添加 4 个虚拟处理器，请在“Hyper-V 管理器”窗口中选择主机系统。 在“虚拟机”列表下的右窗格中，找到刚创建的虚拟机。 选择计算机名称，右键单击该名称后选择“设置”。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image10.png)
12. 在“设置”页的左窗格中，单击“处理器”。 在右窗格中，将“虚拟处理器数目”设置为 4（或 4 以上）。 单击“应用” 。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image11.png)
13. 若要满足最低要求，还需添加 500 GB 的虚拟数据磁盘。 在“设置”页中：

    1. 在左窗格中，选择“SCSI 控制器”。
    2. 在右窗格中，选择“硬盘驱动器”，然后单击“添加”。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image12.png)
14. 在“硬盘驱动器”页上，选择“虚拟硬盘”选项，然后单击“新建”。 此时会启动“新建虚拟硬盘向导”。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image13.png)
15. 在新建虚拟硬盘向导的“准备工作”页上，单击“下一步”。
16. 在“选择磁盘格式”页上，接受默认选项：“VHDX”格式。 单击“下一步”。 如果运行的是 Windows Server 2008 R2，则不会显示此屏幕。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image15.png)
17. 在“选择磁盘类型”页上，将虚拟硬盘类型设置为“动态扩展”（推荐）。 也可以选择“固定大小”磁盘，但可能需等待很长时间。 建议不要使用“差异”选项。 单击“下一步”。 在 Windows Server 2012 R2 和 Windows Server 2012 中，“动态扩展”是默认选项，而在 Windows Server 2008 R2 中，默认值是“固定大小”。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image16.png)
18. 在“指定名称和位置”页上，提供数据磁盘的“名称”和“位置”（可以通过浏览选择一个）。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image17.png)
19. 在“配置磁盘”页上，选择“新建空白虚拟硬盘”选项，将大小指定为“500 GB”（或&500; GB 以上）。 单击“下一步”。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image18.png)
20. 在“摘要”页上查看虚拟数据磁盘的详细信息，如果一切符合要求，则请单击“完成”创建该磁盘。 此时会关闭向导并向虚拟机添加虚拟硬盘。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image19.png)
21. 返回到“设置”页。 单击“确定”关闭“设置”页，返回到“Hyper-V 管理器”窗口。

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image20.png)

## <a name="step-3-start-the-virtual-array-and-get-the-ip"></a>步骤 3：启动虚拟阵列并获取 IP
执行以下步骤，启动虚拟阵列并与其进行连接。

#### <a name="to-start-the-virtual-array"></a>启动虚拟阵列
1. 启动虚拟阵列。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image21.png)
2. 设备运行后，选择该设备，右键单击后选择“连接”。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image22.png)
3. 可能需要等待 5-10 分钟设备才能准备就绪。 控制台会显示指示进度的状态消息。 设备就绪后，转到“操作”。 按 `Ctrl + Alt + Delete` 登录到虚拟阵列。 默认用户为 *StorSimpleAdmin*，默认密码为 *Password1*。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image23.png)
4. 出于安全原因，设备管理员密码在第一次登录后过期。 系统会提示用户更改密码。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image24.png)

   请输入至少包含 8 个字符的密码。 密码必须满足以下 4 个要求中的至少 3 个：大写、小写、数字、特殊字符。 再次输入密码进行确认。 用户会收到密码已更改的通知。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image25.png)
5. 成功更改密码后，虚拟阵列会重新启动。 等待设备启动。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image26.png)

    将显示设备的 Windows PowerShell 控制台和进度栏。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image27.png)
6. 步骤 6-8 仅适用于在非 DHCP 环境中启动的情况。 如果是在 DHCP 环境中，则请跳过这些步骤，转到步骤 9。 如果已在非 DHCP 环境中启动设备，则会看到以下屏幕。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image28m.png)

    接下来，配置网络。
7. 使用 `Get-HcsIpAddress` 命令列出在虚拟阵列上启用的网络接口。 如果设备启用了单个网络接口，则分配到该接口的默认名称为 `Ethernet`。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image29m.png)
8. 使用 `Set-HcsIpAddress` cmdlet 配置网络。 请参阅以下示例：

    `Set-HcsIpAddress –Name Ethernet –IpAddress 10.161.22.90 –Netmask 255.255.255.0 –Gateway 10.161.22.1`

    ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image30.png)
9. 完成初始设置并启动设备以后，将会显示设备横幅文本。 记下显示在横幅文本中的 IP 地址和 URL，以便管理设备。 使用该 IP 地址连接到虚拟阵列的 Web UI 并完成本地设置和注册。

   ![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image31m.png)
10. （可选）仅当你是在政府云中部署设备时，才执行此步骤。 此时会在设备上启用美国联邦信息处理标准 (FIPS) 模式。 FIPS 140 标准定义的加密算法已经过批准，可以用于美国联邦政府计算机系统来保护敏感数据。

    1. 若要启用 FIPS 模式，请运行以下 cmdlet：

        `Enable-HcsFIPSMode`
    2. 启用 FIPS 模式后请重新启动设备，以便加密验证生效。

       > [!NOTE]
       > 可以在设备上启用或禁用 FIPS 模式。 不支持在设备上交替使用 FIPS 模式和非 FIPS 模式。
       >
       >

如果设备不符合最低配置要求，则会在横幅文本中显示以下错误（如下所示）。 修改设备配置，使虚拟机有足够的资源来满足最低要求。 然后即可重新启动设备并与之进行连接。 请参阅[步骤 1：确保主机系统满足最低虚拟阵列要求](#step-1-ensure-that-the-host-system-meets-minimum-virtual-device-requirements)中的最低配置要求。

![](./media/storsimple-virtual-array-deploy2-provision-hyperv/image32.png)

若在使用本地 Web UI 进行初始配置期间遇到其他错误，请参阅以下工作流：

* 运行诊断测试，[排除 Web UI 设置故障](storsimple-ova-web-ui-admin.md#troubleshoot-web-ui-setup-errors)。
* [生成日志包并查看日志文件](storsimple-ova-web-ui-admin.md#generate-a-log-package)。

## <a name="next-steps"></a>后续步骤
* [将 StorSimple 虚拟阵列设置为文件服务器](storsimple-virtual-array-deploy3-fs-setup.md)
* [将 StorSimple 虚拟阵列设置为 iSCSI 服务器](storsimple-virtual-array-deploy3-iscsi-setup.md)

