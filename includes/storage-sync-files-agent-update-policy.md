---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 5be5cf6cd410874d870b351c209517e90fcf3848
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699323"
---
Azure 文件同步代理将定期更新，以便添加新功能和解决问题。 建议配置 Microsoft 更新，以便在 Azure 文件同步代理更新发布时获得这些更新。

#### <a name="major-vs-minor-agent-versions"></a>主要与次要代理版本
* 主要代理版本通常包含新的功能，并且版本号的第一个组成部分会递增。 例如：2.\*.\*\*\*
* 次要代理版本也称为“修补”，其发布频率高于主要版本。 它们通常包含 bug 修复和小幅的改进，但不包含新功能。 例如：\*\*.3.\*\*

#### <a name="upgrade-paths"></a>升级路径
可以通过四种经过批准和测试的方法来安装 Azure 文件同步代理更新。 
1. **（首选）将 Microsoft 更新配置为自动下载并安装代理更新。**  
    我们始终建议安装每项 Azure 文件同步更新，确保能够访问服务器代理的最新修补程序。 Microsoft 更新会自动下载并安装这些更新，以无缝完成此过程。
2. **使用 AfsUpdater.exe 下载并安装代理更新。**  
    AfsUpdater.exe 位于代理安装目录中。 双击可执行文件可下载并安装代理更新。 
3. **使用 Microsoft 更新修补文件或 .msp 可执行文件修补现有的 Azure 文件同步代理。可以从 [Microsoft 更新目录](https://www.catalog.update.microsoft.com/Search.aspx?q=Azure%20File%20Sync)下载最新的 Azure 文件同步更新包。**  
    运行 .msp 可执行文件将会升级 Azure 文件同步安装，所用的方法与上述升级路径中 Microsoft 更新使用的自动方法相同。 应用 Microsoft 更新修补程序会就地升级 Azure 文件同步安装。
4. **从[Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)下载最新的 Azure 文件同步 agent 安装程序。**  
    若要升级现有的 Azure 文件同步代理安装，请卸载旧版本，然后使用下载的安装程序安装最新版本。 服务器注册、同步组和其他任何设置由 Azure 文件同步安装程序维护。

#### <a name="automatic-agent-lifecycle-management"></a>自动代理生命周期管理
使用代理版本 6, 文件同步团队引入了一个代理自动升级功能。 你可以选择两种模式之一, 并指定要在其上尝试在服务器上进行升级的维护时段。 此功能旨在帮助你实现代理生命周期管理, 方法是提供一个 guardrail, 以防止代理过期或允许无障碍, 并保持最新的设置。
1. **默认设置**将尝试防止代理过期。 在代理的发布到期日期21天内, 代理将尝试自行升级。 它将在过期之前的21天内, 一周内开始升级一次, 并在选定的维护时段内升级一次。 **此选项不会消除定期 Microsoft 更新修补程序的需求。**
1. 或者, 你可以选择在新代理版本可用时 (当前不适用于群集服务器), 代理将自动自动升级。 此更新将在选定的维护时段内发生, 并使你的服务器能够在公开发布后立即受益于新功能和改进。 这是建议的无需担心的设置, 它将为你的服务器提供主要代理版本以及定期更新修补程序。 每个发布的代理都处于 GA 质量。 如果选择此选项, Microsoft 将向你飞行最新的代理版本。 排除群集服务器。 试验完成后, 代理也将在[Microsoft 下载中心](https://go.microsoft.com/fwlink/?linkid=858257)aka.ms/AFS/agent 上变为可用。

 ##### <a name="changing-the-auto-upgrade-setting"></a>更改自动升级设置

以下说明介绍了在完成安装程序后如何更改设置 (如果需要进行更改)。

打开 shell 并导航到安装了同步代理的目录, 然后导入服务器 cmdlet, 默认情况下如下所示:
```powershell
cd C:\Program Files\Azure\StorageSyncAgent

ipmo .\StorageSync.Management.ServerCmdlets.dll
```

您可以运行`Get-StorageSyncAgentAutoUpdatePolicy`来检查当前策略设置并确定是否要对其进行更改。

若要将当前策略设置更改为延迟更新跟踪, 你可以使用:`Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode UpdateBeforeExpiration`

若要将当前策略设置更改为立即更新跟踪, 你可以使用:`Set-StorageSyncAgentAutoUpdatePolicy -PolicyMode InstallLatest`

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理生命周期和变更管理保证
Azure 文件同步是一种云服务, 它持续引入新功能和改进。 这意味着，特定的 Azure 文件同步代理版本只在有限的时间内受到支持。 为了便于部署, 以下规则确保你有足够的时间和通知, 以便在你的更改管理过程中适应代理更新/升级:

- 至少支持主要代理版本六个月（从初始版本发布日期算起）。
- 我们保证至少提供三个月的缓冲期来支持不同的主要代理版本。 
- 在代理过期之前的至少三个月，我们会在已注册的服务器中使用代理即将过期消息来发出警告。 可以在存储同步服务的“已注册服务器”部分下检查已注册的服务器是否在使用旧版代理。
- 次要代理版本的生存期受限于相关的主要版本。 例如，发布代理版本 3.0 后，代理版本 2.\* 将设置为一起过期。

> [!Note]
> 安装附带过期警告的代理版本时会显示警告，但安装会成功。 不支持且会阻止安装或连接已过期的代理版本。
