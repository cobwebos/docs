---
title: 如何导入和导出 Azure AD Connect 配置设置
description: 本文档描述有关云预配的常见问题。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/25/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a13236294f74bbe4bdaf8c1a30408afad09d9796
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225081"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a> (公共预览导入和导出 Azure AD Connect 配置设置)  

Azure AD Connect 部署不同于单个林快速模式安装，也不同于使用自定义同步规则跨多个林同步的复杂部署。 由于配置选项和机制数量巨大，因此必须了解哪些设置有效，并且能够快速部署具有相同配置的服务器。 此功能引入了对给定同步服务器的配置进行分类并将这些设置导入到新部署中的功能。 可以比较不同的同步设置快照，以便轻松地直观显示两个服务器之间的差异，或一段时间内的同一服务器。 

每次从 Azure AD Connect 向导更改配置时，会将一个新的时间戳 JSON 设置文件自动导出到 **%ProgramData%\AADConnect**。 设置文件名的格式为**SynchronizationPolicy-*。JSON** ，其中文件名的最后一部分是时间戳。 

>[!IMPORTANT]
> 仅自动导出 Azure AD Connect 所做的更改。 使用 PowerShell、Synchronization Service Manager 或同步规则编辑器所做的任何更改都必须按需导出，以维护最新副本。 按需导出还可用于将设置的副本放在安全位置，以实现灾难恢复。 

## <a name="exporting-azure-ad-connect-settings"></a>导出 Azure AD Connect 设置 

若要查看配置设置的摘要，请打开 Azure AD Connect 工具，然后选择名为：查看或导出当前配置的其他任务。 将显示设置的快速摘要，以及导出服务器的完整配置的功能。 

默认情况下，设置将导出到 **%ProgramData%\AADConnect**，但你可以选择将设置保存到受保护的位置，以确保在发生灾难时的可用性。 设置是使用 JSON 文件格式导出的，不应手动创建或编辑，以确保逻辑一致性。 不支持导入手动创建或编辑的文件，可能会导致意外的结果。 

## <a name="importing-azure-ad-connect-settings"></a>导入 Azure AD Connect 设置 

若要导入以前导出的设置，请执行以下操作：
 
1. 在新服务器上安装**Azure AD Connect** 。 
2. 在**欢迎**页面后，选择 "**自定义**" 选项。 
3. 单击 "**导入同步设置**"。 浏览先前导出的 json 设置文件。  
4. 单击“安装”。

