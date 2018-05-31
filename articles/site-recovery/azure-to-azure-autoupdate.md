---
title: Azure 到 Azure 灾难恢复中的移动服务自动更新 | Microsoft Docs
description: 提供移动服务自动更新概述，此服务用于通过 Azure Site Recovery 复制 Azure VM。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: rajanaki
ms.openlocfilehash: 45f2e2927f699769bb385038c04d4dd23e075a9a
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/03/2018
ms.locfileid: "32779631"
---
# <a name="automatic-update-of-mobility-service-extension-in-azure-to-azure-replication"></a>Azure 到 Azure 复制中的移动服务扩展自动更新

Azure Site Recovery 每月发布一次，增加对现有功能或新功能的增强功能，并对已知问题（如果存在）进行修复。 这就意味着，若要保持服务最新状态，需计划每月部署一次这些修补程序。 为避免产生与升级相关的开销，用户可改为选择允许 Site Recovery 管理组件更新。 如 Azure 到 Azure 灾难恢复的[体系结构参考](azure-to-azure-architecture.md)中所述，如果 Azure 虚拟机在将虚拟机从一个 Azure 区域复制到另一个 Azure 区域的同时启用了复制，则在所有此类 Azure 虚拟机上安装移动服务。 本文档详述了以下内容：

- 自动更新的工作原理
- 启用自动更新
- 常见问题与故障排除
 
## <a name="how-does-automatic-update-work"></a>自动更新的工作原理

一旦允许 Site Recovery 管理更新，则将通过自动化帐户（与保管库在同一订阅中创建）部署 Azure 服务使用的全局 runbook。 一个自动化帐户用于一个特定保管库。 runbook 检查保管库中每个已启用自动更新的 VM，并在新版本可用时启动移动服务拓展升级。 根据复制虚拟机地区的时区，runbook 默认计划在每天上午 12:00 重复。 如有必要，用户还可通过自动化帐户修改 runbook 计划。 

## <a name="enable-automatic-updates"></a>启用自动更新

可选择允许 Site Recovery 通过以下方式管理更新：

- [作为启用复制步骤的一部分](#as-part-of-the-enable-replication-step)
- [在保管库内切换扩展更新设置](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>作为启用复制步骤的一部分：

[从虚拟机](azure-to-azure-quickstart.md)或[从恢复服务保管库](azure-to-azure-how-to-enable-replication.md)启用虚拟机复制时，可选择允许 Site Recovery 管理 Site Recovery 扩展更新或手动管理更新。

![enable-replication-auto-update](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>在保管库内切换扩展更新设置

1. 在保管库中，导航到“管理”-> “Site Recovery 基础结构”
2. 在“用于 Azure 虚拟机”-> “扩展更新设置”下，单击切换按钮，选择允许 ASR 管理更新或手动管理。 单击“ **保存**”。

![vault-toggle-autuo-update](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> 如果选择“允许 ASR 管理”，该设置会应用到相应保管库中的所有虚拟机。


> [!Note] 
> 两个选项均会就用于管理更新的自动化帐户通知用户。 如果是首次在保管库中启用此功能，将创建新的自动化帐户。 同一个保管库中的所有后续启用复制将使用之前创建的帐户。

## <a name="common-issues--troubleshooting"></a>常见问题与故障排除

如果自动更新出现问题，将会在保管库仪表板的“配置问题”下收到相同通知。 

如果尝试启用自动更新但操作失败，请参阅以下内容进行故障排除。

**错误**：没有权限创建 Azure 运行方式帐户（服务主体）并向服务主体授予参与者角色。 
- 建议的操作：请确保已为登录的帐户分配“参与者”角色，然后重试操作。
 
启用自动更新后，Site Recovery 服务可修复大部分问题，这需要单击“修复”按钮。

![repair-button](./media/azure-to-azure-autoupdate/repair.png)

如果修复按钮不可用，请参阅扩展设置窗格中显示的错误消息。

 - **错误**：运行方式帐户没有访问恢复服务资源的权限。

    **推荐的操作**：删除该帐户，然后[重新创建运行方式帐户](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account)，或确保自动化运行方式帐户的 Azure Active Directory 应用程序有权访问恢复服务资源。

- **错误**：找不到运行方式帐户。 以下某一内容已删除或未创建 - Azure Active Directory 应用程序、服务主体、角色、自动化证书资产、自动化连接资产；或者证书和连接的指纹不同。 

    **推荐的操作**：删除该帐户，然后[重新创建运行方式帐户](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account)。
