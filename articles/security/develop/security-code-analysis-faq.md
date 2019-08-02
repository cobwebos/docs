---
title: Microsoft Azure 安全代码分析文档常见问题
description: 本文包含有关安全代码分析扩展的常见问题解答
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 1fc5e83c2c46a7da2a4b56879a2d596405fd8bee
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718309"
---
# <a name="frequently-asked-questions"></a>常见问题
有问题？ 有关详细信息, 请查看下面的常见问题解答。

## <a name="general-faqs"></a>一般常见问题解答

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>能否在 TFS (而非 Azure DevOps) 服务器上安装扩展？ 

不可以, 此扩展不能用于下载和安装 TFS。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>我是否必须在生成时运行 Microsoft 安全代码分析？ 

是和否。 根据分析工具的类型, 源代码本身可能只是所需的, 也可能需要生成的输出。 例如, 由于凭据扫描程序在代码存储库的文件夹结构内分析文件, 因此你可以运行凭据扫描器并在独立版本中发布安全分析日志生成任务以检索结果。
对于分析后生成项目 (如 BinSkim) 的其他工具, 首先需要生成。

### <a name="can-i-break-my-build-when-results-are-found"></a>能否在发现结果时中断生成？ 
是的, 可以在任何工具在其日志文件中报告问题时引入生成中断。 只需添加后期分析生成任务, 并选中想要中断生成的任何工具的复选框即可。 如果任何工具在分析后任务的 UI 中报告错误或警告和错误, 则可以选择中断生成。

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>在 Azure DevOps 中, 命令行参数与在独立桌面工具中的不同之处是什么？ 

大多数情况下, Azure DevOps 生成任务是有关安全工具的命令行参数的直接包装。 通常情况下, 你可以从桌面的命令行将任何内容传递到该工具, 你可以将传递给生成任务的参数输入。
下面是明显差异的列表:
 - 将从代理 $ (Build.sourcesdirectory) 或% BUILD_SOURCESDIRECTORY% 的源文件夹执行该工具。 例如：C:\agent\_work\1\s 
 - 自变量中的路径可以是相对于上面列出的源目录的根目录的相对路径, 也可以通过运行具有本地资源的已知部署位置或使用 Azure DevOps 生成变量的本地代理来确定。
 - 如果提供了输出路径, 则工具将自动提供输出文件路径或文件夹, 并将其替换为生成代理上已知日志位置的路径
 - 一些其他命令行参数在某些工具上进行净化和删除, 例如添加或删除选项, 以确保不会启动 GUI。

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>能否在 Azure DevOps Build 中跨多个存储库运行生成任务 (例如, 凭据扫描器)？ 

不能, 目前不支持对单个管道中的多个存储库运行安全开发工具。

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>未创建指定的输出文件/找不到指定的输出文件 

生成任务当前净化用户输入, 并将生成的输出文件的位置更新为生成代理上的常见位置。 有关此位置的详细信息, 请参阅以下问题。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>工具所生成的输出文件保存在何处？ 

生成任务自动将输出路径添加到生成代理 $ (agent.builddirectory)\_sdt\logs. 上的以下已知位置。 通过在此位置上实现标准化, 我们可以确保生成代码分析日志或使用它们的其他团队具有访问权限。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>是否可以对生成进行排队以便在托管生成代理上运行这些任务？ 

是的, 可以在托管生成代理上执行扩展中的所有任务和工具。

>[!NOTE]
> 反恶意软件生成任务需要启用 Windows Defender 的生成代理, 该代理在 "托管 VS2017" 或更高版本的生成代理上为 true。 (不会在旧版/VS2015 的 "托管" 代理上运行。)无法在这些代理上更新签名, 但签名应始终相对较新, 不能超过3小时。
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>是否可以将这些生成任务作为发布管道的一部分运行 (而不是生成管道)？ 
在大多数情况下为 "是"。 但是, Azure DevOps 不支持发布项目的任务, 而不支持在发布管道中运行这些任务:"不应与发布一起使用的任务的唯一类别是发布项目的任务。 这是因为, 目前, 我们不支持在发布中发布项目。
这会阻止 "发布安全分析日志" 任务从发布管道成功运行;它将失败, 并出现描述性错误消息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>生成任务从何处下载工具？ 
生成任务 a) 从以下[Azure DevOps 包管理源](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)或使用节点包管理器 (例如: "npm install tslint") 下载以下工具的 NuGet 包。

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>在我的 Azure DevOps 组织中安装此扩展的影响是什么？ 

