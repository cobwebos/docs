---
title: "部署 StorSimple 设备管理器服务 | Microsoft 文档"
description: "说明如何在 Azure 门户中创建和删除 StorSimple 设备管理器服务，并介绍了如何管理服务注册密钥。"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/04/2017
ms.author: alkohli
ms.openlocfilehash: 51db9539451afafe7eddaaeef0e02328431611de
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>为 StorSimple 8000 系列设备部署 StorSimple 设备管理器服务

## <a name="overview"></a>概述

StorSimple 设备管理器服务在 Microsoft Azure 中运行并连接到多个 StorSimple 设备。 创建此服务后，可以使用它从单个中央位置监视连接到 StorSimple 设备管理器服务的所有设备，因此最大限度地减少了管理负担。

本教程介绍了创建、删除和迁移此服务以及管理服务注册密钥所需执行的步骤。 本文中包含的信息仅适用于 StorSimple 8000 系列设备。 有关 StorSimple 虚拟阵列的详细信息，请转到[为 StorSimple 虚拟阵列部署 StorSimple 设备管理器服务](storsimple-virtual-array-manage-service.md)。

## <a name="create-a-service"></a>创建服务
若要创建 StorSimple 设备管理器服务，需要具有：

* 一个具有企业协议的订阅
* 一个活动的 Microsoft Azure 存储帐户
* 用于访问管理的帐单信息

仅允许使用具有企业协议的订阅。 Azure 门户中不支持 Azure 经典门户中过去允许使用的 Microsoft 赞助订阅。 使用不受支持的订阅时将会看到以下消息：

![订阅无效](./media/storsimple-8000-manage-service/subscription-not-valid.jpg)

在创建服务时，还可以选择生成默认存储帐户。

单个服务可以管理多台设备。 但是，一台设备不能跨越多个服务。 大型企业可以具有多个服务实例，以便使用不同的订阅、组织，甚至是部署位置。 

> [!NOTE]
> 需要隔离 StorSimple 设备管理器服务的实例，管理 StorSimple 8000 系列设备和 StorSimple 虚拟阵列。

执行以下步骤，创建服务。

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


对于每项 StorSimple 设备管理器服务，存在以下属性：

* **名称** – 在创建 StorSimple 设备管理器服务时为其分配的名称。 **在创建服务后无法更改服务名称。这也适用于其他实体，例如，无法在 Azure 门户中重命名的设备、卷、卷容器和备份策略。**
* **状态** – 服务的状态，可以是**活动**、**正在创建**或**联机**。
* **位置** – 会在其中部署 StorSimple 设备的地理位置。
* **订阅** – 与服务关联的计费订阅。

