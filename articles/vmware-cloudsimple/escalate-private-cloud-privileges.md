---
title: 提升私有云权限
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述如何在 vCenter 中升级私有云上的管理功能的权限
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3ee722f88f766178dd658600893b4497bf611be9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75372765"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>从 CloudSimple 门户升级私有云 vCenter 权限

对于私有云 vCenter 的管理访问权限，可以暂时提升 CloudSimple 权限。  使用提升的权限，你可以安装 VMware 解决方案、添加标识源以及管理用户。

可以在 vCenter SSO 域上创建新用户，并授予对 vCenter 的访问权限。  创建新用户时，请将其添加到用于访问 vCenter 的 CloudSimple 内置组。  有关详细信息，请参阅[VMware vCenter 的 CloudSimple 私有云权限模型](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)。

> [!CAUTION]
> 不要对管理组件进行任何配置更改。 在提升的特权状态中执行的操作可能会对系统产生负面影响，或者导致系统变得不可用。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="escalate-privileges"></a>提升特权

1. 访问[CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开 "**资源**" 页，选择要提升其权限的私有云。

3. 在 "**更改 vSphere 权限**" 下的 "摘要" 页面底部附近，单击 "**升级**"。

    ![更改 vSphere 权限](media/escalate-private-cloud-privilege.png)

4. 选择 vSphere 用户类型。  只能升级 `CloudOwner@cloudsimple.local` 的本地用户。

5. 从下拉项中选择 "提升时间间隔"。 选择将允许您完成任务的最短时间。

6. 选中该复选框以确认你了解风险。

    ![提升权限对话框](media/escalate-private-cloud-privilege-dialog.png)

7. 单击“确定”。

8. 升级过程可能需要几分钟的时间。 完成后，单击“确定”。

权限提升开始，并一直持续到所选间隔结束。  你可以登录到私有云 vCenter 来执行管理任务。

> [!IMPORTANT]
> 只有一个用户可以具有提升的权限。  必须先取消升级用户的权限，然后才能升级其他用户的权限。

## <a name="extend-privilege-escalation"></a>扩展权限提升

如果需要额外的时间来完成任务，则可以扩展权限提升期限。  选择允许你完成管理任务的其他升级时间间隔。

1. 在 CloudSimple 门户中 > **私有云**的**资源**上，选择要为其扩展权限升级的私有云。

2. 在 "摘要" 选项卡的底部附近，单击 "**扩展权限提升**"。

    ![扩展权限提升](media/de-escalate-private-cloud-privilege.png)

3. 从下拉项中选择一个升级时间间隔。 查看新的 "升级结束时间"。

4. 单击 "**保存**" 以延长时间间隔。

## <a name="de-escalate-privileges"></a>反呈报权限

完成管理任务后，应取消对权限的升级。  

1. 在 CloudSimple 门户中 > **私有云**的**资源**上，选择要为其解除升级权限的私有云。

2. 单击 "**取消升级**"。

3. 单击“确定”。

> [!IMPORTANT]
> 若要避免任何错误，请注销 vCenter，并在解除升级的权限后再次登录。

## <a name="next-steps"></a>后续步骤

* [设置要使用的 vCenter 标识源 Active Directory](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* 安装备份解决方案以[备份工作负荷虚拟机](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)