安装后, 扩展提供的安全生成任务将可供组织中的所有用户使用。 创建或编辑 Azure 管道时, 将可以从 "生成任务集合" 列表中添加这些任务。 否则, 在 Azure DevOps 组织中安装扩展不会产生任何影响。 它不会修改任何帐户、项目设置或管道。

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>将安装此扩展是否会修改现有 Azure Pipelines？ 

否。 安装扩展将使安全生成任务可添加到 Azure Pipelines。 用户仍需添加或更新生成定义以将这些工具集成到生成过程中。

## <a name="task-specific-faqs"></a>任务特定常见问题

本部分将列出特定于生成任务的常见问题解答。

### <a name="credential-scanner-faqs"></a>凭据扫描器常见问题

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>常见的禁止显示方案和示例有哪些？ 
下面详细介绍了两种最常见的抑制方案:
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>禁止在指定路径中出现的所有给定机密 
需要凭据扫描器输出文件中机密的哈希键, 如以下示例中所示
   
        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> 哈希键由匹配值或文件内容的一部分生成。 任何源代码修订版本都可以更改哈希键并禁用禁止显示规则。 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>禁止显示指定文件中的所有机密 (或禁止显示机密文件本身) 
文件表达式可以是文件名, 也可以是完整文件路径/名称的任何后缀部分。 不支持通配符。 

**示例** 

要取消的文件: [InputPath] \src\JS\lib\angular.js 

