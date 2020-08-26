---
title: 如何导入和导出 Azure AD Connect 配置设置
description: 本文介绍云预配的常见问题。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da80af9fe598186fa25d59601c9fa4faccb4286a
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87447037"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings-public-preview"></a>导入和导出 Azure AD Connect 配置设置（公共预览版）

Azure Active Directory （Azure AD）连接部署的不同之处在于单个林快速模式安装与通过使用自定义同步规则跨多个林同步的复杂部署不同。 由于配置选项和机制数量巨大，因此必须了解哪些设置有效，并且能够快速部署具有相同配置的服务器。 此功能引入了对给定同步服务器的配置进行分类并将这些设置导入到新部署中的功能。 可以比较不同的同步设置快照，以便轻松地直观显示两个服务器之间的差异，或一段时间内的同一服务器。

每次从 Azure AD Connect 向导更改配置时，会自动将新的带时间戳的 JSON 设置文件导出到 **%ProgramData%\AADConnect**。 设置文件名的格式为**SynchronizationPolicy-*。JSON**，其中，文件名的最后一部分是时间戳。

> [!IMPORTANT]
> 仅自动导出 Azure AD Connect 所做的更改。 使用 PowerShell、Synchronization Service Manager 或同步规则编辑器所做的任何更改都必须根据需要按需导出，以维护最新副本。 按需导出还可用于将设置的副本放在安全位置，以实现灾难恢复。

## <a name="export-azure-ad-connect-settings"></a>导出 Azure AD Connect 设置 

若要查看配置设置的摘要，请打开 "Azure AD Connect" 工具，然后选择名为 "**查看" 或 "导出当前配置**" 的其他任务。 将显示设置的快速摘要，以及导出服务器的完整配置的功能。

默认情况下，设置将导出到 **%ProgramData%\AADConnect**。 还可以选择将设置保存到受保护的位置，以确保在发生灾难时的可用性。 设置是使用 JSON 文件格式导出的，不应手动创建或编辑，以确保逻辑一致性。 不支持导入手动创建或编辑的文件，这可能会导致意外的结果。

## <a name="import-azure-ad-connect-settings"></a>导入 Azure AD Connect 设置

导入以前导出的设置：
 
