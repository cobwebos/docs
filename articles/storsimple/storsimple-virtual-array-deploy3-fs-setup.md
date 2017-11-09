---
title: "将 Azure StorSimple 虚拟阵列设置为文件服务器 | Microsoft 文档"
description: "此教程为 StorSimple 虚拟阵列部署的第三个教程，介绍如何将虚拟设备设置为文件服务器。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: f609f6ff-0927-48bb-a68a-6d8985d2fe34
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/17/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bf507fb21b314a6811db1c1e45a4356381caada1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-storsimple-virtual-array---set-up-as-file-server-via-azure-portal"></a>部署 StorSimple 虚拟阵列 - 通过 Azure 门户设置为文件服务器
![](./media/storsimple-virtual-array-deploy3-fs-setup/fileserver4.png)

## <a name="introduction"></a>介绍
本文介绍如何执行初始设置、注册 StorSimple 文件服务器、完成设备设置，以及创建和连接 SMB 共享。 这是此系列部署教程的最后一篇文章，是将虚拟阵列完全部署为文件服务器或 iSCSI 服务器所必读的。

完成设置和配置过程可能需要大约 10 分钟。 本文中的信息仅适用于部署 StorSimple 虚拟阵列。 有关 StorSimple 8000 系列设备的部署，请转到：[部署运行 Update 2 的 StorSimple 8000 系列设备](storsimple-deployment-walkthrough-u2.md)。

## <a name="setup-prerequisites"></a>设置先决条件
配置和设置 StorSimple 虚拟阵列之前，请确保：

* 已根据[在 Hyper-V 中预配 StorSimple 虚拟阵列](storsimple-virtual-array-deploy2-provision-hyperv.md)或[在 VMware 中预配 StorSimple 虚拟阵列](storsimple-virtual-array-deploy2-provision-vmware.md)中的详细说明预配和连接虚拟阵列。
* 已获得所创建的 StorSimple Device Manager 服务提供的服务注册密钥，该服务用于管理 StorSimple 虚拟阵列。 有关详细信息，请参阅适用于 StorSimple 虚拟阵列的[步骤 2：获取服务注册密钥](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)。
* 如果向现有 StorSimple Device Manager 服务注册过虚拟阵列，用户应该已经有了服务数据加密密钥。 该密钥是在成功地通过此服务注册第一台设备时生成的。 如果该密钥已丢失，请参阅[获取服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)（针对 StorSimple 虚拟阵列）。

## <a name="step-by-step-setup"></a>分步设置
使用以下分步说明设置和配置 StorSimple 虚拟阵列。

## <a name="step-1-complete-the-local-web-ui-setup-and-register-your-device"></a>步骤 1：完成本地 Web UI 设置并注册设备
#### <a name="to-complete-the-setup-and-register-the-device"></a>完成设备设置和注册
1. 打开浏览器窗口并连接到本地 Web UI。 键入：
   
   `https://<ip-address of network interface>`
   
   使用在上一步中记下的连接 URL。 此时会出现一个错误，指出网站的安全证书有问题。 单击“继续访问此网页”。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image2.png)
2. 以 **StorSimpleAdmin** 身份登录到虚拟阵列的 Web UI。 输入在“步骤 3：启动虚拟阵列”中更改的设备管理员密码，详见[在 Hyper-V 中预配 StorSimple 虚拟阵列](storsimple-virtual-array-deploy2-provision-hyperv.md)或[在 VMware 中预配 StorSimple 虚拟阵列](storsimple-virtual-array-deploy2-provision-vmware.md)。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image3.png)
3. 此时会转到“主页”。 该页介绍配置虚拟阵列并将其注册到 StorSimple Device Manager 服务所需的各种设置。 “网络设置”、“Web 代理设置”和“时间设置”为可选设置。 仅“设备设置”和“云设置”为必需设置。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image4.png)
4. 在“网络接口”下的“网络设置”页中，系统会自动为用户配置 DATA 0。 每个网络接口均默认设置为自动获取 IP 地址 (DHCP)。 因此，系统会自动分配 IP 地址、子网和网关（适用于 IPv4 和 IPv6）。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image5.png)
   
   如果在预配设备的过程中添加了多个网络接口，可在此处配置这些接口。 注意，可将网络接口配置为仅限 IPv4，或者同时支持 IPv4 和 IPv6。 不支持仅限 IPv6 的配置。
5. DNS 服务器是必需的，因为在设备尝试与云存储服务提供程序通信时，需要用到这些服务器；在按名称解析设备时，也需要用到这些配置为文件服务器的服务器。 在“DNS 服务器”下的“网络设置”页中：
   
   1. 将自动配置主 DNS 服务器和辅助 DNS 服务器。 如果选择配置静态 IP 地址，则可指定 DNS 服务器。 为了确保高可用性，建议同时配置主 DNS 服务器和辅助 DNS 服务器。
   2. 单击“应用”可应用并验证网络设置。
