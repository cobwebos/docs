---
title: Microsoft 安全代码分析文档常见问题解答
description: 通过查看常见问题解答)  (常见问题了解 Microsoft 安全代码分析扩展。
author: sukhans
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
ms.openlocfilehash: f872159e538dc85121a7a6d4d6503fd18a263628
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543038"
---
# <a name="frequently-asked-questions"></a>常见问题
遇到问题？ 请查看以下常见问题解答来了解详细信息。

## <a name="general-faq"></a>常见问题解答

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>能否在我的 Visual Studio Team Foundation Server 实例而不是在 Azure DevOps 实例上安装扩展？

不是。 此扩展不可用于下载和安装 Visual Studio Team Foundation Server。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>是否必须对生成运行 Microsoft 安全代码分析？ 

也许。 这取决于分析工具的类型。 源代码可能是唯一必需的东西，生成输出也可能是必需的。

例如，凭据扫描程序 (CredScan) 会分析代码存储库的文件夹结构中的文件。 由于此分析，你可以运行 CredScan 并在独立的生成中发布安全分析日志生成任务来获取结果。

对于 BinSkim 之类的用来分析生成后项目的其他工具，首先需要的是生成。

### <a name="can-i-break-my-build-when-results-are-found"></a>在找到结果时是否可以中断生成？

是的。 当有任何工具在其日志文件中报告了问题时，你都可以引入生成中断。 只需添加分析后生成任务，并选中要中断生成的任何工具对应的复选框。

在分析后任务的 UI 中，你可以选择在以下两种情况下中断生成：一是工具仅报告错误，二是工具既报告错误又报告警告。

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps 中的命令行参数与独立桌面工具中的参数有何不同？ 

大多数情况下，Azure DevOps 生成任务是有关安全工具的命令行参数的直接包装。 您可以将您通常传递到命令行工具的任何内容作为自变量传递给生成任务。

明显差异：

- 工具将从代理 $ (的源文件夹中运行) 或% BUILD_SOURCESDIRECTORY%。 例如，C:\agent \_ work\1\s。
- 自变量中的路径可以是相对于前面列出的源目录的根目录的相对路径。 路径也可以是绝对路径。 你可以使用 Azure DevOps 生成变量或运行本地代理和本地资源的已知部署位置来获取绝对路径。
- 工具自动提供输出文件路径或文件夹。 如果提供生成任务的输出位置，则会将该位置替换为生成代理上的日志的众所周知位置的路径
- 某些工具的其他命令行参数会被更改。 例如，添加或删除确保没有启动 GUI 的选项。

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>能否在 Azure DevOps 生成中跨多个存储库运行一个生成任务（例如凭据扫描程序）？

不是。 不支持在单个管道中跨多个存储库运行安全开发工具。

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>未创建我指定的输出文件，或者找不到我指定的输出文件

生成任务会筛选某些用户输入。 具体对于此问题而言，它们将生成的输出文件的位置更新为生成代理上的一个通用位置。 有关此位置的详细信息，请参阅以下问题。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>工具生成的输出文件保存在何处？ 

生成任务会自动将输出路径添加到生成代理上的此已知位置：$(Agent.BuildDirectory)\_sdt\logs。 由于我们对此位置进行了标准化，因此所有生成或使用代码分析日志的团队都可以访问此输出。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>是否可以对生成进行排队以便在托管的生成代理上运行这些任务？ 

是的。 此扩展中的所有任务和工具都可以在托管的生成代理上执行。

>[!NOTE]
> 反恶意软件扫描程序生成任务需要启用了 Windows Defender 的生成代理。 托管的 Visual Studio 2017 和更高版本提供了这样的代理。 生成任务不会在 Visual Studio 2015 托管代理上运行。
>
> 尽管不能在这些代理上更新特征，但特征的使用期限应始终小于三个小时。

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>是否可以将这些生成任务作为发布管道的一部分运行，而不是作为生成管道运行？

在大多数情况下为 "是"。

但是，Azure DevOps 不支持在发布管道中运行任务（当这些任务发布项目时）。 缺少此项支持会阻止发布安全分析日志任务在发布管道中成功运行。 该任务会失败，并出现描述性错误消息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>生成任务从何处下载工具？

生成任务可从 [Azure DevOps 包管理源](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)下载工具的 NuGet 包。 生成任务还可以使用必须在生成代理上预先安装的 Node 包管理器。 此类安装的一个示例是 " **npm install tslint**" 命令。

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>在我的 Azure DevOps 组织中安装扩展有什么影响？ 

在其安装过程中，扩展提供的安全生成任务将可供组织中的所有用户使用。 当你创建或编辑 Azure 管道时，"生成-任务集合" 列表中会提供这些任务。 否则，在 Azure DevOps 组织中安装扩展不起作用。 安装不会修改任何帐户设置、项目设置或管道。

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>安装此扩展是否会修改现有 Azure Pipelines？ 

否。 安装此扩展会使安全生成任务可供添加到管道中。 你仍需要添加或更新生成定义，以便这些工具可用于你的生成过程。

## <a name="task-specific-faq"></a>特定于任务的常见问题解答

本部分列出了特定于生成任务的问题。

### <a name="credential-scanner"></a>凭据扫描程序

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>常见的抑制方案和示例有哪些？

下面是两个最常见的抑制方案的详细信息。

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>抑制指定路径中给定机密的所有实例

CredScan 输出文件中机密的哈希键是必需的，如以下示例所示。

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> 哈希键由匹配值或文件内容的一部分生成。 任何源代码修订都可以更改哈希键并禁用抑制规则。

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>抑制指定文件中的所有机密，或抑制机密文件本身

文件表达式可以是文件名。 它还可以是完整文件路径或文件名的基名称部分。 不支持通配符。

下面的示例展示了如何抑制 \<InputPath>\src\JS\lib\angular.js 文件

有效抑制规则的示例：

- \<InputPath>\src\JS\lib\angular.js - 抑制指定路径中的文件
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - 抑制具有相同名称的任何文件

```js
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
```

>[!WARNING] 
> 将来添加到此文件中的任何机密也将被自动抑制。

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>用于管理机密的建议准则有哪些？

以下资源可帮助你安全地管理机密以及从应用程序中访问敏感信息：

 - [Azure 密钥保管库](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Azure AD 托管服务标识 (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure 应用服务和 Azure Functions 中的托管标识](../../app-service/overview-managed-identity.md)
 - [AppAuthentication 库](../../key-vault/general/service-to-service-authentication.md)


有关详细信息，请参阅博客文章：[Managing Secrets Securely in the Cloud](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)（在云中安全地管理机密）。

#### <a name="can-i-write-my-own-custom-searchers"></a>我是否可以编写自己的自定义搜索器？

凭据扫描程序依赖于通常在 buildsearchers.xml 文件中定义的一组内容搜索器。 该文件包含一个 XML 序列化对象数组，代表 **ContentSearcher** 对象。 该程序在分发时附带了一组经过充分测试的搜索器。 但你也可以实现自己的自定义搜索器。

内容搜索器定义如下：

- **名称**：要在凭据扫描程序输出文件中使用的描述性搜索器名称。 建议为搜索器名称使用驼峰式大小写命名约定。
- **RuleId**：搜索器的稳定不透明 ID：
    - 将为凭据扫描程序默认搜索器分配一个 **RuleId** 值，例如 CSCAN0010、CSCAN0020 或 CSCAN0030。 最后一个数字保留用于可能会通过正则表达式 (regex) 对搜索器组进行的合并或划分。
    - 自定义的搜索器的 **RuleId** 值应当具有其自己的命名空间。 例如，CSCAN-\<Namespace\>0010、CSCAN-\<Namespace\>0020 和 CSCAN-\<Namespace\>0030。
    - 完全限定的搜索器名称是 **RuleId** 值和搜索器名称的组合。 例如，CSCAN0010.KeyStoreFiles 和 CSCAN0020.Base64EncodedCertificate。
- **ResourceMatchPattern**：用于针对搜索器进行检查的文件扩展名的正则表达式。
- **ContentSearchPatterns**：一个字符串数组，其中包含要匹配的正则表达式语句。 如果未定义搜索模式，则返回与 **ResourceMatchPattern** 值匹配的所有文件。
- **ContentSearchFilters**：一个包含 regex 语句的字符串数组，用于筛选特定于搜索器的误报。
- **MatchDetails**：要为搜索器的每个匹配项添加的描述性消息和/或缓解说明。
- **建议**：针对匹配项的建议字段内容，使用 PREfast 报告格式。
- **严重性**：一个整数，反映问题的严重性级别。 最高严重性级别的值为 1。

  ![显示了凭据扫描程序设置的 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>使用 Roslyn 分析器任务时的常见错误有哪些？

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>使用错误的 Microsoft.NETCore.App 版本还原了项目

完整错误消息：

“错误:该项目是使用 Microsoft.NETCore.App 版本 x.x.x 还原的，但使用当前设置时，将改用版本 y.y.y。 若要解决此问题，请确保使用相同的设置执行还原和后续操作，例如生成或发布。 通常，如果在生成或发布过程中设置了 RuntimeIdentifier 属性，但在还原过程中未设置此属性，则会出现此问题。”

因为 Roslyn 分析器任务作为编译过程的一部分运行，因此生成计算机上的源树需要处于可生成状态。

介于主生成与 Roslyn 分析器步骤之间的一个步骤可能会将源树置于会阻止生成的状态。 这一额外步骤可能是 **dotnet.exe 发布**。 在即将执行 Roslyn 分析器步骤之前，请尝试重复将执行 NuGet 还原的步骤。 此重复的步骤可以将源树重新置于可生成状态。

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe 无法创建分析器实例

完整错误消息：

“'csc.exe' 已退出并显示了错误代码 1 -- 无法通过 C:\\*BBBB*.dll 创建分析器 *AAAA* 的实例:无法加载文件或程序集 'Microsoft.CodeAnalysis, Version=*X.X.X.X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 或它的某一个依赖项。 系统找不到指定的文件。”

请确保你的编译器支持 Roslyn 分析器。 运行 **csc.exe /version** 命令应当报告版本值 2.6 或更高值。

有时，.csproj 文件可通过引用 Microsoft.Net.Compilers 中的包来替代生成计算机的 Visual Studio 安装。 如果不打算使用编译器的某个特定版本，请删除对 Microsoft.Net.Compilers 的引用。 否则，请确保所引用的包的版本也是 2.6 或更高。

请尝试获取错误日志路径，该路径在 **csc.exe /errorlog** 选项中指定。 此选项和路径显示在 Roslyn 分析器生成任务的日志中。 它们可能类似于 **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# 编译器版本不够新

若要获取最新版本的 C# 编译器，请转到 [Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers)。 若要获取已安装的版本，请在命令提示符下运行 **csc.exe /version**。 确保引用 2.6 版或更高版本的 Microsoft.Net.Compilers NuGet 包。

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>找不到 MSBuild 和 VSBuild 日志

Roslyn 分析器生成任务需要从 MSBuild 生成任务中查询 MSBuild 日志的 Azure DevOps。 如果 analyzer 任务在 MSBuild 任务之后立即运行，则日志仍不可用。 在 MSBuild 任务和 Roslyn 分析器任务之间放置其他任务。 其他任务的示例包括 BinSkim 和反恶意软件扫描程序。

## <a name="next-steps"></a>后续步骤

如果你需要更多帮助，可以在周一到周五的太平洋标准时间上午 9:00 到下午 5:00 联系 Microsoft 安全代码分析支持人员。

- 载入：请参阅我们的 [载入文档](security-code-analysis-onboard.md)
  
- 支持：请通过 [Microsoft 安全代码分析支持](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)向我们的团队发送电子邮件