1. 在新服务器上安装**Azure AD Connect** 。
1. 选择 "**欢迎**" 页之后的 "**自定义**" 选项。
1. 选择 "**导入同步设置**"。 浏览先前导出的 JSON 设置文件。
1. 选择“安装”。

   ![显示 "安装所需组件" 屏幕的屏幕截图](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> 在此页上覆盖设置，如使用 SQL Server 而不是 LocalDB 或使用现有服务帐户而不是默认 VSA。 这些设置不会从配置设置文件中导入。 它们用于信息和比较目的。

### <a name="import-installation-experience"></a>导入安装体验 

导入安装体验有意保持简单，只需用户输入即可轻松地提供现有服务器的可再现性。

下面是在安装体验中可以进行的唯一更改。 在从 Azure AD Connect 向导安装后，可以执行其他所有更改：
- **Azure Active Directory 凭据**：默认情况下，建议使用用于配置原始服务器的 Azure 全局管理员帐户名。 *must*   如果要将信息同步到新目录，则必须更改此信息。
- **用户登录**：默认情况下会选择为原始服务器配置的登录选项，并自动提示输入配置过程中所需的凭据或其他信息。 在极少数情况下，可能需要设置具有不同选项的服务器，以避免更改活动服务器的行为。 否则，选择 "**下一步**" 以使用相同的设置。
- **本地目录凭据**：对于同步设置中包含的每个本地目录，必须提供凭据以创建同步帐户或提供预先创建的自定义同步帐户。 此过程与全新安装体验相同，但不能添加或删除目录。
- **配置选项**：对于全新安装，你可以选择为是否启动自动同步或启用暂存模式配置初始设置。 主要区别是，默认情况下，过渡模式默认启用，以允许在将结果主动导出到 Azure 之前比较配置和同步结果。

![显示 "连接目录" 屏幕的屏幕截图](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> 只有一个同步服务器可以位于主角色中，并主动将配置更改导出到 Azure。 必须将所有其他服务器置于暂存模式。

## <a name="migrate-settings-from-an-existing-server"></a>从现有服务器迁移设置 

如果现有服务器不支持 "设置管理"，则可以选择就地升级服务器或迁移设置以在新的暂存服务器上使用。

迁移需要运行 PowerShell 脚本，该脚本可提取现有设置以在新的安装中使用。使用此方法对现有服务器的设置进行分类，然后将其应用于新安装的过渡服务器。将原始服务器的设置与新创建的服务器进行比较可快速可视化服务器之间的更改。与往常一样，请遵循组织的认证过程，确保无需进行其他配置。

### <a name="migration-process"></a>迁移过程 
若要迁移设置：

1. 开始在新的过渡服务器上**AzureADConnect.msi** ，并在 Azure AD Connect 的 "**欢迎**" 页处停止。

1. 将**MigrateSettings.ps1**从 Microsoft Azure AD Connect\Tools 目录复制到现有服务器上的某个位置。 例如，C:\setup，其中，安装程序是在现有服务器上创建的目录。

   ![显示 Azure AD Connect 目录的屏幕截图。](media/how-to-connect-import-export-config/migrate1.png)

1. 运行如下所示的脚本，并保存整个下级服务器配置目录。 将该目录复制到新的过渡服务器。 必须将整个**ServerConfiguration-*** 文件夹复制到新服务器。

   ![在 Windows PowerShell 中显示脚本的屏幕截图。 ](media/how-to-connect-import-export-config/migrate2.png)
    ![显示复制 ServerConfiguration-* 文件夹的屏幕截图。](media/how-to-connect-import-export-config/migrate3.png)

1. 通过双击桌面上的图标开始**Azure AD Connect** 。 接受 Microsoft 软件许可条款，然后在下一页上，选择 "**自定义**"。
1. 选中 "**导入同步设置**" 复选框。 选择 "**浏览**" 以浏览复制的 ServerConfiguration-* 文件夹。 选择 "MigratedPolicy.js" 以导入迁移的设置。

   ![显示 "导入同步设置" 选项的屏幕截图。](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>安装后验证 

将最初导入的设置文件与新部署的服务器的导出设置文件进行比较是了解预期部署与生成的部署之间的任何差异的必要步骤。 使用最喜欢的并排文本比较应用程序将生成即时可视化，以便快速突出显示任何所需或意外的更改。

尽管现在已经消除了许多以前的手动配置步骤，但你仍应遵循组织的认证过程，确保无需进行其他配置。 如果你使用的高级设置（当前在设置管理的公共预览版本中未捕获），则可能会发生此配置。

下面是已知的限制：
- **同步规则**：自定义规则的优先顺序必须介于0到99之间，以避免与 Microsoft 的标准规则发生冲突。 将自定义规则置于保留范围之外可能会导致自定义规则在添加到配置中时移动。 如果你的配置包含修改后的标准规则，则会出现类似问题。 不鼓励修改标准规则，并且规则放置可能不正确。
- **设备写回**：对这些设置进行了编录。 在配置过程中当前未应用这些设置。 如果为原始服务器启用了设备写回，则必须在新部署的服务器上手动配置该功能。
- **同步对象类型**：虽然可以使用 Synchronization Service Manager 约束同步对象类型（如用户、联系人和组）的列表，但目前不支持通过同步设置使用此功能。 完成安装后，必须手动重新应用高级配置。
- **自定义运行配置文件**：尽管可以使用 Synchronization Service Manager 修改默认运行配置文件集，但目前不支持通过同步设置使用此功能。 完成安装后，必须手动重新应用高级配置。
- **配置预配层次结构**：不支持通过同步设置进行此 Synchronization Service Manager 的高级功能。 完成初始部署后，必须手动重新配置它。
- **Active Directory 联合身份验证服务（AD FS）和 PingFederate authentication**：会自动预选择与这些身份验证功能关联的登录方法。 必须以交互方式提供所有其他所需的配置参数。
- **禁用的自定义同步规则将导入为启用**：禁用的自定义同步规则将导入为启用。 还应确保在新服务器上禁用它。

 ## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 
