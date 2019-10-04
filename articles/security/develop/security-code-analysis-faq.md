---
title: Microsoft 安全代码分析文档常见问题
description: 本文包含有关 Microsoft 安全代码分析扩展的常见问题
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
ms.openlocfilehash: b28d02dd0ca375451f6ff75b1253ae8874bf2ab4
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828252"
---
# <a name="frequently-asked-questions"></a>常见问题
有问题？ 有关详细信息，请查看以下常见问题解答。

## <a name="general-faq"></a>一般常见问题解答

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>能否在我的 Visual Studio Team Foundation Server 实例而不是在 Azure DevOps 实例上安装扩展？

否。 此扩展不可用于下载和安装 Visual Studio Team Foundation Server。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>我是否必须在生成时运行 Microsoft 安全代码分析？ 

也许。 这取决于分析工具的类型。 源代码可能是唯一需要的，也可能是必需的生成输出。

例如，Credential 扫描器（CredScan）会分析代码存储库的文件夹结构中的文件。 由于此分析，可以运行 CredScan 并在独立版本中发布安全分析日志生成任务以获取结果。

对于分析后生成项目的其他工具（如 BinSkim），首先需要生成。

### <a name="can-i-break-my-build-when-results-are-found"></a>能否在发现结果时中断生成？

是。 如果任何工具在其日志文件中报告了问题或问题，则可以引入生成中断。 只需添加后期分析生成任务，并选中要为其中断生成的任何工具的复选框。

在分析后任务的 UI 中，你可以选择在任何工具仅报告错误或同时报告错误和警告时中断生成。

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps 中的命令行参数与独立桌面工具中的参数有何不同？ 

大多数情况下，Azure DevOps 生成任务是有关安全工具的命令行参数的直接包装。 您可以将您通常传递到命令行工具的任何内容作为自变量传递给生成任务。

明显差异：

- 从代理 $ （Build.sourcesdirectory）或% BUILD_SOURCESDIRECTORY% 的源文件夹运行的工具。 例如，C:\agent @ no__t-0work\1\s。
- 自变量中的路径可以是相对于前面列出的源目录的根目录的相对路径。 路径也可以是绝对路径。 你可以使用 Azure DevOps 生成变量或运行本地代理和本地资源的已知部署位置来获取绝对路径。
- 工具自动提供输出文件路径或文件夹。 如果提供生成任务的输出位置，则会将该位置替换为生成代理上的日志的众所周知位置的路径
- 某些工具的其他命令行参数会被更改。 例如，添加或删除确保没有启动 GUI 的选项。

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>能否在 Azure DevOps 生成中跨多个存储库运行一个生成任务（例如凭据扫描程序）？

否。 不支持在单个管道中跨多个存储库运行安全开发工具。

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>未创建指定的输出文件，或者找不到指定的输出文件

生成任务筛选部分用户输入。 具体而言，它们将生成的输出文件的位置更新为生成代理上的常见位置。 有关此位置的详细信息，请参阅以下问题。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>工具所生成的输出文件保存在何处？ 

生成任务自动将输出路径添加到生成代理上的此已知位置： $ （Agent.builddirectory） \_sdt \ logs。 由于我们将在此位置上标准化，因此生成或使用代码分析日志的所有团队都可以访问输出。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>是否可以对生成进行排队以便在托管生成代理上运行这些任务？ 

是。 扩展中的所有任务和工具都可以在托管生成代理上执行。

>[!NOTE]
> 反恶意软件扫描程序生成任务需要启用了 Windows Defender 的生成代理。 托管的 Visual Studio 2017 和更高版本提供此类代理。 生成任务不会在 Visual Studio 2015 托管代理上运行。
>
> 尽管不能在这些代理上更新签名，但签名应始终小于三个小时。

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>是否可以将这些生成任务作为发布管道的一部分运行，而不是作为生成管道运行？

在大多数情况下为 "是"。