![安装组件](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> 请在此页上覆盖设置，如使用 SQL Server 而不是 LocalDB 或使用现有的服务帐户而不是默认的 VSA 等。这些设置不会从配置设置文件中导入，而是用于信息和比较目的。

### <a name="import-installation-experience"></a>导入安装体验 

导入的安装体验特意保持简单，用户输入非常简单，可以轻松地提供现有服务器的可再现性。  

下面是在安装体验中可以进行的唯一更改。 在从 Azure AD Connect 向导安装后，可以进行所有其他更改。： 
- **Azure Active Directory 凭据**  –默认情况下，建议使用用于配置原始服务器的 Azure 全局管理员帐户名，并且如果要 **MUST**将   信息同步到新目录，则必须更改。
- **用户登录**  –默认情况下会选择为原始服务器配置的登录选项，并且会自动提示输入配置过程中所需的凭据或其他信息。 在极少数情况下，可能需要设置具有不同选项的服务器，以避免更改活动服务器的行为。 否则，只需按 "下一步" 即可使用相同的设置。 
- **本地目录凭据**  –对于同步设置中包含的每个本地目录，你必须提供凭据以创建同步帐户或提供预先创建的自定义同步帐户。 此过程与全新安装体验相同，你无法添加或删除目录。 
- **配置选项**  –对于全新安装，你可以选择为是否启动自动同步或启用暂存模式配置初始设置。 主要区别是，默认情况下，过渡模式默认启用，以允许在将结果主动导出到 Azure 之前比较配置和同步结果。 

![连接目录](media/how-to-connect-import-export-config/import2.png)

>[!NOTE]
>只有一个同步服务器可以位于主角色中，并主动将配置更改导出到 Azure。 必须将所有其他服务器置于暂存模式。 

## <a name="migrating-settings-from-an-existing-server"></a>从现有服务器迁移设置 

如果现有服务器不支持 "设置管理"，则可以选择就地升级服务器或迁移设置以在新的暂存服务器上使用。  

迁移需要运行 PowerShell 脚本，该脚本可提取现有设置以在新的安装中使用。建议使用此方法编录现有服务器的设置，然后将其应用于新安装的过渡服务器。将原始服务器的设置与新创建的服务器进行比较将快速可视化服务器之间的更改。与往常一样，请遵循组织的认证过程，确保无需进行其他配置。  

### <a name="migration-process"></a>迁移过程 
若要迁移这些设置，请执行以下操作：

1. 在新的暂存服务器上以管理员身份打开 cmd。
2. 通过运行以下内容提取**AzureADConnect.msi** ： `msiexec /a msifile/qb TARGETDIR=targetpath` 其中**msifile**是 msi 的地址， **targetpath**是要将文件提取到的目录。
   
   示例：`msiexec /a "C:\Holding\AzureADConnect.msi" TARGETDIR="C:\extractedfiles"`
3. 将**MigrateSettings.ps1**从 Microsoft Azure AD Connect\Tools 目录复制到现有服务器上的某个位置。  例如，C:\setup。  其中，安装程序是在现有服务器上创建的目录。 
![连接目录](media/how-to-connect-import-export-config/migrate1.png)

4. 运行如下所示的脚本，并保存整个下级服务器配置目录。 将该目录复制到新的过渡服务器。 请注意，需要将整个**ServerConfiguration-*** 文件夹复制到新服务器。 
 ![连接目录](media/how-to-connect-import-export-config/migrate2.png)

 ![连接目录](media/how-to-connect-import-export-config/migrate3.png)

5. 通过双击桌面上的图标启动**Azure AD Connect** 。 接受 EULA，在下一页上单击 "**自定义**" 按钮。 
6. 选择 "**导入同步设置**" 复选框，然后单击 "**浏览**" 按钮以浏览复制的 ServerConfiguration-* 文件夹，然后选择 "MigratedPolicy.js" 以导入迁移的设置。
 ![连接目录](media/how-to-connect-import-export-config/migrate4.png)

7. 若要将已迁移的设置与应用的设置的设置进行比较，请查找最新的**SynchronizationPolicy-*。JSON**和**SynchronizationPolicy-*。JSON** ( * 是 **%ProgramData%\AADConnect**下) 时间戳。 使用最常用的文件比较工具比较奇偶校验。 

## <a name="post-installation-verification"></a>安装后验证 

对于新部署的服务器，将最初导入的设置文件与导出的设置文件进行比较，这是了解目标与生成的部署之间的差异的必要步骤。 使用最喜欢的并排文本比较应用程序将生成即时可视化，以便快速突出显示任何所需或意外的更改。 尽管现在已经消除了许多以前的手动配置步骤，但你仍应遵循组织的认证过程，确保无需进行其他配置。 如果你使用的高级设置（当前在设置管理的公共预览版本中未捕获），则可能会发生此配置。 

已知限制包括： 
- **同步规则**  –自定义规则的优先顺序必须为0-99 的保留范围，以避免与 Microsoft 的标准规则发生冲突。 将自定义规则置于保留范围之外可能会导致自定义规则在添加到配置中时移动。 如果你的配置包含修改后的标准规则，则会出现类似问题。 强烈建议修改标准规则，并且规则放置可能不正确。 设备写回–这些设置将被编录，但在配置过程中当前未应用这些设置。 如果为原始服务器启用了设备写回，则必须在新部署的服务器上手动配置该功能。 
- **同步对象类型**  –尽管可以使用 Synchronization Service Manager 约束同步对象类型 (用户、联系人、组等 ) 的列表，但目前不支持通过同步设置来使用此功能。 完成安装后，必须手动重新应用高级配置。 
- **自定义运行配置文件**  -虽然可以使用 Synchronization Service Manager 修改默认运行配置文件集，但目前不支持通过同步设置来执行此功能。 完成安装后，必须手动重新应用高级配置。 
- **配置预配层次结构**  –不支持通过同步设置来支持 Synchronization Service Manager 的高级功能，在完成初始部署后，必须手动重新配置此功能。 
- **AD FS 和 PingFederate Authentication**  –将自动预选择与这些身份验证功能关联的登录方法，但必须以交互方式提供所有其他所需的配置参数。 

 ## <a name="next-steps"></a>后续步骤

- [硬件和先决条件](how-to-connect-install-prerequisites.md) 
- [快速设置](how-to-connect-install-express.md)
- [自定义设置](how-to-connect-install-custom.md)
- [安装 Azure AD Connect Health 代理](how-to-connect-health-agent-install.md) 
