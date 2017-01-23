---
title: "StorSimple 虚拟阵列 iSCSI 服务器设置 | Microsoft Docs"
description: "介绍如何进行初始设置、如何注册 StorSimple iSCSI 服务器，以及如何完成设备设置。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 1a854bb3-3068-4db9-87b7-9f3692ab64e4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 715720d22b58ddd3d0e5042de151219e49549c5e


---
# <a name="deploy-storsimple-virtual-array--set-up-your-virtual-device-as-an-iscsi-server"></a>部署 StorSimple 虚拟阵列 - 将虚拟设备设置为 iSCSI 服务器
![iscsi 设置流程](./media/storsimple-ova-deploy3-iscsi-setup/iscsi4.png)

## <a name="overview"></a>概述
本部署教程适用于运行 2016 年 3 月公开发行 (GA) 版的 Microsoft Azure StorSimple 虚拟阵列（也称 StorSimple 本地虚拟设备或 StorSimple 虚拟设备）。 本教程介绍如何进行初始安装、注册 StorSimple iSCSI 服务器、完成设备设置，然后创建、装载、初始化和格式化 StorSimple 虚拟设备 iSCSI 服务器上的卷。 本文中的 StorSimple 设置信息仅适用于 StorSimple 虚拟阵列。 

完成此处所述过程大约需要 30 分钟到 1 小时。 本文发布的信息仅适用于 StorSimple 虚拟阵列。

## <a name="setup-prerequisites"></a>设置先决条件
配置和设置 StorSimple 虚拟设备之前，请确保：

* 已根据[部署 StorSimple 虚拟阵列 - 在 Hyper-V 中预配虚拟阵列](storsimple-ova-deploy2-provision-hyperv.md)或[部署 StorSimple 虚拟阵列 - 在 VMware 中预配虚拟阵列](storsimple-ova-deploy2-provision-vmware.md)中的说明预配和连接虚拟设备。
* 已获得所创建的 StorSimple Manager 服务提供的服务注册密钥，该服务用于管理 StorSimple 虚拟设备。 有关详细信息，请参阅[部署 StorSimple 虚拟阵列 - 准备门户](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)中的“步骤 2：获取服务注册密钥”。
* 如果通过现有 StorSimple Manager 服务注册过虚拟设备，用户应该已经有了服务数据加密密钥。 该密钥是在成功地通过此服务注册第一台设备时生成的。 如果该密钥已丢失，请参阅[使用 Web UI 管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)中的**获取服务数据加密密钥**。

## <a name="step-by-step-setup"></a>分步设置
若要设置和配置 StorSimple 虚拟设备，请按下述分步说明操作：

