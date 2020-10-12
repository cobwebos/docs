---
title: 如何导入和导出 Azure AD Connect 配置设置
description: 本文介绍有关云预配的常见问题。
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87447037"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings-public-preview"></a>导入和导出 Azure AD Connect 配置设置（公共预览版）

Azure Active Directory (Azure AD) Connect 部署有多种变化，从单个林快捷模式安装到通过使用自定义同步规则跨多个林进行同步的复杂部署。 由于配置选项和机制数量巨大，因此了解哪些设置有效，且能够快速部署具有相同配置的服务器非常重要。 此功能引入了对给定同步服务器的配置进行分类并将设置导入到新部署中的功能。 可以比较不同的同步设置快照，以便轻松地直观显示两个服务器间的差异，或一段时间内同一服务器的差异。

每次从 Azure AD Connect 向导更改配置时，会自动将新的带时间戳的 JSON 设置文件导出到“%ProgramData%\AADConnect” **** 。 设置文件名的格式为“Applied-SynchronizationPolicy-*.JSON”，其中文件名的最后一部分为时间戳。

> [!IMPORTANT]
> 只会自动导出由 Azure AD Connect 进行的更改。 使用 PowerShell、Synchronization Service Manager 或同步规则编辑器所做的任何更改都必须根据需要按需导出，以维护最新副本。 按需导出还可用于将设置的副本放在安全位置，以实现灾难恢复。

## <a name="export-azure-ad-connect-settings"></a>导出 Azure AD Connect 设置 

若要查看配置设置的摘要，请打开 Azure AD Connect 工具，然后选择名为“查看或导出当前配置”的其他任务。 将显示你的设置的快速摘要以及用于导出服务器完整配置的功能。

默认情况下，设置会导出到 %ProgramData%\AADConnect。 你也可以选择将设置保存到受保护的位置，以确保在发生灾难时可用。 设置是使用 JSON 文件格式导出的，不应手动创建或编辑设置，以确保逻辑一致性。 不支持导入手动创建或编辑的文件，这可能会导致意外的结果。

## <a name="import-azure-ad-connect-settings"></a>导入 Azure AD Connect 设置

导入先前导出的设置，方法如下：
 