6. 在“设备设置”页中：
   
   1. 为设备指定唯一“名称”。 该名称长度为 1-15 个字符，并可包含字母、数字和连字符。
   2. 选择要创建的设备的“类型”时，请单击“文件服务器”图标 ![](./media/storsimple-virtual-array-deploy3-fs-setup/image6.png)。 文件服务器可用于创建共享文件夹。
   3. 由于设备是文件服务器，因此需将其加入域。 输入“域名”。
   4. 单击“应用” 。
7. 此时会显示一个对话框。 按指定格式输入域凭据。 单击选中图标。 此时会验证域凭据。 如果凭据不正确，则会显示错误消息。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image7.png)
8. 单击“应用” 。 此时会应用和验证设备设置。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image8.png)
   
   > [!NOTE]
   > 请确保虚拟阵列采用其自身的适用于 Active Directory 的组织单位 (OU)，没有对其应用或让其继承任何组策略对象 (GPO)。 组策略可能会在 StorSimple 虚拟阵列上安装防病毒软件等应用程序。 不支持安装其他软件，否则容易导致数据损坏。 
   > 
   > 
9. （可选）配置 Web 代理服务器。 尽管 Web 代理服务器配置是可选项，仍应注意，在使用 Web 代理时，只能在此处配置它。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image9.png)
   
   在“Web 代理”页中：
   
   1. 以下述格式提供“Web 代理 URL”：*http://&lt;主机 IP 地址或 FDQN&gt;:端口号*。 请注意，不支持 HTTPS URL。
   2. 将“身份验证”指定为“基本”或“无”。
   3. 如果使用身份验证，则还需提供“用户名”和“密码”。
   4. 单击“应用” 。 此时会验证并应用配置的 Web 代理设置。
10. （可选）配置设备的时间设置，例如时区以及主 NTP 服务器和辅助 NTP 服务器。 NTP 服务器是必需的，因为设备必须同步时间，才能通过云服务提供程序进行身份验证。
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/image10.png)
    
    在“时间设置”页中：
    
    1. 根据部署设备的地理位置，从下拉列表中选择相应的“时区” 。 设备的默认时区为太平洋标准时间。 设备将此时区用于所有计划操作。
    2. 为设备指定“主 NTP 服务器”，或者接受默认值：time.windows.com。确保网络允许 NTP 流量从数据中心传递到 Internet。
    3. （可选）为设备指定“辅助 NTP 服务器”。
    4. 单击“应用” 。 此时会验证并应用配置的时间设置。
11. 配置设备的云设置。 此步骤需完成本地设备配置，然后将设备注册到 StorSimple Device Manager 服务。
    
    1. 输入在[步骤 2：获取服务注册密钥](storsimple-virtual-array-deploy1-portal-prep.md#step-2-get-the-service-registration-key)（适用于 StorSimple 虚拟阵列）中获得的“服务注册密钥”。
    2. 如果这是注册到此服务的第一个设备，则会向你提供服务数据加密密钥。 复制此密钥并将其保存到一个安全位置。 向 StorSimple Device Manager 服务注册其他设备时，需要此密钥以及该服务注册密钥。 
       
       如果这不是注册到此服务的第一个设备，则需提供服务数据加密密钥。 有关详细信息，请参阅本地 Web UI 上的[获取服务数据加密密钥](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)。
    3. 单击“注册”。 此时会重新启动设备。 可能需要等待 2-3 分钟才能成功注册设备。 重新启动设备后，会转到登录页。
       
       ![](./media/storsimple-virtual-array-deploy3-fs-setup/image13.png)
12. 返回到 Azure 门户。 转到“所有资源”，搜索 StorSimple Device Manager 服务。
    
    ![](./media/storsimple-virtual-array-deploy3-fs-setup/searchdevicemanagerservice1.png) 
13. 在筛选列表中，选择 StorSimple Device Manager 服务，并导航到“管理”>“设备”。 在“设备”边栏选项卡中，验证设备是否已成功连接到服务，并且其状态是否为“已准备好设置”。
    
    ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png)

## <a name="step-2-configure-the-device-as-file-server"></a>步骤 2：将设备配置为文件服务器
在 [Azure 门户](https://portal.azure.com/)中执行以下步骤，完成所需的设备设置。

#### <a name="to-configure-the-device-as-file-server"></a>将设备配置为文件服务器
1. 转到 StorSimple Device Manager 服务，并转到“管理”>“设备”。 在“设备”边栏选项卡中，选择刚创建的设备。 此设备会显示为“已准备好设置”。
   
   ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs2m.png) 
2. 单击设备，会显示横幅消息，指示该设备已准备好设置。
   
    ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs3m.png)