但是，Azure DevOps 不支持在发布管道中运行任务（当这些任务发布项目时）。 缺少此项支持会阻止发布安全分析日志任务在发布管道中成功运行。 该任务会失败，并出现描述性错误消息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>生成任务从何处下载工具？

生成任务可从[Azure DevOps 包管理源](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)下载工具的 NuGet 包。 生成任务还可以使用必须在生成代理上预先安装的 Node 包管理器。 此类安装的一个示例是 " **npm install tslint**" 命令。

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>在我的 Azure DevOps 组织中安装扩展有什么影响？ 

在其安装过程中，扩展提供的安全生成任务将可供组织中的所有用户使用。 当你创建或编辑 Azure 管道时，"生成-任务集合" 列表中会提供这些任务。 否则，在 Azure DevOps 组织中安装扩展不起作用。 安装不会修改任何帐户设置、项目设置或管道。

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>安装扩展是否会修改现有的 Azure 管道？ 

否。 安装扩展会使安全生成任务可用于添加到管道。 你仍需要添加或更新生成定义，以便这些工具可用于你的生成过程。

## <a name="task-specific-faq"></a>任务特定的常见问题

本部分列出了特定于生成任务的问题。

### <a name="credential-scanner"></a>凭据扫描器

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>常见的抑制方案和示例有哪些？

下面是两个最常见的抑制方案的详细信息。

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>禁止显示指定路径中的所有给定机密

需要 CredScan 输出文件中机密的哈希键，如以下示例中所示。

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

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>禁止显示指定文件中的所有机密，或禁止显示机密文件本身

文件表达式可以是文件名。 它也可以是完整文件路径或文件名的 basename 部分。 不支持通配符。

下面的示例演示如何取消 \<InputPath > \src\JS\lib\angular.js 的文件。

有效抑制规则的示例：

- \<InputPath > \src\JS\lib\angular.js-取消指定路径中的文件
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- node.js-禁止名称相同的任何文件

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
> 添加到该文件中的所有未来机密也将自动取消。

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>什么是用于管理机密的推荐指导原则？

以下资源可帮助你安全地管理机密，并从应用程序中访问敏感信息：

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD 托管服务标识（MSI）](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure App Service 和 Azure Functions 中的托管标识](../../app-service/overview-managed-identity.md)
 - [Microsoft.azure.services.appauthentication 库](../../key-vault/service-to-service-authentication.md)


有关详细信息，请参阅博客文章[在云中安全管理机密](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)。

#### <a name="can-i-write-my-own-custom-searchers"></a>我是否可以编写自己的自定义 searchers？

Credential 扫描器依赖于一组通常在 buildsearchers 文件中定义的内容 searchers。 文件包含表示**ContentSearcher**对象的 XML 序列化对象的数组。 该程序与一组经过充分测试的 searchers 一起分发。 但也可以实现您自己的自定义 searchers。

内容搜索者定义如下：

- **名称**：要在 Credential 扫描器输出文件中使用的描述性搜索程序名称。 建议为搜索程序名称使用 camel 大小写命名约定。
- **RuleId**：搜索者的稳定不透明 ID：
    - 为凭据扫描器默认搜索者分配了**RuleId**值，如 CSCAN0010、CSCAN0020 或 CSCAN0030。 最后一个数字保留用于通过正则表达式（regex）合并或划分搜索程序组。
    - 自定义搜索程序的**RuleId**值应具有其自己的命名空间。 示例包括 CSCAN-\<Namespace @ no__t-10010、CSCAN \<Namespace @ no__t-30020 和 @no__t CSCAN-4Namespace @ no__t-50030。
    - 完全限定的搜索者名称是**RuleId**值和搜索者名称的组合。 例如，CSCAN0010。KeyStoreFiles 和 CSCAN0020。Base64EncodedCertificate.
