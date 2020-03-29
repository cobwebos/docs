---
title: 微软安全代码分析文档常见问题解答
description: 本文包含有关 Microsoft 安全代码分析扩展的常见问题解答
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
ms.openlocfilehash: de76467cc741a65a851e5f3a7ec424d0326aebb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74851513"
---
# <a name="frequently-asked-questions"></a>常见问题
有问题吗？ 有关详细信息，请查看以下常见问题解答。

## <a name="general-faq"></a>一般常见问题解答

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>是否可以在可视化工作室团队基础服务器实例上安装扩展，而不是在 Azure DevOps 实例上安装该扩展？

不是。 该扩展不适用于 Visual Studio 团队基础服务器的下载和安装。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>我是否要使用我的生成运行 Microsoft 安全代码分析？ 

也许。 这取决于分析工具的类型。 源代码可能是唯一需要的东西，或者可能需要生成输出。

例如，凭据扫描程序 （CredScan） 分析代码存储库文件夹结构中的文件。 由于此分析，您可以在独立生成中运行 CredScan 和发布安全分析日志生成任务以获得结果。

对于分析生成后工件的其他工具（如 BinSkim），首先需要生成。

### <a name="can-i-break-my-build-when-results-are-found"></a>找到结果时，是否可以中断我的生成？

是的。 当任何工具报告其日志文件中的问题时，都可以引入生成中断。 只需添加"分析后生成"任务，然后为要中断生成的任何工具选择复选框。

在"分析后"任务的 UI 中，您可以选择在任何工具仅报告错误或同时报告错误和警告时中断生成。

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps 中的命令行参数与独立桌面工具中的这些参数有何不同？ 

在大多数情况下，Azure DevOps 生成任务是围绕安全工具的命令行参数的直接包装。 您可以将通常传递给命令行工具的任何内容作为参数传递给生成任务。

明显差异：

- 工具从代理 $（Build.SourceDirectory） 的源文件夹运行，或从 %BUILD_SOURCESDIRECTORY%。 例如 C：\代理\_工作\1\s。
- 参数中的路径可以相对于以前列出的源目录的根目录。 路径也可以是绝对的。 通过使用 Azure DevOps 生成变量或运行具有本地资源的已知部署位置的本地代理，可以获取绝对路径。
- 工具自动提供输出文件路径或文件夹。 如果为生成任务提供输出位置，则该位置将替换为到生成代理上已知日志位置的路径
- 某些工具将更改一些其他命令行参数。 一个例子是添加或删除确保不启动 GUI 的选项。

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>是否可以在 Azure DevOps 生成中的多个存储库中运行像凭据扫描器这样的生成任务？

不是。 不支持在单个管道中跨多个存储库运行安全开发工具。

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>未创建我指定的输出文件，或者找不到我指定的输出文件

生成任务筛选一些用户输入。 具体对于此问题，它们会将生成的输出文件的位置更新为生成代理上的常见位置。 有关此位置的详细信息，请参阅以下问题。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>工具生成的输出文件保存在何处？ 

生成任务会自动将输出路径添加到生成代理上的此已知位置：$（代理.BuildDirectory）\_sdt_logs。 由于我们在此位置进行标准化，因此生成或使用代码分析日志的所有团队都有权访问输出。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>是否可以对生成进行排队以在托管生成代理上运行这些任务？ 

是的。 扩展中的所有任务和工具都可以在托管生成代理上执行。

>[!NOTE]
> 反恶意软件扫描仪生成任务需要启用 Windows Defender 的生成代理。 托管视觉工作室 2017 和更高版本提供这样的代理。 生成任务不会在 Visual Studio 2015 托管代理上运行。
>
> 尽管无法在这些代理上更新签名，但签名应始终小于三个小时。

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>我可以作为发布管道的一部分而不是生成管道运行这些生成任务吗？

在大多数情况下，是的。

