---
title: 升级私有云权限-Azure CloudSimple VMware 解决方案
description: 介绍如何在 vCenter 中的管理功能在私有云上的特权提升
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d11c88b91b13cca13120a9203e376fdc2c3d6d8d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333234"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>从 CloudSimple 门户的私有云 vCenter 特权提升 

为到私有云 vCenter 的管理访问权限，可以暂时提升 CloudSimple 特权。  使用提升的权限，可以安装 VMware 解决方案、 添加标识源，以及管理用户。

新用户可以在 vCenter SSO 域上创建并授予对 vCenter 访问权限。  在创建新用户时，将其添加到 CloudSimple 内置组用于访问 vCenter。  有关详细信息，请参阅[CloudSimple 私有云的权限模型的 VMware vCenter](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)。

> [!CAUTION]
> 不要进行任何配置更改管理组件。 期间已提升的特权状态采取的操作可能会反过来影响您的系统或可能导致系统变得不可用。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="escalate-privileges"></a>提升特权

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开**资源**页上，选择你想要提升权限的私有云。

3. 在摘要页底部附近**更改 vSphere 权限**，单击**升级**。

    ![更改 vSphere 特权](media/escalate-private-cloud-privilege.png)

4. 选择 vSphere 用户类型。  仅 **CloudOwner@cloudsimple.local** 可升级本地用户。

5. 从下拉列表中选择的升级时间间隔。 选择将允许您完成任务的最短期限。

6. 选中复选框以确认你了解风险。

    ![提升权限对话框](media/escalate-private-cloud-privilege-dialog.png)

7. 单击“确定”。 

8. 升级过程可能需要几分钟的时间。 完成后，单击“确定”。 

权限提升开始并持续到所选间隔结束。  你可以登录到私有云 vCenter 来执行管理任务。

> [!IMPORTANT]
> 只有一个用户可以具有提升的权限。  之前可以提升另一个用户的权限，您必须取消提升用户的权限。

## <a name="extend-privilege-escalation"></a>扩展权限提升

如果需要额外的时间来完成任务，您可以扩展的权限升级期间。  选择其他升级，您可以完成管理任务的时间间隔。

1. 上**资源** > **私有云**在 CloudSimple 门户中，选择你想要扩展权限提升的私有云。

2. 附近的摘要选项卡底部，单击**扩展权限提升**。

    ![扩展权限提升](media/de-escalate-private-cloud-privilege.png)

3. 从下拉列表中选择升级时间间隔。 查看新的升级结束时间。

4. 单击**保存**来延长间隔。

## <a name="de-escalate-privileges"></a>取消提升权限

完成管理任务后，应取消升级你的特权。  

1. 上**资源** > **私有云**在 CloudSimple 门户中，选择你想要取消提升特权的私有云。

2. 单击**安抚**。

3. 单击“确定”。 

> [!IMPORTANT]
> 若要避免出现任何错误，注销 vCenter 并取消不断提高的权限后，再次登录。

## <a name="next-steps"></a>后续步骤

* [将 vCenter 标识源设置为使用 Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* 安装备份解决方案来[备份工作负荷的虚拟机](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)