3. 在命令栏中，单击“配置”。 此时会打开“配置”边栏选项卡。 在“配置”边栏选项卡中，执行以下操作：
   
    1. 将自动填充文件服务器名称。
    
    2. 确保云存储加密已设置为“启用”。 这会对发送到云的所有数据都进行加密。 
    
    3. 将使用 256 位 AES 密钥与用户定义密钥进行加密。 指定 32 个字符的密钥，并再次输入该密钥以进行确认。 在密钥管理应用中记录密钥以供将来参考。
    
    4. 单击“配置所需设置”，指定要用于设备的存储帐户凭据。 如果未配置任何存储帐户凭据，请单击“新增”。 **请确保所用的存储帐户支持块 blob。页 blob 不受支持。** 请查阅[有关块 blob 和页 blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) 的更多信息。
   
    ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs6m.png) 
4. 在“添加存储帐户凭据”边栏选项卡中，执行以下操作： 

    1. 如果存储帐户与服务位于相同的订阅中，请选择当前订阅。 如果存储帐户在服务订阅外部，则指定其他订阅。 
    
    2. 从下拉列表中，选择现有的存储帐户。 
    
    3. 将根据指定的存储帐户自动填充位置。 
    
    4. 启用 SSL 可确保设备和云之间的网络通信通道安全。
    
    5. 单击“添加”添加此存储帐户凭据。 
   
        ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs8m.png)

5. 成功创建存储帐户凭据后，“配置”边栏选项卡将更新以显示指定的存储帐户凭据。 单击 **“配置”**。
   
   ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs11m.png)
   
   会看到正在创建文件服务器。 成功创建文件服务器后，会收到通知。
   
   ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs13m.png)
   
   设备状态也将更改为“联机”。
   
   ![配置文件服务器](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs14m.png)
   
   可以继续添加共享。

## <a name="step-3-add-a-share"></a>步骤 3：添加共享
在 [Azure 门户](https://portal.azure.com/)中执行以下步骤，以创建共享。

#### <a name="to-create-a-share"></a>创建共享
1. 选择在上一步中配置的文件服务器设备，并单击“...”（或右键单击）。 在上下文菜单中，选择“添加共享”。 或者，可以单击设备命令栏上的“+ 添加共享”。
   
   ![添加共享](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs15m.png)
2. 指定以下共享设置：

    1. 共享的唯一名称。 该名称必须是包含 3 到 127 个字符的字符串。
    
    2. 共享的可选**说明**。 可以根据说明确定共享所有者。
    
    3. 共享的**类型**。 类型可以是“分层”或“本地固定”，默认为分层。 对于需要本地保证、低延迟和高性能的工作负荷，请选择“本地固定”共享。 对于所有其他数据，请选择“分层”共享。
    本地固定共享经过充分预配，可确保共享上的主数据保留在设备本地，未溢出到云。 分层共享则是进行精简预配。 创建分层共享时，10% 的空间预配在本地层，90% 的空间预配在云中。 例如，如果预配 1 TB 的卷，则当数据分层时，100 GB 会驻留在本地空间，900 GB 会在云中使用。 反过来说，这意味着，如果用光了设备上的所有本地空间，则无法预配分层共享。
   
    4. 在“将默认完全权限设置给”字段中，将权限分配给要访问此共享的用户或组。 以 *john@contoso.com* 格式指定用户或用户组的名称。 若要启用访问这些共享所需的管理员权限，建议使用用户组（而非单个用户）。 在此处分配权限以后，即可使用文件资源管理器修改这些权限。
   
    5. 单击“添加”以创建共享。 
    
        ![添加共享](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs18m.png)
   
        将通知你“正在创建共享”。
   
        ![添加共享](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs19m.png)
   
    使用指定的设置创建共享后，“共享”边栏选项卡将更新以反映新共享。 默认情况下，将为共享启用监视和备份功能。
   
    ![添加共享](./media/storsimple-virtual-array-deploy3-fs-setup/deployfs22m.png)

## <a name="step-4-connect-to-the-share"></a>步骤 4：连接到共享
现在需连接到上一步所创建的一个或多个共享。 已连接到 StorSimple 虚拟阵列的 Windows Server 主机上执行这些步骤。

#### <a name="to-connect-to-the-share"></a>连接到共享
1. 按 ![](./media/storsimple-virtual-array-deploy3-fs-setup/image22.png) + R。在“运行”窗口中，指定“&#92;&#92;&lt;文件服务器名称&gt;”作为路径，将“文件服务器名称”替换为分配给文件服务器的设备名称。 单击“确定”。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image23.png)
2. 这会打开文件资源管理器。 此时应能看到作为文件夹创建的共享。 选择并双击要查看其内容的共享（文件夹）。
   
   ![](./media/storsimple-virtual-array-deploy3-fs-setup/image24.png)
3. 此时可向这些共享添加文件并进行备份。

## <a name="next-steps"></a>后续步骤
了解如何使用本地 Web UI [管理 StorSimple 虚拟阵列](storsimple-ova-web-ui-admin.md)。

