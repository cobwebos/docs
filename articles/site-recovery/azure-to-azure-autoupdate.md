---
title: 在 Azure Site Recovery 中自动更新移动服务
description: 使用 Azure Site Recovery 复制 Azure Vm 时自动更新移动服务的概述。
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b2f5faea3df695500ea245d1dc71cb96a84c3643
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985595"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Azure 到 Azure 复制的自动更新移动服务

Azure Site Recovery 使用每月发布节奏来修复任何问题并增强现有功能或添加新功能。 若要使服务保持最新，必须每月规划修补程序部署。 若要避免每次升级关联的开销，可以允许 Site Recovery 管理组件更新。

如[azure 到 azure 灾难恢复体系结构](azure-to-azure-architecture.md)中所述，移动服务安装在从一个 Azure 区域复制到另一个 azure 区域的所有 azure 虚拟机（vm）上。 使用 "自动更新" 时，每个新版本会更新移动服务扩展。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>自动更新的工作方式

当你使用 Site Recovery 管理更新时，它将通过在与保管库相同的订阅中创建的自动化帐户来部署 Azure 服务使用的全局 runbook。 每个保管库使用一个自动化帐户。 对于保管库中的每个 VM，runbook 将检查是否有活动的自动更新。 如果有较新版本的移动服务扩展可用，则会安装更新。

默认 runbook 计划每日的时间为复制的 VM 地域时区的凌晨12:00。 还可以通过自动化帐户来更改 runbook 计划。

> [!NOTE]
> 从[更新汇总 35](site-recovery-whats-new.md#updates-march-2019)开始，你可以选择要用于更新的现有自动化帐户。 在更新汇总35之前，默认情况下 Site Recovery 创建了自动化帐户。 仅当为 VM 启用复制时，才能选择此选项。 它不适用于已启用复制的虚拟机。 选择的设置适用于在同一保管库中受保护的所有 Azure Vm。

启用自动更新不需要重新启动 Azure Vm，也不会影响正在进行的复制。

自动化帐户中的作业计费基于每月使用的作业运行时分钟数。 作业执行每天需要几秒钟到大约一分钟的时间，并作为免费单元进行了介绍。 默认情况下，500分钟包含为自动化帐户的免费单位，如下表所示：

| 包含免费单位数（每月） | 价格 |
|---|---|
| 作业运行时500分钟 | ₹ 0.14/分钟

## <a name="enable-automatic-updates"></a>启用自动更新

Site Recovery 可以通过多种方式来管理扩展更新：

- [作为 "启用复制" 步骤的一部分进行管理](#manage-as-part-of-the-enable-replication-step)
- [在保管库内切换扩展更新设置](#toggle-the-extension-update-settings-inside-the-vault)
- [手动管理更新](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>作为 "启用复制" 步骤的一部分进行管理

为 VM 启用复制时，可以[从 vm 视图](azure-to-azure-quickstart.md)或[从恢复服务保管库](azure-to-azure-how-to-enable-replication.md)中进行复制，可以允许 Site Recovery 管理 Site Recovery 扩展的更新，也可以手动对其进行管理。

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="扩展设置":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>在保管库内切换扩展更新设置

1. 在恢复服务保管库中，请参阅**管理**  >  **Site Recovery 基础结构**。
1. 在 "**适用于 Azure 虚拟机的**  >  **扩展更新**  >  **Site Recovery**设置" 中，选择 **"打开**"。

   若要手动管理扩展，请选择 "**关闭**"。

1. 选择“保存”。

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="扩展更新设置":::

> [!IMPORTANT]
> 选择 "**允许 Site Recovery 管理**" 时，该设置将应用到保管库中的所有 vm。

> [!NOTE]
> 任一选项都通知你用于管理更新的自动化帐户。 如果是首次在保管库中使用此功能，则默认情况下会创建一个新的自动化帐户。 或者，可以自定义设置，并选择现有的自动化帐户。 在同一保管库中启用复制的所有后续操作都将使用以前创建的自动化帐户。 目前，下拉菜单将仅列出与保管库位于同一资源组中的自动化帐户。

### <a name="manage-updates-manually"></a>手动管理更新

1. 如果你的 Vm 上安装了移动服务的新更新，你将看到以下通知： "**新建 Site Recovery 复制代理更新" 可用。单击 "安装"。**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="“复制的项”窗口":::

1. 选择通知以打开 VM 选择页。
1. 选择要升级的 Vm，然后选择 **"确定"**。 将为每个选定的 VM 启动更新移动服务。

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="“复制的项”VM 列表":::

## <a name="common-issues-and-troubleshooting"></a>常见问题和故障排除

如果自动更新存在问题，你会在保管库仪表板中的 "**配置问题**" 下看到错误通知。

如果无法启用自动更新，请参阅以下常见错误和建议的操作：

- **错误**：没有权限创建 Azure 运行方式帐户（服务主体）并向服务主体授予参与者角色。

  **建议的操作**：确保已将登录的帐户分配为投稿方，然后重试。 有关分配权限的详细信息，请参阅[如何：使用门户创建 Azure AD 应用程序以及可访问资源的服务主体](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)的 "所需权限" 部分。

  若要在启用自动更新后解决大多数问题，请选择 "**修复**"。 如果 "修复" 按钮不可用，请参阅 "扩展更新设置" 窗格中显示的错误消息。

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="扩展更新设置中 Site Recovery 服务修复按钮":::

- **错误**：运行方式帐户没有访问恢复服务资源的权限。

  **建议的操作**：删除[运行方式帐户，然后重新创建该帐户](/azure/automation/automation-create-runas-account)。 或者，确保自动化运行方式帐户的 Azure Active Directory 应用程序可以访问恢复服务资源。

- **错误**：找不到运行方式帐户。 以下某一内容已删除或未创建 - Azure Active Directory 应用程序、服务主体、角色、自动化证书资产、自动化连接资产；或者证书和连接的指纹不同。

  **建议的操作**：删除[运行方式帐户，然后重新创建该帐户](/azure/automation/automation-create-runas-account)。

- **错误**： automation 帐户使用的 Azure 运行方式证书即将过期。

  为运行方式帐户创建的自签名证书从创建日期起一年后过期。 可以在该证书过期之前的任何时间续订。 如果你已注册了电子邮件通知，则在一侧需要执行某个操作时，还会收到电子邮件。 此错误将在到期日期之前的两个月内显示，如果证书已过期，将更改为严重错误。 证书过期后，自动更新将不起作用，除非你续订了同一。

  **建议的操作**：若要解决此问题，请选择 "**修复**"，然后单击 "**续订证书**"。

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="续订-证书":::

  > [!NOTE]
  > 续订证书后，刷新页面以显示当前状态。