有效的抑制规则: 
- [InputPath] \src\JS\lib\angular.js--取消指定路径中的文件
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- node.js--禁止显示名称相同的任何文件

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> 添加到该文件中的所有未来机密也会自动被取消。 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>建议使用哪些机密管理指导原则？ 
尽管及时检测硬编码的机密并降低风险非常有用, 但如果有可能导致机密无法完全签入, 则更是如此。 在这方面, Microsoft 已将 CredScan 代码分析器作为[Microsoft DevLabs extension](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio) For Visual Studio 的一部分发布。 在早期预览版中, 它为开发人员提供了在其代码中检测潜在机密的内嵌体验, 使他们能够实时解决这些问题。 有关详细信息, 请参阅[此](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)博客, 了解如何安全地在云中管理机密。 下面是一些其他资源, 可帮助你以安全的方式管理机密并访问应用程序内的敏感信息: 
 - [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/)
 - [Azure Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)
 - [Azure AD 托管服务标识](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 资源的托管服务标识 (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)
 - [Azure 托管服务标识](https://docs.microsoft.com/azure/app-service/overview-managed-identity)
 - [Microsoft.azure.services.appauthentication 库](https://docs.microsoft.com/azure/key-vault/service-to-service-authentication)

#### <a name="can-i-write-my-own-custom-searchers"></a>我是否可以编写自己的自定义 searchers？

Credential 扫描器依赖于一组通常在**buildsearchers**文件中定义的内容 searchers。 文件包含表示 ContentSearcher 对象的 XML 序列化对象的数组。 该程序与一组经过良好测试的 searchers 一起分发, 但它也允许实现您自己的自定义 searchers。 

内容搜索者定义如下: 

- **Name** –要在凭据扫描器输出文件中使用的描述性搜索程序名称。 建议为搜索程序名称使用 camel 大小写命名约定。 
- **RuleId** -搜索者的稳定不透明 ID。 
    - 凭据扫描器默认 searchers 通过 RuleIds (如 CSCAN0010、CSCAN0020、CSCAN0030 等) 进行分配。为潜在的搜索表达式组合并或除法保留最后一个数字。
    - 自定义的 searchers 的 RuleId 的命名空间应为以下格式:CSCAN-{Namespace} 0010、CSCAN-{Namespace} 0020、CSCAN-{Namespace} 0030 等。
    - 完全限定的搜索者名称是 RuleId 与搜索者名称的组合。 示例 CSCAN0010。KeyStoreFiles, CSCAN0020.Base64EncodedCertificate 等。
- **ResourceMatchPattern** –用于对照搜索者检查的文件扩展名的正则表达式
- **ContentSearchPatterns** –包含要匹配的正则表达式语句的字符串数组。 如果未定义搜索模式, 则将返回与资源匹配模式匹配的所有文件。
- **ContentSearchFilters** –包含 Regex 语句的字符串数组, 用于筛选搜索器特定误报。
- **Matchdetails** -要为每个搜索匹配项添加的描述性消息和/或缓解说明。
- **建议**–使用 PREfast 报表格式为匹配项提供建议字段内容。
- **严重性**–一个整数, 用于反映问题的严重性 (最高 = 1)。
![凭据扫描程序设置](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Roslyn 分析器常见问题

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>使用 Roslyn 分析器任务时最常见的错误是什么？

**错误：使用 NETCore 版本1.x 还原了项目, 但使用当前设置时, 将改用版本为 y 的版本。若要解决此问题, 请确保使用相同的设置进行还原, 并进行后续操作, 例如生成或发布。通常, 如果在生成或发布期间设置 Runtimeidentifiers 属性, 但在还原过程中未设置此属性, 则会出现此问题:**

Roslyn 分析器作为编译的一部分运行, 因此生成计算机上的源树需要处于可生成状态。 主生成和 Roslyn 分析器之间的步骤 (可能是 "dotnet 发布") 可能使源树处于 unbuildable 状态。 或许在 Roslyn 分析器步骤之前复制执行 Nuget 还原的步骤会将源树置于可生成状态。

**"csc" 已退出, 错误代码为 1--无法从 C:\BBBB.dll 创建分析器 AAAA 的实例:未能加载文件或程序集 "CodeAnalysis, Version = X. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x系统找不到指定的文件。**

确保编译器支持 Roslyn 分析器。 "csc/version" 至少应报告6.x 版。 在某些情况下, 单个 .csproj 文件可通过从 Microsoft.Net 引用包来替代生成计算机的 Visual Studio 安装。 如果不需要使用特定版本的编译器, 请删除对 Microsoft.Net 的引用。 否则, 请确保引用的包也至少为6.x 版。 尝试获取错误日志, 该日志可在 csc 命令行 (在 Roslyn 生成任务的日志中找到) 的/errorlog: 参数中找到。 它可能类似于:/errorlog: f:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif

**编译器C#最近没有足够的空间 (它必须 > = 2.6)**

最新版本的C#编译器在此处发布:。 https://www.nuget.org/packages/Microsoft.Net.Compilers 若要获取已安装的版本, 请`C:\>csc.exe /version`在命令提示符下运行。 请确保没有任何对 Microsoft.Net NuGet 包的引用 < 2.6。

**找不到 MSBuild/VSBuild 日志**

由于任务的工作方式, 此任务需要在 Azure DevOps 中查询 MSBuild 生成任务中的 MSBuild 日志。 如果此任务在 MSBuild 生成任务之后立即运行, 则日志将无法使用;在 MSBuild 生成任务和 Roslyn 分析器生成任务之间放置其他生成任务, 包括 SecDevTools 生成任务, 如 Binskim、反恶意软件扫描和其他任务。 

## <a name="next-steps"></a>后续步骤

如果需要更多帮助, 可在太平洋标准时间星期一至星期五 (太平洋标准时间上午 9:00-5:00 太平洋时间) 提供 Microsoft 安全代码分析支持

  - 载入-请联系你的技术客户经理, 开始学习。 
  >[!NOTE] 
  >如果你尚未与 Microsoft 进行付费支持关系, 或者如果你的支持产品不允许你从 Phoenix 目录购买服务, 请访问我们的[支持服务主页](https://www.microsoft.com/enterprise/services/support)以获取详细信息。

  - 支持-通过[Microsoft 安全代码分析支持](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)团队向团队发送电子邮件