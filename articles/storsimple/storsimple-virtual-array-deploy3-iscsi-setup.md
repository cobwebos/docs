---
title: Microsoft Azure StorSimple 虚拟阵列 iSCSI 服务器设置 | Microsoft 文档
description: 介绍如何进行初始设置、如何注册 StorSimple iSCSI 服务器，以及如何完成设备设置。
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 4db116d1-978b-48e8-b572-a719a8425dbc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.openlocfilehash: 076df176d7cd40c009aea27004fe0f4415999c80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
ms.locfileid: "23112275"
---
# <a name="deploy-storsimple-virtual-array--set-up-as-an-iscsi-server-via-azure-portal"></a>部署 StorSimple 虚拟阵列 - 通过 Azure 门户设置为 iSCSI 服务器

![iscsi 设置流程](./media/storsimple-virtual-array-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>概述

本部署教程适用于 Microsoft Azure StorSimple 虚拟阵列。 本教程介绍如何进行初始安装、注册 StorSimple iSCSI 服务器、完成设备设置，并创建、装载、初始化和格式化配置为 iSCSI 服务器的 StorSimple 虚拟阵列上的卷。 

完成此处所述过程大约需要 30 分钟到 1 小时。 本文发布的信息仅适用于 StorSimple 虚拟阵列。

## <a name="setup-prerequisites"></a>设置先决条件

配置和设置 StorSimple 虚拟阵列之前，请确保：

* 已根据[部署 StorSimple 虚拟阵列 - 在 Hyper-V 中预配虚拟阵列](storsimple-ova-deploy2-provision-hyperv.md)或[部署 StorSimple 虚拟阵列 - 在 VMware 中预配虚拟阵列](storsimple-virtual-array-deploy2-provision-vmware.md)中的说明预配和连接虚拟阵列。
* 已获得所创建的 StorSimple Device Manager 服务提供的服务注册密钥，该服务用于管理 StorSimple 虚拟阵列。 有关详细信息，请参阅[部署 StorSimple 虚拟阵列 - 准备门户](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)中的“步骤 2：获取服务注册密钥”。
* 如果向现有 StorSimple Device Manager 服务注册过虚拟阵列，用户应该已经有了服务数据加密密钥。 该密钥是在成功地通过此服务注册第一台设备时生成的。 如果该密钥已丢失，请参阅[使用 Web UI 管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)中的**获取服务数据加密密钥**。

## <a name="step-by-step-setup"></a>分步设置

使用以下分步说明设置和配置 StorSimple 虚拟阵列：

* [步骤 1：完成本地 Web UI 设置并注册设备](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [步骤 2：完成所需的设备设置](#step-2-complete-the-required-device-setup)
* [步骤 3：添加卷](#step-3-add-a-volume)
* [步骤 4：装载、初始化和格式化卷](#step-4-mount-initialize-and-format-a-volume)

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>步骤 1：完成本地 Web UI 设置并注册设备

#### <a name="to-complete-the-setup-and-register-the-device"></a>完成设备设置和注册

1. 打开一个浏览器窗口。 若要连接到 Web UI，请键入：
   
    `https://<ip-address of network interface>`
   
    使用在上一步中记下的连接 URL。 此时会出现一个错误，告知用户网站的安全证书有问题。 单击“继续访问此网页”。
   
    ![安全证书错误](./media/storsimple-virtual-array-deploy3-iscsi-setup/image3.png)
2. 以 **StorSimpleAdmin** 身份登录到虚拟设备的 Web UI。 输入在“步骤 3：启动虚拟设备”中更改的设备管理员密码，详见[部署 StorSimple 虚拟阵列 - 在 Hyper-V 中预配虚拟设备](storsimple-virtual-array-deploy2-provision-hyperv.md)或[部署 StorSimple 虚拟阵列 - 在 VMware 中预配虚拟设备](storsimple-virtual-array-deploy2-provision-vmware.md)。
   
    ![登录页](./media/storsimple-virtual-array-deploy3-iscsi-setup/image4.png)
3. 此时会转到“主页”。 该页介绍配置虚拟设备并将其注册到 StorSimple Device Manager 服务所需的各种设置。 请注意，“网络设置”、“Web 代理设置”和“时间设置”为可选设置。 仅“设备设置”和“云设置”为必需设置。
   
    ![主页](./media/storsimple-virtual-array-deploy3-iscsi-setup/image5.png)
4. 在“网络接口”下的“网络设置”页上，系统会自动为用户配置 DATA 0。 每个网络接口均默认设置为自动获取 IP 地址 (DHCP)。 因此，系统会自动分配 IP 地址、子网和网关（适用于 IPv4 和 IPv6）。
   
    由于计划将设备部署为 iSCSI 服务器（目的是预配块存储），因此建议禁用“自动获取 IP 地址”选项并配置静态 IP 地址。
   
    ![“网络设置”页](./media/storsimple-virtual-array-deploy3-iscsi-setup/image6.png)
   
    如果在预配设备的过程中添加了多个网络接口，可在此处配置这些接口。 注意，可将网络接口配置为仅限 IPv4，或者同时支持 IPv4 和 IPv6。 不支持仅限 IPv6 的配置。
5. DNS 服务器是必需的，因为在设备尝试与云存储服务提供程序通信时，需要用到这些服务器；在按名称解析设备时，也需要用到这些配置为文件服务器的服务器。 在“DNS 服务器”下的“网络设置”页上：
   
   1. 将自动配置主 DNS 服务器和辅助 DNS 服务器。 如果选择配置静态 IP 地址，则可指定 DNS 服务器。 为了确保高可用性，建议同时配置主 DNS 服务器和辅助 DNS 服务器。
   2. 单击“应用” 。 此时会应用和验证网络设置。
6. 在“设备设置”页上：
   
   1. 为设备指定唯一“名称”。 该名称长度为 1-15 个字符，并可包含字母、数字和连字符。
   2. 选择要创建的设备的“类型”时，请单击“iSCSI 服务器”图标 ![iSCSI 服务器图标](./media/storsimple-virtual-array-deploy3-iscsi-setup/image7.png)。 iSCSI 服务器会允许用户预配块存储。
   3. 指定是否希望此设备加入域。 如果设备是 iSCSI 服务器，则可选择加入域。 如果决定不将 iSCSI 服务器加入域，请单击“应用”，等待系统应用设置，然后跳到下一步。
      
       要将设备加入域， 请输入“域名”，并单击“应用”。
      
      > [!NOTE]
      > 如果将 iSCSI 服务器加入域，请确保虚拟阵列采用其自身的适用于 Microsoft Azure Active Directory 的组织单位 (OU)，没有对其应用任何组策略对象 (GPO)。
      > 
      > 
   4. 此时会显示一个对话框。 按指定格式输入域凭据。 单击选中图标  ![选中图标](./media/storsimple-virtual-array-deploy3-iscsi-setup/image15.png)。 此时会验证域凭据。 如果凭据不正确，则会显示错误消息。
      
       ![凭据](./media/storsimple-virtual-array-deploy3-iscsi-setup/image8.png)
   5. 单击“应用” 。 此时会应用和验证设备设置。
7. （可选）配置 Web 代理服务器。 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。
   
    ![配置 Web 代理](./media/storsimple-virtual-array-deploy3-iscsi-setup/image9.png)
   
    在“Web 代理”页上：
   
   1. 以下述格式提供“Web 代理 URL”：*http://主机 IP 地址*或 *FDQN:端口号*。 请注意，不支持 HTTPS URL。
   2. 将“身份验证”指定为“基本”或“无”。
   3. 如果使用身份验证，则还需提供“用户名”和“密码”。
   4. 单击“应用” 。 此时会验证并应用配置的 Web 代理设置。
8. （可选）配置设备的时间设置，例如时区以及主 NTP 服务器和辅助 NTP 服务器。 NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
   
    ![时间设置](./media/storsimple-virtual-array-deploy3-iscsi-setup/image10.png)
   
    在“时间设置”页上：
   
   1. 根据部署设备的地理位置，从下拉列表中选择相应的“时区” 。 设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。
   2. 为设备指定“主 NTP 服务器”，或者接受默认值：time.windows.com。确保网络允许 NTP 流量从数据中心传递到 Internet。
   3. （可选）为设备指定“辅助 NTP 服务器”。
   4. 单击“应用” 。 此时会验证并应用配置的时间设置。
9. 配置设备的云设置。 此步骤需完成本地设备配置，然后将设备注册到 StorSimple Device Manager 服务。
   
   1. 输入在[部署 StorSimple 虚拟阵列 - 准备门户](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)的“步骤 2：获取服务注册密钥”中获得的“服务注册密钥”。
   2. 如果这不是注册到此服务的第一个设备，则需提供“服务数据加密密钥”。 向 StorSimple Device Manager 服务注册其他设备时，需要此密钥以及该服务注册密钥。 有关详细信息，请参阅本地 Web UI 上的[获取服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。
   3. 单击“注册”。 此时会重新启动设备。 可能需要等待 2-3 分钟才能成功注册设备。 重新启动设备后，会转到登录页。
      
      ![注册设备](./media/storsimple-virtual-array-deploy3-iscsi-setup/image11.png)
10. 返回到 Azure 门户。
11. 导航到服务的“设备”边栏选项卡。 如果有大量资源，请单击“所有资源”，单击服务名称（必要时可搜索），然后单击“设备”。
12. 在“设备”边栏选项卡上，通过查看状态验证该设备是否已成功连接到该服务。 设备状态应为“已准备好设置”。
    
    ![注册设备](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png)

## <a name="step-2-configure-the-device-as-iscsi-server"></a>步骤 2：将设备配置为 iSCSI 服务器

在 Azure 门户中执行以下步骤，完成所需的设备设置。

#### <a name="to-configure-the-device-as-iscsi-server"></a>将设备配置为 iSCSI 服务器

1. 转到 StorSimple Device Manager 服务，并转到“管理”>“设备”。 在“设备”边栏选项卡中，选择刚创建的设备。 此设备会显示为“已准备好设置”。
   
    ![将设备配置为 iSCSI 服务器](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis1m.png) 
2. 单击设备，会显示横幅消息，指示该设备已准备好设置。
   
    ![将设备配置为 iSCSI 服务器](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis2m.png)  
3. 在设备命令栏中，单击“配置”。 此时会打开“配置”边栏选项卡。 在“配置”边栏选项卡中，执行以下操作：
   
   * 将自动填充 iSCSI 服务器名称。
   * 确保云存储加密已设置为“启用”。 这可确保从设备发送到云的数据已加密。
   * 指定 32 个字符的加密密钥并将其记录在密钥管理应用中以供将来参考。
   * 选择要用于设备的存储帐户。 在此订阅中，可以选择现有存储帐户，也可以单击“添加”从其他订阅中选择一个帐户。
     
     ![将设备配置为 iSCSI 服务器](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis4m.png)
4. 单击“配置”完成 iSCSI 服务器的设置。
   
    ![将设备配置为 iSCSI 服务器](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis5m.png) 
5. 将通知你“正在创建 iSCSI 服务器”。 成功创建 iSCSI 服务器后，“设备”边栏选项卡将更新，并且相应的设备状态为“联机”。
   
    ![将设备配置为 iSCSI 服务器](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis9m.png)

## <a name="step-3-add-a-volume"></a>步骤 3：添加卷

1. 在“设备”边栏选项卡中，选择刚配置为 iSCSI 服务器的设备。 单击“...”（或者在此行中右键单击），并从上下文菜单中选择“添加卷”。 也可以在命令栏中单击“+ 添加卷”。 此时会打开“添加卷”边栏选项卡。
   
    ![添加卷](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis10m.png)
2. 在“添加卷”边栏选项卡中，执行以下操作：
   
   * 在“卷名”字段中，输入卷的唯一名称。 该名称必须是包含 3 到 127 个字符的字符串。
   * 在“类型”下拉列表中，指定要创建**分层**卷还是要创建**本地固定**卷。 对于需要本地保证、低延迟和高性能的工作负荷，请选择“本地固定卷”。 对于所有其他数据，请选择“分层卷”。
   * 在“容量”字段中，指定卷的大小。 分层卷的大小必须介于 500 GB 到 5 TB 之间，本地固定卷的大小必须介于 50 GB 到 500 GB 之间。
     
     本地固定卷经过充分预配，可确保卷中的主数据保留在设备上，未溢出到云。
     
     分层卷则是进行精简预配。 创建分层卷时，大约 10% 的空间预配在本地层，90% 的空间预配在云中。 例如，如果预配 1 TB 的卷，则当数据分层时，100 GB 会驻留在本地空间，900 GB 会在云中使用。 反过来说，这意味着，如果用光了设备上的所有本地空间，则无法预配分层共享（因为该 10% 的空间不可用）。
     
     ![添加卷](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis12.png)
   * 单击“已连接主机”，选择要连接到此卷的 iSCSI 发起程序所对应的访问控制记录 (ACR)，并单击“选择”。 <br><br> 
3. 要添加新连接的主机，请单击“新增”，输入主机的名称及其 iSCSI 限定名称 (IQN)，并单击“添加”。 如果没有 IQN，请转到[附录 A：获取 Windows Server 主机的 IQN](#appendix-a-get-the-iqn-of-a-windows-server-host)。
   
      ![添加卷](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis15m.png)
4. 完成配置卷后，单击“确定”。 此时会使用指定的设置创建卷，会看到一条通知。 默认情况下，将为卷启用监视和备份功能。
   
     ![添加卷](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis18m.png)
5. 若要确认已成功创建卷，请转到“卷”边栏选项卡。 此时会看到列出的卷。
   
   ![添加卷](./media/storsimple-virtual-array-deploy3-iscsi-setup/deployis20m.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>步骤 4：装载、初始化和格式化卷

执行以下步骤，装载、初始化和格式化 Windows Server 主机上的 StorSimple 卷。

#### <a name="to-mount-initialize-and-format-a-volume"></a>装载、初始化和格式化卷

1. 打开相应服务器上的“iSCSI 发起程序”应用。
2. 在“iSCSI 发起程序属性”窗口中的“发现”选项卡上，单击“发现门户”。
   
    ![发现门户](./media/storsimple-virtual-array-deploy3-iscsi-setup/image22.png)
3. 在“发现目标门户”对话框中，提供支持 iSCSI 网络接口的 IP 地址，并单击“确定”。
   
    ![IP 地址](./media/storsimple-virtual-array-deploy3-iscsi-setup/image23.png)
4. 在“iSCSI 发起程序属性”窗口中的“目标”选项卡上，找到“已发现目标”。 （每个卷都可以是已发现的目标。）设备状态应显示为 **非活动**。
   
    ![已发现的目标](./media/storsimple-virtual-array-deploy3-iscsi-setup/image24.png)
5. 选择目标设备，并单击“连接”。 设备连接后，状态应更改为 **已连接**。 （有关使用 Microsoft iSCSI 发起程序的详细信息，请参阅[安装和配置 Microsoft iSCSI 发起程序][1]。
   
    ![选择目标设备](./media/storsimple-virtual-array-deploy3-iscsi-setup/image25.png)
6. 在 Windows 主机上，按 Windows 徽标键 + X，并单击“ **运行**”。
7. 在“运行”对话框中，键入 **Diskmgmt.msc**。 单击“确定”，将出现“磁盘管理”对话框。 右窗格中会显示在主机上的卷。
8. 在“ **磁盘管理** ”窗口中，已装载的卷会显示在以下所示的插图中。 右键单击“发现的卷”（单击磁盘名称），并单击“ **联机**”。
   
    ![磁盘管理](./media/storsimple-virtual-array-deploy3-iscsi-setup/image26.png)
9. 右键单击，并选择“初始化磁盘”。
   
    ![初始化磁盘 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image27.png)
10. 在对话框中选择要初始化的磁盘，并单击“确定”。
    
    ![初始化磁盘 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image28.png)
11. 此时会启动新建简单卷向导。 选择磁盘大小，并单击“下一步”。
    
    ![新建卷向导 1](./media/storsimple-virtual-array-deploy3-iscsi-setup/image29.png)
12. 为卷分配驱动器号，并单击“下一步”。
    
    ![新建卷向导 2](./media/storsimple-virtual-array-deploy3-iscsi-setup/image30.png)
13. 输入对卷进行格式化的参数。 **Windows Server 仅支持 NTFS。** 将分配单元大小设置为 64 K。 为卷提供一个标签。 根据最佳做法，建议将此名称设置为在 StorSimple 虚拟阵列上提供的卷名。 单击“下一步”。
    
    ![新建卷向导 3](./media/storsimple-virtual-array-deploy3-iscsi-setup/image31.png)
14. 查看卷的值，并单击“完成”。
    
    ![新建卷向导 4](./media/storsimple-virtual-array-deploy3-iscsi-setup/image32.png)
    
    这些卷会在“磁盘管理”页上显示为“联机”。
    
    ![联机卷](./media/storsimple-virtual-array-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>后续步骤

了解如何使用本地 Web UI [管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md)。

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>附录 A：获取 Windows Server 主机的 IQN

执行以下步骤，获取正在运行 Windows Server 2012 的 Windows 主机的 iSCSI 限定名称 (IQN)。

#### <a name="to-get-the-iqn-of-a-windows-host"></a>获取 Windows 主机的 IQN

1. 在 Windows 主机上启动 Microsoft iSCSI 发起程序。
2. 在“iSCSI 发起程序属性”窗口中的“配置”选项卡上，选择并复制“发起程序名称”字段中的字符串。
   
    ![iSCSI 发起程序属性](./media/storsimple-virtual-array-deploy3-iscsi-setup/image34.png)
3. 保存此字符串。

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx



