---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/11/2018
ms.author: tamram
ms.openlocfilehash: 9b8812b1fca6a72a69f06a6c0278da8ee4d4c852
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841436"
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
4. **下载最新 Azure 文件同步代理安装程序从[Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257)。**  
    若要升级现有的 Azure 文件同步代理安装，请卸载旧版本，然后使用下载的安装程序安装最新版本。 服务器注册、同步组和其他任何设置由 Azure 文件同步安装程序维护。

#### <a name="automatic-agent-lifecycle-management"></a>自动代理生命周期管理
代理版本 6，文件同步团队引入了代理自动升级功能。 可以选择两种模式之一，并在服务器上指定应在其中尝试升级的维护时段。 此功能旨在帮助您通过提供阻止从过期代理护代理生命周期管理或无障碍，从而允许保留当前设置。
1. **默认设置**将尝试阻止从过期的代理。 在 21 天的代理的已发布的到期日期，代理将尝试自我升级。 它将开始尝试将每周一次在过期之前和在所选的维护时段的 21 天内升级。 **此选项不会消除花常规 Microsoft 更新修补程序的需要。**
2. （可选） 选择，代理将自动升级本身就立即推出新的代理版本 （当前不适用于群集服务器）。 此更新将在选定的维护时段内发生，并允许你的服务器以利用新功能和改进，只要它们已公开发布。 这是主要代理版本，以及定期更新到服务器的修补程序将提供的建议，无需担心的设置。 发布每个代理是在 GA 质量。 即使你选择自动更新时的新版本将成为可用的选项，你可能不会获得更新发布后立即。 新的代理程序最初提供给少量的服务器，然后我们逐步展开产品/服务。 外部测试完成后，代理还会提供 Microsoft Update 并[Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257)。

#### <a name="agent-lifecycle-and-change-management-guarantees"></a>代理生命周期和变更管理保证
Azure 文件同步是一个云服务，它不断引入了新功能和改进。 这意味着，特定的 Azure 文件同步代理版本只在有限的时间内受到支持。 为了便于您的部署，以下规则保证有足够的时间和通知，以适应代理更新/升级您的更改管理流程：

- 至少支持主要代理版本六个月（从初始版本发布日期算起）。
- 我们保证至少提供三个月的缓冲期来支持不同的主要代理版本。 
- 在代理过期之前的至少三个月，我们会在已注册的服务器中使用代理即将过期消息来发出警告。 可以在存储同步服务的“已注册服务器”部分下检查已注册的服务器是否在使用旧版代理。
- 次要代理版本的生存期受限于相关的主要版本。 例如，发布代理版本 3.0 后，代理版本 2.\* 将设置为一起过期。

> [!Note]
> 安装附带过期警告的代理版本时会显示警告，但安装会成功。 不支持且会阻止安装或连接已过期的代理版本。