1. 在新服务器上安装 Azure AD Connect。
1. 在“欢迎”页后，选择“自定义”选项 。
1. 选择“导入同步设置”。 浏览先前导出的 JSON 设置文件。
1. 选择“安装”。

   ![显示“安装所需的组件”屏幕的屏幕截图](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> 替代此页上的设置，如使用 SQL Server 而不是 LocalDB 或使用现有服务帐户而不是默认 VSA。 这些设置不会从配置设置文件导入。 它们用于提供信息以及进行比较。

### <a name="import-installation-experience"></a>导入安装体验 

导入安装体验有意保持简单，只需用户进行极少的输入即可轻松地提供现有服务器的可再现性。

下面是在安装体验期间可以进行的唯一更改。 安装完成后，可通过 Azure AD Connect 向导进行所有其他更改：
- **Azure Active Directory 凭据**：默认情况下，建议使用用于配置原始服务器的 Azure 全局管理员帐户名。 如果要将信息同步到新目录，则必须更改它 。
- **用户登录**：默认情况下会选择其为原始服务器配置的登录选项，并自动提示输入配置期间所需的凭据或其他信息。 在极少数情况下，可能需要使用不同的选项来设置服务器，以避免更改活动服务器的行为。 否则，请选择“下一步”以使用相同的设置。
- **本地目录凭据**：对于同步设置中包含的每个本地目录，必须提供凭据以创建同步帐户或提供预先创建的自定义同步帐户。 此过程与全新安装体验相同，只是你不能添加或删除目录。
- **配置选项**：与全新安装一样，你可以选择配置初始设置，来确定是启动自动同步还是启用暂存模式。 主要区别在于，默认有意启用暂存模式，以允许在将结果主动导出到 Azure 之前比较配置和同步结果。

![显示“连接目录”屏幕的屏幕截图](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> 只有一个同步服务器可以担任主角色，并主动将配置更改导出到 Azure。 所有其他服务器都必须处于暂存模式。

## <a name="migrate-settings-from-an-existing-server"></a>从现有服务器迁移设置 

如果现有服务器不支持设置管理，则可以选择就地升级服务器或迁移设置以在新的暂存服务器上使用。

迁移需要运行 PowerShell 脚本，该脚本可提取现有设置以在新的安装中使用。 使用此方法对现有服务器的设置进行分类，然后将其应用于新安装的暂存服务器。 将原始服务器的设置与新创建的服务器进行比较，可以快速对服务器之间的更改进行可视化。 与往常一样，请遵循组织的认证过程以确保无需进行其他配置。

### <a name="migration-process"></a>迁移过程 
迁移设置的方法如下：

1. 在新暂存服务器上启动 AzureADConnect.msi，然后转到 Azure AD Connect 的“欢迎”页 。

1. 将 **MigrateSettings.ps1** 从 Microsoft Azure AD Connect\Tools 目录复制到现有服务器上的某个位置。 一个示例是 C:\setup，其中 setup 是在现有服务器上创建的目录。

   ![显示 Azure AD Connect 目录的屏幕截图。](media/how-to-connect-import-export-config/migrate1.png)

1. 运行如下所示的脚本，并保存整个下级服务器配置目录。 将该目录复制到新的暂存服务器。 必须将整个 Exported-ServerConfiguration-* 文件夹复制到新服务器。

   ![显示 Windows PowerShell 中的脚本的屏幕截图。](media/how-to-connect-import-export-config/migrate2.png)
   ![显示如何复制 Exported-Exported-ServerConfiguration-* 文件夹的屏幕截图。](media/how-to-connect-import-export-config/migrate3.png)

1. 通过双击桌面上的图标启动 Azure AD Connect。 接受 Microsoft 软件许可条款，然后在下一页上选择“自定义”。
1. 选择“导入同步设置”复选框。 选择“浏览”以浏览复制的 Exported-ServerConfiguration-* 文件夹。 选择 MigratedPolicy.json 以导入迁移的设置。

   ![显示“导入同步设置”选项的屏幕截图。](media/how-to-connect-import-export-config/migrate4.png)

## <a name="post-installation-verification"></a>安装后验证 

将原始导入的设置文件与新部署的服务器的导出的设置文件进行比较，是了解预期部署与所得部署之间任何差异的必要步骤。 使用你喜欢的并排文本比较应用程序会产出可快速突出显示任何所需或意外的更改的即时可视化效果。

尽管现在已经取消了许多先前的手动配置步骤，但你仍应遵循组织的认证过程，以确保无需进行其他配置。 如果你使用高级设置（其当前在公共预览版本的设置管理中尚未被捕获），则可能会发生此配置。

以下是已知限制：
- **同步规则**：自定义规则的优先顺序必须在 0 到 99 的保留范围内，以避免与 Microsoft 的标准规则发生冲突。 将自定义规则置于保留范围之外可能会导致自定义规则发生偏移，因为标准规则被添加到了配置。 如果配置包含修改后的标准规则，则会出现类似问题。 不鼓励修改标准规则，且规则放置可能不正确。
- **设备写回**：对这些设置进行了编录。 在配置过程中当前未应用这些设置。 如果为原始服务器启用了设备写回，则必须在新部署的服务器上手动配置该功能。
- **同步的对象类型**：尽管可以使用 Synchronization Service Manager 约束同步对象类型（如用户、联系人和组）的列表，但当前不支持通过同步设置来实现此功能。 完成安装后，必须手动重新应用高级配置。
- **自定义运行配置文件**：尽管可以使用 Synchronization Service Manager 修改默认的一组运行配置文件，但当前不支持通过同步设置来实现此功能。 完成安装后，必须手动重新应用高级配置。
- **配置预配层次结构**：不支持通过同步设置来实现 Synchronization Service Manager 的这一高级功能。 完成初始部署后，必须手动重新配置它。
- **Active Directory 联合身份验证服务 (AD FS) 和 PingFederate 身份验证**：与这些身份验证功能相关联的登录方法会自动进行预选。 必须以交互方式提供所有其他所需的配置参数。
- **已禁用的自定义同步规则将导入为已启用**：已禁用的自定义同步规则将导入为已启用。 请确保在新服务器上也禁用它。

 ## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 
