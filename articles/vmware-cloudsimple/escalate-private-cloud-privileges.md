---
title: 升级私有云权限
titleSuffix: Azure VMware Solution by CloudSimple
description: 描述如何升级私有云上的权限，以用于 vCenter 中的管理功能
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d06f3e8be449e7050c65c75339a0cff6efe19e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025310"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>从云简单门户升级私有云 vCenter 权限

要对私有云 vCenter 进行管理访问，您可以暂时升级云简单权限。  使用提升的权限，您可以安装 VMware 解决方案、添加标识源和管理用户。

可以在 vCenter SSO 域上创建新用户，并授予对 vCenter 的访问权限。  创建新用户时，请将它们添加到 CloudSimple 内置组中以访问 vCenter。  有关详细信息，请参阅[VMware vCenter 的云简单私有云权限模型](https://docs.azure.cloudsimple.com/learn-private-cloud-permissions/)。

> [!CAUTION]
> 不要对管理组件进行任何配置更改。 在升级的特权状态期间执行的操作可能会对您的系统产生负面影响，或可能导致您的系统不可用。

## <a name="sign-in-to-azure"></a>登录 Azure

登录到 中的[https://portal.azure.com](https://portal.azure.com)Azure 门户。

## <a name="escalate-privileges"></a>提升特权

1. 访问[云简单门户](access-cloudsimple-portal.md)。

2. 打开 **"资源"** 页面，选择要为其升级权限的私有云。

3. 在 **"更改 vSphere"权限**下的"摘要"页底部附近，单击"**升级**"。

    ![更改 vSphere 权限](media/escalate-private-cloud-privilege.png)

4. 选择 vSphere 用户类型。  只能`CloudOwner@cloudsimple.local`升级本地用户。

5. 从下拉列表中选择升级时间间隔。 选择允许您完成任务的最短期限。

6. 选择该复选框以确认您了解风险。

    ![升级权限对话框](media/escalate-private-cloud-privilege-dialog.png)

7. 单击“确定”。

8. 升级过程可能需要几分钟时间。 完成后，请单击“确定”****。

权限升级开始并持续到所选间隔的结束。  您可以登录到私有云 vCenter 以执行管理任务。

> [!IMPORTANT]
> 只有一个用户可以具有升级的权限。  必须先取消升级用户的权限，然后才能升级其他用户的权限。

> [!CAUTION]
> 新用户只能添加到*云所有者组*、*云-全球群集-管理员组*、*云-全球-存储-管理员组*、*云-全球-网络-管理员组*或*云-全球-VM-管理员组*。  添加到*管理员*组的用户将自动删除。  只能将服务帐户添加到*管理员*组，并且不得使用服务帐户登录到 vSphere Web UI。

## <a name="extend-privilege-escalation"></a>扩展权限升级

如果您需要额外的时间来完成任务，可以延长权限升级期。  选择允许您完成管理任务的额外升级时间间隔。

1. 在 CloudSimple 门户中的 **"资源** > **私有云**"上，选择要为其扩展权限升级的私有云。

2. 在"摘要"选项卡底部附近，单击 **"扩展权限升级**"。

    ![扩展权限升级](media/de-escalate-private-cloud-privilege.png)

3. 从下拉列表中选择升级时间间隔。 查看新的上报结束时间。

4. 单击 **"保存**"以延长间隔。

## <a name="de-escalate-privileges"></a>降级特权

完成管理任务后，应取消降级权限。  

1. 在 CloudSimple 门户中的 **"资源** > **私有云**"上，选择要取消权限的私有云。

2. 单击 **"取消升级**"。

3. 单击“确定”。

> [!IMPORTANT]
> 为避免任何错误，请注销 vCenter，并在取消升级权限后再次登录。

## <a name="next-steps"></a>后续步骤

* [设置 vCenter 标识源以使用活动目录](https://docs.azure.cloudsimple.com/set-vcenter-identity/)
* 将备份解决方案安装到[备份工作负载虚拟机](https://docs.azure.cloudsimple.com/backup-workloads-veeam/)