但是，Azure DevOps 不支持在发布管道中运行的任务，因为这些任务发布项目。 这种缺乏支持可防止发布安全分析日志任务在发布管道中成功运行。 任务会失败，并出现描述性错误消息。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>生成任务从何处下载工具？

生成任务可以从[Azure DevOps 包管理源](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)下载工具的 NuGet 包。 生成任务也可以使用节点包管理器，必须在生成代理上预安装节点包管理器。 这种安装的一个示例是命令**npm 安装 tslint**。

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>安装扩展对 Azure DevOps 组织有何影响？ 

安装后，扩展提供的安全生成任务将可供组织中的所有用户使用。 创建或编辑 Azure 管道时，这些任务可从生成任务集合列表中获取。 否则，在 Azure DevOps 组织中安装扩展不起作用。 安装不会修改任何帐户设置、项目设置或管道。

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>安装扩展是否修改了我现有的 Azure 管道？ 

不是。 安装扩展使安全生成任务可用于添加到管道中。 您仍然需要添加或更新生成定义，以便工具可以使用生成过程。

## <a name="task-specific-faq"></a>特定于任务的常见问题解答

本节列出了特定于生成任务的问题。

### <a name="credential-scanner"></a>凭据扫描程序

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>什么是常见的抑制方案和示例？

下面是两个最常见的抑制方案的详细信息。

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>禁止指定路径中给定机密的所有匹配项

CredScan 输出文件中的机密哈希密钥是必需的，如下例所示。

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
> 哈希键由匹配值或文件内容的一部分生成。 任何源代码修订都可以更改哈希键并禁用抑制规则。

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>禁止显示指定文件中的所有机密或禁止机密文件本身

文件表达式可以是文件名。 它也可以是完整文件路径或文件名的基名部分。 不支持通配符。

以下示例演示如何禁止>_src_JS_lib_angular.js 的文件\<输入路径

有效抑制规则的示例：