- **ResourceMatchPattern**：用于对照搜索者检查的文件扩展名的正则表达式。
- **ContentSearchPatterns**：一个字符串数组，其中包含要匹配的正则表达式语句。 如果未定义搜索模式，则返回与**ResourceMatchPattern**值匹配的所有文件。
- **ContentSearchFilters**：一个包含 regex 语句的字符串数组，用于筛选搜索器特定的误报。
- **MatchDetails**：为每个搜索匹配项添加的描述性消息、缓解说明或同时添加这两者。
- **建议**：使用 PREfast 报表格式的匹配项的建议字段内容。
- **严重性**：一个整数，反映问题的严重级别。 最高严重性级别的值为1。

  ![显示 Credential 扫描器安装程序的 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>使用 Roslyn 分析器任务时的常见错误是什么？

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>使用错误的 NETCore 版本还原了项目

完整的错误消息：

“错误:使用*NETCore 版本 1.x*还原了项目，但使用当前设置时，将*改用版本为 y 的*版本。 若要解决此问题，请确保使用相同的设置进行还原，并进行后续操作，例如生成或发布。 通常，如果在生成或发布过程中设置了 Runtimeidentifiers 属性，但在还原过程中未设置此属性，则会出现此问题。

由于 Roslyn 分析器任务作为编译的一部分运行，因此生成计算机上的源树需要处于可生成状态。

主生成和 Roslyn 分析器步骤之间的步骤可能会将源树置于阻止生成的状态。 这一额外步骤可能**dotnet 发布**。 尝试复制在 Roslyn 分析器步骤之前执行 NuGet 还原的步骤。 此重复步骤可能使源树处于可生成状态。

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc 无法创建分析器实例

完整的错误消息：

"csc" 已退出，错误代码为 1--无法从 C： \\*BBBB*创建分析器*AAAA*的实例：未能加载文件或程序集 "CodeAnalysis，Version =*x*. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x. x 系统找不到指定的文件。”

确保编译器支持 Roslyn 分析器。 运行命令**csc/version**应报告版本值2.6 或更高版本。

有时，.csproj 文件可通过从 Microsoft.Net 引用包来重写生成计算机的 Visual Studio 安装。 如果不打算使用特定版本的编译器，请删除对 Microsoft.Net 的引用。 否则，请确保引用包的版本也为2.6 或更高版本。

尝试获取在**csc/errorlog**选项中指定的错误日志路径。 选项和路径显示在 Roslyn 分析器生成任务的日志中。 它们可能类似于 **/errorlog： F:\ts-services-123 @ no__t-1work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C#编译器版本不是最新版本

若要获取最新版本的C#编译器，请转到[Microsoft.Net](https://www.nuget.org/packages/Microsoft.Net.Compilers)。 若要获取已安装的版本，请在命令提示符下运行 **/version** 。 确保引用的是版本2.6 或更高版本的 Microsoft.Net NuGet 包。

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>找不到 MSBuild 和 VSBuild 日志

Roslyn 分析器生成任务需要从 MSBuild 生成任务中查询 MSBuild 日志的 Azure DevOps。 如果 analyzer 任务在 MSBuild 任务之后立即运行，则日志仍不可用。 在 MSBuild 任务和 Roslyn 分析器任务之间放置其他任务。 其他任务的示例包括 BinSkim 和反恶意软件扫描程序。

## <a name="next-steps"></a>后续步骤

如果需要更多帮助，Microsoft 安全代码分析支持将于太平洋标准时间上午9:00 到晚上5:00 的星期五提供。

  - 登记：请与你的技术客户经理联系，开始。
  
  - 支持通过电子邮件将我们的团队发送到[Microsoft 安全代码分析支持](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)。

  >[!NOTE] 
  >你可能没有与 Microsoft 的付费支持关系。 或者，你可能有一个支持产品，它阻止你从 Phoenix 目录购买服务。 如果满足上述任一条件，请访问我们的[支持服务主页](https://www.microsoft.com/enterprise/services/support)获取详细信息。