## <a name="move-a-service-to-azure-portal"></a>将服务移动到 Azure 门户
现在可以在 Azure 门户中管理 StorSimple 8000 系列了。 如果有用来管理 StorSimple 设备的现有服务，建议将该服务移动到 Azure 门户。 在 2017 年 9 月 30 日之后，将不再支持针对 StorSimple Manager 服务的 Azure 经典门户。 如果想要移动到新的 Azure 门户，请参阅[转换注意事项](#considerations-for-transition)。 

> [!NOTE]
> 从 2017 年 10 月 5 日开始，经典 StorSimple 设备管理器将自动移至新的 Azure 门户。 此为分阶段转出，我们将通过电子邮件和门户通知告知你有关移动的最新消息。 如果你有任何问题，请参阅[常见问题解答：移至 Azure 门户](storsimple-8000-move-azure-portal-faq.md)。

### <a name="considerations-for-transition"></a>有关转换的注意事项

在移动此服务之前，请查看迁移到新 Azure 门户的影响。

> [!NOTE]
> 在移至新的 Azure 门户后，将不再支持现有的 Azure 服务管理 (ASM) PowerShell cmdlet。 请更新脚本以通过 Azure 资源管理器 SDK 管理设备。 有关详细信息，请转到[使用基于 Azure 资源管理器 SDK 的脚本管理 StorSimple 设备](storsimple-8000-automation-azurerm-scripts.md)。
> 新的 Azure 门户支持运行 Update 3.0 或更高版本的设备。 如果你的设备不是最新的，我们强烈建议请尽快应用 Update 5。

#### <a name="before-you-transition"></a>在转换之前

* 设备运行的是 Update 3.0 或更高版本。 如果设备运行的是较旧版本，请安装最新更新。 有关详细信息，请转到[安装 Update 5](storsimple-8000-install-update-5.md)。 如果在使用 StorSimple 云设备 (8010/8020)，则无法更新云设备。 请使用最新版本的软件新建使用 Update 5.0 的云设备，然后故障转移到新建的云设备。

* 一旦转换到新 Azure 门户，将无法使用 Azure 经典门户来管理 StorSimple 设备。

* 此转换无破坏性，设备不需要停机。

* 指定订阅下的所有 StorSimple 设备管理器都将转换。

#### <a name="during-the-transition"></a>在转换期间

* 无法从门户管理设备。
* 诸如分层和计划备份等操作将继续进行。
* 在转换进行过程中，不要删除旧的 StorSimple 设备管理器。

#### <a name="after-the-transition"></a>在转换之后

* 无法再从经典门户管理设备。

* 现有 Azure 服务管理 (ASM) PowerShell cmdlet 将不受支持。 请更新脚本以通过 Azure Resource Manager 管理设备。 有关使用资源管理器 SDK 的示例脚本，请参阅 [storsimpledevicemgmttools github](https://github.com/anoobbacker/storsimpledevicemgmttools)。

* 服务和设备配置将保留。 所有卷和备份也将转换到 Azure 门户。

### <a name="begin-transition"></a>开始转换

执行以下步骤将服务转换到 Azure 门户。

1. 在新的 Azure 门户中转到现有的 StorSimple Manager 服务。
    ![更多服务](./media/storsimple-8000-manage-service/service-browse01.png) ![选择设备管理器](./media/storsimple-8000-manage-service/service-browse02.png)

2. 可以看到一条通知，指出现在可以在 Azure 门户中使用 StorSimple 设备管理器服务了。 在 Azure 门户中，该服务称为 StorSimple 设备管理器服务。
    ![迁移通知](./media/storsimple-8000-manage-service/service-transition1.jpg)
    
    1. 请确保已查看了迁移的全部影响。
    2. 查看将从经典门户移动的 StorSimple 设备管理器的列表。

3. 单击“迁移”。 转换将开始并且需要花费几分钟时间才能完成。

转换完成后，可以通过 Azure 门户中的 StorSimple 设备管理器服务管理设备。 如果你没有看到用来迁移到 Azure 门户的选项，但你想要移动，则可[提交请求](https://aka.ms/ss8000-cx-signup)。

Azure 门户中仅支持运行 Update 3.0 和更高版本的 StorSimple 设备。 对于运行较旧版本的设备，仅提供有限支持。 在迁移到 Azure 门户后，可使用下表来了解在运行 Update 3.0 之前版本的设备上支持的操作。

| 操作                                                                                                                       | 支持      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| 注册设备                                                                                                               | 是            |
| 配置设备设置，例如常规、网络和安全性                                                                | 是            |
| 扫描、下载和安装更新                                                                                             | 是            |
| 停用设备                                                                                                               | 是            |
| 删除设备                                                                                                                   | 是            |
| 创建、修改和删除卷容器                                                                                   | 否             |
| 创建、修改和删除卷                                                                                             | 否             |
| 创建、修改和删除备份策略                                                                                      | 否             |
| 创建手动备份                                                                                                            | 否             |
| 创建计划的备份                                                                                                         | 不适用 |
| 从备份集还原                                                                                                        | 否             |
| 克隆到运行 Update 3.0 和更高版本的设备 <br> 源设备运行的是 Update 3.0 之前的版本。                                | 是            |
| 克隆到运行 Update 3.0 之前版本的设备                                                                          | 否             |
| 作为源设备进行故障转移 <br> （从运行 Update 3.0 之前版本的设备故障转移到运行 Update 3.0 和更高版本的设备）                                                               | 是            |
| 作为目标设备进行故障转移 <br> （故障转移到运行 Update 3.0 之前的软件版本的设备）                                                                                   | 否             |
| 清除警报                                                                                                                  | 是            |
| 查看在经典门户中创建的备份策略、备份目录、卷、卷容器、监视图表、作业和警报 | 是            |
| 打开和关闭设备控制器                                                                                              | 是            |


## <a name="delete-a-service"></a>删除服务

删除服务之前，请确保没有连接的设备正在使用它。 如果服务正在使用中，停用连接的设备。 停用操作将断开设备与服务之间的连接，但将设备数据保留在云中。

> [!IMPORTANT]
> 删除服务后，该操作不可逆。 使用该服务的任何设备都需要重置为出厂默认设置，才能与其他服务一起使用。 在这种情况下，设备上的本地数据以及配置都将丢失。

可以执行以下步骤来删除服务。

### <a name="to-delete-a-service"></a>删除服务

1. 搜索要删除的服务。 单击“资源”图标，然后输入要搜索的相应术语。 在搜索结果中，单击要删除的服务。

    ![搜索要删除的服务](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. 这将转到 StorSimple 设备管理器服务边栏选项卡。 单击“删除” 。

    ![删除服务](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. 在确认通知中单击“是”。 可能需要花费几分钟时间才能删除服务。

    ![确认删除](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>获取服务注册密钥

成功创建服务后，需要为 StorSimple 设备注册该服务。 若要注册第一台 StorSimple 设备，需要使用服务注册密钥。 若要向现有 StorSimple 服务注册额外的设备，需要使用注册密钥和服务数据加密密钥（后者是在注册期间在第一台设备上生成的）。 有关服务数据加密密钥的详细信息，请参阅 [StorSimple 安全性](storsimple-8000-security.md)。 可以通过访问 StorSimple 设备管理器边栏选项卡上的“密钥”来获取注册密钥。

可以执行以下步骤来获取服务注册密钥。

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

将服务注册密钥保存在安全位置。 要为其他设备注册此服务，需要使用此密钥以及服务数据加密密钥。 获取服务注册密钥后，必须通过用于 StorSimple 的 Windows PowerShell 界面配置设备。

有关如何使用此注册密钥的详细信息，请参阅[步骤 3：通过 Windows PowerShell for StorSimple 配置和注册设备](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)。

## <a name="regenerate-the-service-registration-key"></a>重新生成服务注册密钥
如果系统要求执行密钥轮换或者如果服务管理员列表发生更改，则需要重新生成服务注册密钥。 重新生成密钥时，新密钥仅用于注册后续设备。 已注册的设备不受此过程影响。

执行以下步骤，重新生成服务注册密钥。

### <a name="to-regenerate-the-service-registration-key"></a>重新生成服务注册密钥
1. 在“StorSimple Device Manager”边栏选项卡中，转到“管理”&gt;“密钥”。
    
    ![“密钥”边栏选项卡](./media/storsimple-8000-manage-service/regenregkey2.png)

2. 在“密钥”边栏选项卡中，单击“重新生成”。

    ![单击“重新生成”](./media/storsimple-8000-manage-service/regenregkey3.png)
3. 在“重新生成服务注册密钥”边栏选项卡中，查看重新生成密钥时所需的操作。 之后注册到此服务的所有设备将使用新的注册密钥。 单击“重新生成”以确认。 重新生成完成后，你将收到通知。

    ![确认重新生成](./media/storsimple-8000-manage-service/regenregkey4.png)

4. 将显示新的服务注册密钥。

5. 复制此密钥，并保存密钥以供为任何新设备注册此服务。



## <a name="change-the-service-data-encryption-key"></a>更改服务数据加密密钥
服务数据加密密钥用于加密机密客户数据，例如从 StorSimple Manager 服务发送到 StorSimple 设备的存储帐户凭据。 如果 IT 组织具有有关存储设备的密钥轮换策略，将需要定期更改这些密钥。 密钥更改过程可能会稍有不同，具体取决于 StorSimple Manager 服务管理的一个设备还是多个设备。 有关详细信息，请转到 [StorSimple 安全性和数据保护](storsimple-8000-security.md)。

更改服务数据加密密钥过程有 3 个步骤：

1. 使用适用于 Azure Resource Manager 的 Windows PowerShell 脚本，授权某个设备来更改服务数据加密密钥。
2. 使用 StorSimple 的 Windows PowerShell，启动服务数据加密密钥更改。
3. 如果有多个 StorSimple 设备，请更新其他设备上的服务数据加密密钥。

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>步骤 1：使用 Windows PowerShell 脚本授权某个设备来更改服务数据加密密钥
通常情况下，设备管理员会请求服务管理员授权某个设备来更改服务数据加密密钥。 然后，服务管理员会授权该设备来更改密钥。

此步骤是使用基于 Azure Resource Manager 的脚本执行的。 服务管理员可以选择有资格接受授权的一个设备。 然后，授权该设备来启动服务数据加密密钥更改过程。 

有关使用脚本的详细信息，请转到 [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>可以授权哪些设备来更改服务数据加密密钥？
设备必须满足以下条件，才能接受授权以启动服务数据加密密钥更改：

* 设备必须联机才有资格接受服务数据加密密钥更改授权。
* 如果密钥更改尚未启动，可以在 30 分钟后再次授权同一台设备。
* 如果先前授权的设备尚未启动密钥更改，可以授权其他设备。 新设备获得授权之后，旧设备不能启动更改。
* 当正在进行服务数据加密密钥的滚动更新时，将无法授权设备。
* 可以在以下情况下授权设备：某些使用服务注册的设备已滚动更新，但其他设备却没有。 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改
在 Windows PowerShell for StorSimple 界面中对授权的 StorSimple 设备执行此步骤。

> [!NOTE]
> 除非已完成密钥的滚动更新，否则无法在 StorSimple Manager 服务的 Azure 门户中执行任何操作。
> 
> 

如果使用设备串行控制台连接到 Windows PowerShell 界面，请执行以下步骤。

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>启动服务数据加密密钥更改
1. 选择选项 1 以使用完全访问权限登录。
2. 在命令提示符处，键入：
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. cmdlet 成功完成后，将收到新的服务数据加密密钥。 复制并保存此密钥，以供在此过程的步骤 3 中使用。 此密钥用于更新已使用 StorSimple Manager 服务注册的所有剩余设备。
   
   > [!NOTE]
   > 此过程必须在授权 StorSimple 设备后的四小时内启动。
   > 
   > 
   
   然后，此新密钥将发送到该服务，以推送到所有已使用该服务注册的设备。 之后，服务仪表板上会显示警报。 该服务将禁用已注册设备上的所有操作，随后设备管理员将需要更新其他设备上的服务数据加密密钥。 但是，不会中断 I/O（将数据发送到云的主机）。
   
   如果已有一台设备注册到服务，滚动更新过程现已完成，可以跳过下一步。 如果已有多台设备注册到服务，请继续执行步骤 3。

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>步骤 3：在其他 StorSimple 设备上更新服务数据加密密钥
如果已有多台设备注册到 StorSimple Manager 服务，则必须在 StorSimple 设备的 Windows PowerShell 界面中执行这些步骤。 必须使用在步骤 2 中获取的密钥来更新注册到 StorSimple Manager 服务的所有剩余 StorSimple 设备。

执行以下步骤，更新设备上的服务数据加密。

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>在物理设备上更新服务数据加密密钥
1. 使用 Windows PowerShell for StorSimple 连接到控制台。 选择选项 1 以使用完全访问权限登录。
2. 在命令提示符处，键入：`Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. 提供在[步骤 2：使用 Windows PowerShell for StorSimple 启动服务数据加密密钥更改](#to-initiate-the-service-data-encryption-key-change)中所获取的服务数据加密密钥。

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>更新所有 8010/8020 云设备上的服务数据加密密钥
1. 下载和安装 [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) PowerShell 脚本。 
2. 打开 PowerShell 并在命令提示符处键入：`Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

此脚本将确保在设备管理器下的所有 8010/8020 云设备上设置服务数据加密密钥。

## <a name="next-steps"></a>后续步骤
* 详细了解 [StorSimple 部署过程](storsimple-8000-deployment-walkthrough-u2.md)。
* 详细了解如何[管理 StorSimple 存储帐户](storsimple-8000-manage-storage-accounts.md)。
* 详细了解如何[使用 StorSimple 设备管理器服务管理 StorSimple 设备](storsimple-8000-manager-service-administration.md)。