- \<输入路径>_src_JS_lib_angular.js - 在指定路径中禁止显示文件
- \src_JS_lib_angular.js
- [JS_lib_angular.js
- [lib_angular.js
- angular.js - 禁止任何同名文件

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
> 添加到文件的所有将来机密也将自动被抑制。

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>建议哪些准则来管理机密？

以下资源可帮助您安全地管理机密并从应用程序内访问敏感信息：

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD 托管服务标识 （MSI）](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure 应用服务和 Azure 函数中的托管标识](../../app-service/overview-managed-identity.md)
 - [应用身份验证库](../../key-vault/service-to-service-authentication.md)


有关详细信息，请参阅在[云中安全地管理机密的](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)博客文章。

#### <a name="can-i-write-my-own-custom-searchers"></a>我可以编写自己的自定义搜索器吗？

凭据扫描程序依赖于一组内容搜索程序，这些搜索程序通常在生成搜索器.xml 文件中定义。 该文件包含表示**内容搜索器**对象的 XML 序列化对象数组。 该程序由一组经过良好测试的搜索人员分发。 但是，您也可以实现自己的自定义搜索。

内容搜索器的定义如下：

- **名称**： 要在凭据扫描器输出文件中使用的描述性搜索者名称。 我们建议您对搜索者名称使用骆驼大小写命名约定。
- **规则 Id**： 搜索器的稳定不透明 ID：
    - 凭据扫描程序默认搜索程序被分配为**规则 Id**值，如 CSCAN0010、CSCAN0020 或 CSCAN0030。 最后一个数字保留用于通过正则表达式 （regex） 可能合并或划分搜索者组。
    - 自定义搜索器的**RuleId**值应有自己的命名空间。 示例包括\<CSCAN-\>命名空间 0010、CSCAN-\<命名空间\>0020\<和\>CSCAN- 命名空间 0030。
    - 完全限定的搜索者名称是**RuleId**值和搜索者名称的组合。 示例包括 CSCAN0010。密钥存储文件和 CSCAN0020。基64编码证书。
- **资源匹配模式**：文件扩展名的 Regex，以对照搜索程序进行检查。
- **内容搜索模式**：包含要匹配的正则表达式语句的字符串数组。 如果未定义搜索模式，则返回与**ResourceMatchPattern**值匹配的所有文件。
- **内容搜索筛选器**：包含正则表达式语句的字符串数组，用于筛选特定于搜索器的误报。
- **匹配详细信息**：要为搜索器的每个匹配添加描述性消息、缓解说明或两者。
- **建议**：使用 PREfast 报告格式的匹配的建议字段内容。
- **严重性**：反映问题严重性级别的整数。 最高严重性级别具有值 1。

  ![显示凭据扫描仪设置的 XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn 分析器

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>使用罗斯林分析器任务时的常见错误是什么？

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>该项目是使用错误的 Microsoft.NETCore.App 版本还原的

完整的错误消息：

错误：该项目是使用 Microsoft.NETCore.App 版本*x.x.x*还原的，但使用当前设置时，将使用版本*y.y.y。* 若要解决此问题，请确保将相同的设置用于 restore 和后续操作，例如 build 或 publish。 如果在 build 或 publish 期间设置了 RuntimeIdentifier 属性，而没有在 restore 过程中设置，通常就会出现此问题。”

由于 Roslyn 分析器任务作为编译的一部分运行，因此生成计算机上的源树需要处于可构建状态。

主生成和 Roslyn 分析器步骤之间的步骤可能使源树处于阻止生成的状态。 此额外步骤可能是**dotnet.exe 发布**。 尝试复制在 Roslyn 分析器步骤之前执行 NuGet 还原的步骤。 此重复步骤可能会使源树重新处于可构建状态。

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe 无法创建分析器实例

完整的错误消息：

"'csc.exe'退出与错误代码 1 - 分析器*AAAA*的实例不能从\\C：*BBBB*.dll ： 无法加载文件或程序集 'Microsoft.代码分析， 版本=*X.X.X.X，* 文化=中性， PublicKeyToken_31bf3856ad364e35' 或其依赖项之一。 系统找不到指定的文件。”

确保您的编译器支持罗斯林分析器。 运行命令**csc.exe /version**应报告版本值 2.6 或更高版本。

有时 .csproj 文件可以通过引用 Microsoft.Net.编译器的包来覆盖生成计算机的 Visual Studio 安装。 如果您不打算使用编译器的特定版本，请删除对 Microsoft.Net.编译器的引用。 否则，请确保引用的包的版本也是 2.6 或更高版本。

尝试获取错误日志路径，该路径在**csc.exe /错误日志**选项中指定。 选项和路径显示在 Roslyn 分析器生成任务的日志中。 它们可能类似于 **/错误日志：F：\ts-服务-123\_工作\456\s\一些\项目\代码.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# 编译器版本不够新

要获取最新版本的 C# 编译器，请访问[Microsoft.Net.编译器](https://www.nuget.org/packages/Microsoft.Net.Compilers)。 要获取已安装的版本，请以命令提示符运行**csc.exe /version。** 请确保引用 Microsoft.Net.编译器 NuGet 版本 2.6 或更高版本的数据包。

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>找不到 MSBuild 和 VSBuild 日志

Roslyn 分析器生成任务需要查询 Azure DevOps 从 MSBuild 生成任务中查询 MSBuild 日志。 如果分析器任务在 MSBuild 任务之后立即运行，则日志将不可用。 在 MSBuild 任务和罗斯林分析器任务之间放置其他任务。 其他任务的示例包括 BinSkim 和反恶意软件扫描程序。

## <a name="next-steps"></a>后续步骤

如果您需要其他帮助，Microsoft 安全代码分析支持可于周一至周五上午 9：00 至下午 5：00 太平洋标准时间提供。

- 入职：请参阅我们的[入职文档](security-code-analysis-onboard.md)
  
- 支持：在 Microsoft[安全代码分析支持](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)中向我们的团队发送电子邮件