* [步骤 1：完成本地 Web UI 设置并注册设备](#step-1-complete-the-local-web-ui-setup-and-register-your-device)
* [步骤 2：完成所需的设备设置](#step-2-complete-the-required-device-setup)
* [步骤 3：添加卷](#step-3-add-a-volume)
* [步骤 4：装载、初始化和格式化卷](#step-4-mount-initialize-and-format-a-volume)  

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>步骤 1：完成本地 Web UI 设置并注册设备
#### <a name="to-complete-the-setup-and-register-the-device"></a>完成设备设置和注册
1. 打开浏览器窗口，通过键入以下内容连接到 Web UI：
   
    `https://<ip-address of network interface>`
   
    使用在上一步中记下的连接 URL。 此时会出现一个错误，告知用户网站的安全证书有问题。 单击“继续访问此网页”。
   
    ![安全证书错误](./media/storsimple-ova-deploy3-iscsi-setup/image3.png)
2. 以 **StorSimpleAdmin** 身份登录到虚拟设备的 Web UI。 输入在“步骤 3：启动虚拟设备”中更改的设备管理员密码，详见[部署 StorSimple 虚拟阵列 - 在 Hyper-V 中预配虚拟设备](storsimple-ova-deploy2-provision-hyperv.md)或[部署 StorSimple 虚拟阵列 - 在 VMware 中预配虚拟设备](storsimple-ova-deploy2-provision-vmware.md)。
   
    ![登录页](./media/storsimple-ova-deploy3-iscsi-setup/image4.png)
3. 此时会转到“主页”。 该页介绍配置虚拟设备并将其注册到 StorSimple Manager 服务所需的各种设置。 请注意，“网络设置”、“Web 代理设置”和“时间设置”为可选设置。 仅“设备设置”和“云设置”为必需设置。
   
    ![主页](./media/storsimple-ova-deploy3-iscsi-setup/image5.png)
4. 在“网络接口”下的“网络设置”页上，系统会自动为用户配置 DATA 0。 每个网络接口均默认设置为自动获取 IP 地址 (DHCP)。 因此，系统会自动分配 IP 地址、子网和网关（适用于 IPv4 和 IPv6）。
   
    由于你计划将设备部署为 iSCSI 服务器（目的是预配块存储），因此建议禁用“自动获取 IP 地址”选项并配置静态 IP 地址。
   
    ![“网络设置”页](./media/storsimple-ova-deploy3-iscsi-setup/image6.png)
   
    如果在预配设备的过程中添加了多个网络接口，可在此处配置这些接口。 注意，可将网络接口配置为仅限 IPv4，或者同时支持 IPv4 和 IPv6。 不支持仅限 IPv6 的配置。
5. DNS 服务器是必需的，因为在设备尝试与云存储服务提供程序通信时，需要用到这些服务器；在按名称解析设备时，也需要用到这些配置为文件服务器的服务器。 在“DNS 服务器”下的“网络设置”页上：
   
   1. 将自动配置主 DNS 服务器和辅助 DNS 服务器。 如果选择配置静态 IP 地址，则可指定 DNS 服务器。 为了确保高可用性，建议同时配置主 DNS 服务器和辅助 DNS 服务器。
   2. 单击“应用” 。 此时会应用和验证网络设置。
6. 在“设备设置”页上：
   
   1. 为设备指定唯一“名称”。 该名称长度为 1-15 个字符，并可包含字母、数字和连字符。
   2. 选择要创建的设备的“类型”时，请单击“iSCSI 服务器”图标 ![iSCSI 服务器图标](./media/storsimple-ova-deploy3-iscsi-setup/image7.png)。 iSCSI 服务器会允许用户预配块存储。
   3. 指定是否希望此设备加入域。 如果设备是 iSCSI 服务器，则可选择加入域。 如果决定不将 iSCSI 服务器加入域，请单击“应用”，等待系统应用设置，然后跳到下一步。
      
       若要将设备加入域， 请输入“域名”，然后单击“应用”。
      
      > [!NOTE]
      > 如果将 iSCSI 服务器加入域，请确保虚拟阵列采用其自身的适用于 Microsoft Azure Active Directory 的组织单位 (OU)，没有对其应用任何组策略对象 (GPO)。
      > 
      > 
   4. 此时会显示一个对话框。 按指定格式输入域凭据。 单击选中图标  ![选中图标](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)。 此时会验证域凭据。 如果凭据不正确，则会显示错误消息。
      
       ![凭据](./media/storsimple-ova-deploy3-iscsi-setup/image8.png)
   5. 单击“应用” 。 此时会应用和验证设备设置。
7. （可选）配置 Web 代理服务器。 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。
   
    ![配置 Web 代理](./media/storsimple-ova-deploy3-iscsi-setup/image9.png)
   
    在“Web 代理”页上：
   
   1. 以下述格式提供“Web 代理 URL”：*http://主机 IP 地址*或 *FDQN:端口号*。 请注意，不支持 HTTPS URL。
   2. 将“身份验证”指定为“基本”或“无”。
   3. 如果使用身份验证，则还需提供“用户名”和“密码”。
   4. 单击“应用” 。 此时会验证并应用配置的 Web 代理设置。
8. （可选）配置设备的时间设置，例如时区以及主 NTP 服务器和辅助 NTP 服务器。 NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
   
    ![时间设置](./media/storsimple-ova-deploy3-iscsi-setup/image10.png)
   
    在“时间设置”页上：
   
   1. 根据部署设备的地理位置，从下拉列表中选择相应的“时区” 。 设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。
   2. 为设备指定“主 NTP 服务器”，或者接受默认值：time.windows.com。 确保网络允许 NTP 流量从数据中心传递到 Internet。
   3. （可选）为设备指定“辅助 NTP 服务器”。
   4. 单击“应用” 。 此时会验证并应用配置的时间设置。
9. 配置设备的云设置。 此步骤需完成本地设备配置，然后将设备注册到 StorSimple Manager 服务。
   
   1. 输入在[部署 StorSimple 虚拟阵列 - 准备门户](storsimple-ova-deploy1-portal-prep.md#step-2-get-the-service-registration-key)的“步骤 2：获取服务注册密钥”中获得的“服务注册密钥”。
   2. 如果这不是注册到此服务的第一个设备，则需提供“服务数据加密密钥”。 向 StorSimple Manager 服务注册其他设备时，需要此密钥以及该服务注册密钥。 有关详细信息，请参阅本地 Web UI 上的[获取服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。
   3. 单击“注册”。 此时会重新启动设备。 可能需要等待 2-3 分钟才能成功注册设备。 重新启动设备后，会转到登录页。
      
      ![注册设备](./media/storsimple-ova-deploy3-iscsi-setup/image11.png)
10. 回到 Azure 经典门户。 在“ **设备** ”页上，通过查看状态验证该设备是否已成功连接到该服务。 设备状态应为“活动”。
    
    ![“设备”页](./media/storsimple-ova-deploy3-iscsi-setup/image12.png)

## <a name="step-2-complete-the-required-device-setup"></a>步骤 2：完成所需的设备设置
若要完成 StorSimple 设备的设备配置，需执行以下操作：

* 选择与设备关联的存储帐户。
* 为发送到云的数据选择加密设置。

在 Azure 经典门户中执行以下步骤，完成所需的设备设置。

#### <a name="to-complete-the-minimum-device-setup"></a>完成最低要求的设备设置
1. 在“设备”页上，选择刚创建的设备。 该设备将显示为“活动”。 单击设备名称旁边的箭头，然后单击“快速启动”。
   
    ![“设备”页](./media/storsimple-ova-deploy3-iscsi-setup/image13.png)
2. 单击“完成设备设置”启动“配置设备向导”。
   
    ![配置设备向导](./media/storsimple-ova-deploy3-iscsi-setup/image14.png)
3. 在配置设备向导的“基本设置”页中，执行以下操作：
   
   1. 指定用于设备的存储帐户。 在此订阅中，可以从下拉列表中选择现有存储帐户，也可以指定“添加更多帐户”，从其他订阅中选择一个帐户。
   2. 为所有将要发送到云的静态数据定义加密设置。 （StorSimple 使用 AES-256 加密。）若要加密数据，请选中“启用云存储加密”复选框。 输入包含 32 个字符的云存储加密密钥。 重新输入密钥进行确认。
   3. 单击选中图标  ![选中图标](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)。
      
      ![基本设置](./media/storsimple-ova-deploy3-iscsi-setup/image16.png)
      
      此时会更新设置。 成功更新设置后，“完成设备设置”按钮将不可用。 将返回到设备“ **快速启动** ”页。                                                        

> [!NOTE]
> 通过访问“ **配置** ”页可随时修改所有其他设备设置。
> 
> 

## <a name="step-3-add-a-volume"></a>步骤 3：添加卷
请在 Azure 经典门户中执行以下步骤，创建卷。

#### <a name="to-create-a-volume"></a>创建卷
1. 在设备“快速启动”页上，单击“添加卷”。 这将启动“添加卷向导”。
2. 在“添加卷向导”中的“基本设置” 下，执行以下操作：
   
   1. 为卷提供唯一名称。 该名称必须是包含 3 到 127 个字符的字符串。
   2. 提供对卷的说明。 可以根据说明确定卷所有者。
   3. 选择卷的使用类型。 使用类型可以是“分层卷”或“本地固定卷”。 （“分层卷”为默认设置。）对于需要本地保证、低延迟和高性能的工作负荷，请选择“本地固定卷”。 对于所有其他数据，请选择“分层卷”。
      
       本地固定卷经过充分预配，可确保卷中的主数据保留在设备上，未溢出到云。 如果创建一个本地固定卷，设备会检查本地层上的可用空间，预配所请求大小的卷。 创建本地固定卷可能需要让设备中的现有数据溢出到云，因此创建该卷所花的时间可能会很长。 总时间取决于预配卷的大小、可用的网络带宽以及设备中的数据。
      
       分层卷则是进行精简预配，可以很快速地创建。 创建分层卷时，大约 10% 的空间预配在本地层，90% 的空间预配在云中。 例如，如果预配 1 TB 的卷，则当数据分层时，100 GB 会驻留在本地空间，900 GB 会在云中使用。 反过来说，这意味着，如果用光了设备上的所有本地空间，则无法预配分层共享（因为该 10% 的空间不可用）。
   4. 指定卷的预配容量。 请注意，指定的容量应小于可用容量。 如果创建分层卷，其大小应介于 500 GB 到 5 TB 之间。 对于本地固定卷，可将卷大小指定为 50 GB 到 500 GB 之间。 可以根据可用容量预配卷。 如果可用本地容量为 0 GB，则不能预配本地固定卷或分层卷。
      
       ![基本设置](./media/storsimple-ova-deploy3-iscsi-setup/image17.png)
   5. 单击箭头图标  ![箭头图标](./media/storsimple-ova-deploy3-iscsi-setup/image18.png) 转到下一页。
3. 在“其他设置”页上，添加新的访问控制记录 (ACR)：
   
   1. 为你的 ACR 提供“名称”  。
   2. 在“iSCSI 发起程序名称” 下，提供 Windows 主机的 iSCSI 限定名称 (IQN)。 如果没有 IQN，请转到[附录 A：获取 Windows Server 主机的 IQN](#appendix-a-get-the-iqn-of-a-windows-server-host)。
   3. 我们建议通过选中“为此卷启用默认备份”  复选框启用默认备份。 默认备份将创建于每天 22:30（设备时间）执行的策略，并创建此卷的云快照。
      
       ![其他设置](./media/storsimple-ova-deploy3-iscsi-setup/image19.png)
   4. 单击选中图标  ![选中图标](./media/storsimple-ova-deploy3-iscsi-setup/image15.png)。 此时会启动卷创建作业。 此时会显示如下所示的进度消息。
      
       ![进度消息](./media/storsimple-ova-deploy3-iscsi-setup/image20.png)
      
       使用指定设置创建卷。 默认情况下，将为卷启用监视和备份功能。
   5. 若要确认已成功创建卷，请转到“卷”页。 此时会看到列出的卷。
      
       ![](./media/storsimple-ova-deploy3-iscsi-setup/image21.png)

## <a name="step-4-mount-initialize-and-format-a-volume"></a>步骤 4：装载、初始化和格式化卷
执行以下步骤，装载、初始化和格式化 Windows Server 主机上的 StorSimple 卷。

#### <a name="to-mount-initialize-and-format-a-volume"></a>装载、初始化和格式化卷
1. 启动 Microsoft iSCSI 发起程序。
2. 在“iSCSI 发起程序属性”窗口中的“发现”选项卡上，单击“发现门户”。
   
    ![发现门户](./media/storsimple-ova-deploy3-iscsi-setup/image22.png)
3. 在“发现目标门户”对话框中，提供支持 iSCSI 网络接口的 IP 地址，然后单击“确定”。
   
    ![IP 地址](./media/storsimple-ova-deploy3-iscsi-setup/image23.png)
4. 在“iSCSI 发起程序属性”窗口中的“目标”选项卡上，找到“已发现目标”。 （每个卷都可以是已发现的目标。）设备状态应显示为 **非活动**。
   
    ![已发现的目标](./media/storsimple-ova-deploy3-iscsi-setup/image24.png)
5. 选择目标设备，然后单击“连接”。 设备连接后，状态应更改为 **已连接**。 （有关使用 Microsoft iSCSI 发起程序的详细信息，请参阅[安装和配置 Microsoft iSCSI 发起程序][1]。
   
    ![选择目标设备](./media/storsimple-ova-deploy3-iscsi-setup/image25.png)
6. 在 Windows 主机上，按 Windows 徽标键 + X，然后单击“ **运行**”。
7. 在“运行”对话框中，键入 **Diskmgmt.msc**。 单击“确定”，将出现“磁盘管理”对话框。 右窗格中将显示在主机上的卷。
8. 在“ **磁盘管理** ”窗口中，已装载的卷将显示在以下所示的插图中。 右键单击“发现的卷”（单击磁盘名称），然后单击“ **联机**”。
   
    ![磁盘管理](./media/storsimple-ova-deploy3-iscsi-setup/image26.png)
9. 右键单击，然后选择“初始化磁盘”。
   
    ![初始化磁盘 1](./media/storsimple-ova-deploy3-iscsi-setup/image27.png)
10. 在对话框中选择要初始化的磁盘，然后单击“确定”。
    
    ![初始化磁盘 2](./media/storsimple-ova-deploy3-iscsi-setup/image28.png)
11. 此时会启动新建简单卷向导。 选择磁盘大小，然后单击“下一步”。
    
    ![新建卷向导 1](./media/storsimple-ova-deploy3-iscsi-setup/image29.png)
12. 为卷分配驱动器号，然后单击“下一步”。
    
    ![新建卷向导 2](./media/storsimple-ova-deploy3-iscsi-setup/image30.png)
13. 输入对卷进行格式化的参数。 **Windows Server 仅支持 NTFS。** 将 AUS 设置为 64K。 为卷提供一个标签。 根据最佳做法，建议将此名称设置为在 StorSimple 虚拟设备上提供的卷名称。 单击“资源组名称” 的 Azure 数据工厂。
    
    ![新建卷向导 3](./media/storsimple-ova-deploy3-iscsi-setup/image31.png)
14. 查看卷的值，然后单击“完成”。
    
    ![新建卷向导 4](./media/storsimple-ova-deploy3-iscsi-setup/image32.png)
    
    这些卷将在“磁盘管理”页上显示为“联机”。
    
    ![联机卷](./media/storsimple-ova-deploy3-iscsi-setup/image33.png)

## <a name="next-steps"></a>后续步骤
了解如何使用本地 Web UI [管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md)。

## <a name="appendix-a-get-the-iqn-of-a-windows-server-host"></a>附录 A：获取 Windows Server 主机的 IQN
执行以下步骤，获取正在运行 Windows Server 2012 的 Windows 主机的 iSCSI 限定名称 (IQN)。

#### <a name="to-get-the-iqn-of-a-windows-host"></a>获取 Windows 主机的 IQN
1. 在 Windows 主机上启动 Microsoft iSCSI 发起程序。
2. 在“iSCSI 发起程序属性”窗口中的“配置”选项卡上，选择并复制“发起程序名称”字段中的字符串。
   
    ![iSCSI 发起程序属性](./media/storsimple-ova-deploy3-iscsi-setup/image34.png)
3. 保存此字符串。

<!--Reference link-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx






<!--HONumber=Dec16_HO2-->


