---
title: 教程 - 了解如何访问私有云
description: 了解如何访问 Azure VMware 解决方案 (AVS) 私有云
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fcee332818c89d9c8b00795dca9e74f68260eefd
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82739582"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>教程：了解如何访问 Azure VMware 解决方案 (AVS) 私有云

在预览期，AVS 不允许使用本地 vCenter 管理私有云。 需要通过一个 Jumpbox 对本地 vCenter 实例执行额外的设置和连接。 

在本教程中，我们为上一篇教程 [Tutorial:Configure networking for your VMWare private cloud in Azure](tutorial-configure-networking.md)（教程：在 Azure 中配置 VMWare 私有云的网络）中创建的资源组中的某个 Jumpbox 创建 Windows 虚拟机，然后登录到 vCenter。 此 VM 位于创建的同一虚拟网络中，提供对 vCenter 和 NSX 管理器的访问。 

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建用于连接到 vCenter 的 Windows 虚拟机
> * 从虚拟机登录到 vCenter

## <a name="create-a-new-windows-virtual-machine"></a>创建新的 Windows 虚拟机

在资源组中选择“+ 添加”，搜索并选择“Microsoft Windows 10”，然后单击“创建”    。

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="为 Jumpbox 添加新的 Windows 10 VM" border="true":::

在字段中输入所需信息，然后选择“查看 + 创建”  。 有关字段的详细信息，请参阅下表。

| 字段 | 值 |
| --- | --- |
| **订阅** | 已使用资源组所属的订阅填充此值。 |
| **资源组** | 已为当前资源组填充此值。 此资源组应是在前一教程中创建的资源组。 |
| **虚拟机名称** | 输入 VM 的唯一名称。 |
| **区域** | 选择 VM 的地理位置。 |
| **可用性选项** | 保留选择的默认值。 |
| **图像** | 选择 VM 映像。 |
| **大小** | 保留默认大小值。 |
| **身份验证类型**  | 选择“密码”。  |
| **用户名** | 输入用于登录到 VM 的用户名。 |
| **密码** | 输入用于登录到 VM 的密码。 |
| **确认密码** | 输入用于登录到 VM 的密码。 |
| **公共入站端口** | 选择“无”。  如果选择“无”，可以使用 [JIT 访问](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-)，以便仅在需要访问 VM 时才对其进行访问。  |

输入适当的信息后，单击“查看 + 创建”  。 验证通过后，选择“创建”启动虚拟机创建过程  。

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="为 Jumpbox 创建新的 Windows 10 VM" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>连接到私有云的本地 vCenter

使用 VMware vCenter SSO 从 Jumpbox 登录到 vSphere 客户端。 使用云管理员用户名登录到 vSphere 客户端；看到有关潜在安全风险的警告时，请接受安全风险并继续；使用单一登录凭据登录到 VMware vCenter，并验证是否成功显示了用户界面。

在 Azure 门户中选择你的私有云，然后在“概览”视图中选择“标识”>“默认”   。 此时会显示私有云 vCenter 和 NSX-T 管理器的 URL 和登录凭据。

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="显示私有云 vCenter 和 NSX 管理器 URL 和凭据" border="true":::

导航到在上一步创建的虚拟机，并连接到该虚拟机。 有关如何连接到虚拟机的详细步骤，请参阅[连接到虚拟机](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)

在 Windows VM 中，打开浏览器并导航到两个选项卡中的 vCenter 和 NSX-T 管理器 URL。 在“vCenter”选项卡中，输入在上一步获取的 `cloudadmin@vmcp.local` 用户凭据。

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="登录到私有云 vCenter" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter 门户" border="true":::

在浏览器的第二个选项卡中，登录到 NSX-T 管理器。

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="本地私有云 NSX 管理器主页" border="true":::

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建用于连接到 vCenter 的 Windows 虚拟机
> * 从虚拟机登录到 vCenter

继续学习下一教程，了解如何缩放 AVS 私有云。

> [!div class="nextstepaction"]
> [缩放 AVS 私有云](tutorial-scale-private-cloud.md)