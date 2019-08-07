---
title: 开发 Microsoft Azure 上的安全应用程序
description: 本文讨论在你的 web 应用程序项目的实现和验证阶段中要考虑的最佳实践。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 1b2e6e0aa74c06afea09a67dbdf65ca47727b72e
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780563"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上开发安全的应用程序
本文介绍了在开发云应用程序时要考虑的安全活动和控制措施。 介绍了在 Microsoft[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的实施和验证阶段中要考虑的安全问题和概念。 其目标是帮助你定义可用于开发更安全的应用程序的活动和 Azure 服务。

本文介绍了以下 SDL 阶段:

- 实现
- 验证

## <a name="implementation"></a>实现
实现阶段的重点是建立早期预防的最佳实践, 并检测和删除代码中的安全问题。
假设您的应用程序将以您不打算使用的方式使用。 这有助于防止意外或有意地滥用应用程序。

### <a name="perform-code-reviews"></a>执行代码评审

在签入代码之前, 执行[代码评审](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)以提高总体代码质量并降低创建 bug 的风险。 您可以使用[Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts)来管理代码审核过程。

### <a name="perform-static-code-analysis"></a>执行静态代码分析

[静态代码分析](https://www.owasp.org/index.php/Static_Code_Analysis)(也称为*源代码分析*) 通常作为代码评审的一部分执行。 静态代码分析通常是指运行静态代码分析工具, 通过使用[破坏检查](https://en.wikipedia.org/wiki/Taint_checking)和[数据流分析](https://en.wikipedia.org/wiki/Data-flow_analysis)等技术查找非运行代码中的潜在漏洞。

Azure Marketplace 提供[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review), 可执行静态代码分析并协助代码评审。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>验证和净化应用程序的每个输入

将所有输入视为不受信任, 以防止应用程序受到最常见的 web 应用程序漏洞的影响。 不受信任的数据是用于注入攻击的手段。 您的应用程序的输入包括 URL 中的参数、用户输入、数据库中的数据或 API, 以及用户可能会操作的任何传递的内容。 在应用程序以任何方式使用数据 (包括向用户显示数据) 之前, 应用程序应该[验证](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)数据在语法和语义上是有效的。

在数据流初期验证输入, 以确保只有正确格式的数据进入工作流。 不需要在数据库中保留格式错误的数据, 也不会在下游组件中触发故障。

列入方块列表和允许列表是执行输入语法验证的两种常规方法:

  - 列入方块列表尝试检查给定的用户输入是否未包含 "已知为恶意" 内容。

  - 允许列表尝试检查给定的用户输入是否与一组 "已知良好" 的输入匹配。 基于字符的允许列表是允许列表的一种形式, 其中, 应用程序检查用户输入是否只包含 "已知良好" 字符, 或输入是否与已知的格式匹配。
    例如, 这可能涉及到检查用户名只能包含字母数字字符或只包含两个数字。

允许列表是构建安全软件的首选方法。
列入方块列表很容易出现错误, 因为不可能考虑可能不正确的输入的完整列表。

在服务器上执行此操作, 而不是在客户端 (在服务器和客户端上) 执行。

### <a name="verify-your-applications-outputs"></a>验证应用程序的输出

直观或在文档内呈现的任何输出都应始终进行编码和转义。 [转义](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)也称为*输出编码*, 用于帮助确保不受信任的数据不是用于注入攻击的载体。 与数据验证结合使用时, 将提供分层防御机制, 以整体提高系统的安全性。

转义可确保所有内容都显示为*输出。* 还可以通过转义来确定数据不应执行, 这会阻止攻击的正常运行。 这是另一种称为*跨站点脚本*(XSS) 的常见攻击方法。

如果你使用的是第三方的 web 框架, 则可以使用[OWASP XSS 防护](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)备忘单, 验证用于网站上的输出编码的选项。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>在联系数据库时使用参数化查询

不要在代码中 "即时" 创建内联数据库查询, 并将其直接发送到数据库。 插入到应用程序中的恶意代码可能会导致数据库被盗、擦除或修改。 应用程序还可用于在承载数据库的操作系统上运行恶意操作系统命令。

相反, 请使用参数化查询或存储过程。 使用参数化查询时, 可以安全地从代码调用过程并向其传递字符串, 而无需担心会将其视为查询语句的一部分。

### <a name="remove-standard-server-headers"></a>删除标准服务器标头

标头 (如服务器、X-电源) 和 X-AspNet 版本将显示有关服务器和基础技术的信息。 建议取消这些标头以避免对应用程序进行指纹。
请参阅[在 Azure 网站上删除标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>分离生产数据

生产数据 (或 "真实" 数据) 不应用于开发、测试或其他任何目的。 屏蔽 ([匿名](https://en.wikipedia.org/wiki/Data_anonymization)) 数据集应该用于所有开发和测试。

这意味着更少的用户可以访问你的真实数据, 从而减少了你的攻击面。 这也意味着较少的员工会看到个人数据, 从而消除了对机密性的潜在侵害。

### <a name="implement-a-strong-password-policy"></a>实现强密码策略

若要防御暴力破解和基于字典的猜测, 你必须实施强密码策略, 以确保用户创建复杂密码 (例如, 12 个字符的最小长度并要求字母数字和特殊字符)。

您可以使用标识框架来创建和强制实施密码策略。 Azure AD B2C 提供[内置策略](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow)、[自助密码重置](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)等功能, 从而帮助你进行密码管理。

若要防御默认帐户的攻击, 请验证所有密钥和密码是否可替换, 并确保在安装资源后生成或替换它们。

如果应用程序必须自动生成密码, 请确保生成的密码是随机的且具有高熵。

### <a name="validate-file-uploads"></a>验证文件上传

如果你的应用程序允许上[传文件](https://www.owasp.org/index.php/Unrestricted_File_Upload), 请考虑你可以对此风险活动采取一些预防措施。 在许多攻击中, 第一步是在受到攻击的系统中获取一些恶意代码。 使用文件上传可帮助攻击者实现此目的。 OWASP 提供验证文件的解决方案, 以确保要上载的文件是安全的。

反恶意软件防护有助于识别和删除病毒、间谍软件和其他恶意软件。 可安装 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/fundamentals/antimalware)或 Microsoft 合作伙伴的终结点保护解决方案（[Trend Micro](https://www.trendmicro.com/azure/)、[Symantec](https://www.symantec.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 和 [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)）。

[Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/fundamentals/antimalware)包括实时保护、计划扫描、恶意软件修正、签名更新、引擎更新、示例报告以及排除事件收集等功能。 可将 Microsoft 反恶意软件和合作伙伴解决方案与 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)集成，以方便部署和内置检测（警报和事件）。

### <a name="dont-cache-sensitive-content"></a>不缓存敏感内容

不要在浏览器中缓存敏感内容。 浏览器可以存储缓存和历史记录的信息。 在 Internet Explorer 的情况下, 缓存的文件存储在临时 Internet Files 文件夹 (如 Internet Explorer) 中。 再次引用这些页面时, 浏览器将显示其缓存中的页面。 如果向用户显示敏感信息 (地址、信用卡详细信息、社会安全号码、用户名), 则信息可能存储在浏览器缓存中, 并可通过查看浏览器的缓存或只是按浏览器的**后退**按钮。

## <a name="verification"></a>验证
验证阶段涉及到一项全面的工作, 以确保代码满足前面几个阶段中建立的安全和隐私原则。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>查找并修复应用程序依赖项中的漏洞

扫描应用程序及其依赖的库以识别任何已知的漏洞。 可用于执行此扫描的产品包括[OWASP 依赖项检查](https://www.owasp.org/index.php/OWASP_Dependency_Check)、[Snyk](https://snyk.io/)和[黑色](https://www.blackducksoftware.com/)。

通过[Tinfoil security](https://www.tinfoilsecurity.com/)支持的漏洞扫描可用于 Azure App Service Web Apps。 [通过应用服务的 Tinfoil security 扫描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)为开发人员和管理员提供了快速、集成且经济的方法来发现和解决漏洞, 使恶意执行组件可以利用它们。

> [!NOTE]
> 你还可以将[Tinfoil security 与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial)。 将 Tinfoil Security 与 Azure AD 集成可提供以下优势:
>  - 在 Azure AD 中, 可以控制谁有权访问 Tinfoil Security。
>  - 你的用户可以使用其 Azure AD 帐户自动登录到 Tinfoil Security (单一登录)。
>  - 你可以在一个中心位置 (Azure 门户) 管理你的帐户。

### <a name="test-your-application-in-an-operating-state"></a>在运行状态中测试应用程序

动态应用程序安全测试 (DAST) 是一种在操作状态中测试应用程序以查找安全漏洞的过程。 DAST 工具在执行时对程序进行分析, 以找出安全漏洞, 如内存损坏、不安全的服务器配置、跨站点脚本、用户特权问题、SQL 注入和其他关键安全问题。

DAST 不同于静态应用程序安全测试 (SAST)。 当代码未执行时, SAST 工具会分析源代码或编译的代码版本, 以找出安全漏洞。

执行 DAST, 最好是使用安全专业人员 ([渗透测试人员](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)或漏洞取值函数) 的帮助。 如果安全性专业人员不可用, 则可以使用 web 代理扫描程序和一些培训来亲自执行 DAST。 在初期插入 DAST 扫描器, 以确保不会将明显的安全问题引入代码。 有关 web 应用程序漏洞扫描程序的列表, 请参阅[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)站点。

### <a name="perform-fuzz-testing"></a>执行模糊测试

在[模糊测试](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)中, 通过将格式不正确或随机数据引入应用程序来引发程序失败。 引入程序故障有助于在应用程序发布之前泄露潜在的安全问题。

[安全风险检测](https://docs.microsoft.com/security-risk-detection/)是在软件中查找安全关键错误的 Microsoft 独特模糊测试服务。

### <a name="conduct-attack-surface-review"></a>执行攻击面评审

在代码完成后查看攻击面可帮助确保已考虑对应用程序或系统进行的任何设计或实现更改。 这有助于确保已经查看并缓解了由于更改而创建的任何新攻击媒介, 包括威胁模型。

可以通过扫描应用程序来构建攻击面的图片。 Microsoft 提供了一个称为 "[攻击面分析器](https://www.microsoft.com/download/details.aspx?id=24487)" 的攻击面分析工具。 可以从许多商业动态测试和漏洞扫描工具或服务中进行选择, 包括[OWASP Zed 攻击代理项目](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、 [Arachni](http://arachni-scanner.com/)、 [Skipfish](https://code.google.com/p/skipfish/)和[w3af](http://w3af.sourceforge.net/)。 这些扫描工具会对应用进行爬网, 并映射应用程序中可通过 web 访问的部分。 你还可以在 Azure Marketplace 中搜索类似的[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>执行安全渗透测试

确保应用程序的安全与测试任何其他功能一样重要。 将[渗透测试](https://docs.microsoft.com/azure/security/fundamentals/pen-testing)作为生成和部署过程的标准部分。 在部署的应用程序上计划定期安全测试和漏洞扫描, 并监视开放端口、终结点和攻击。

### <a name="run-security-verification-tests"></a>运行安全验证测试

[适用于 Azure 的 Secure DevOps 工具包](https://azsk.azurewebsites.net/index.html)(AzSK) 包含 Azure 平台的多个服务的 SVTs。 定期运行这些 SVTs 以确保 Azure 订阅和构成应用程序的不同资源处于安全状态。 还可以通过使用 AzSK 的持续集成/持续部署 (CI/CD) 扩展功能来自动执行这些测试, 这使得 SVTs 可用作 Visual Studio 扩展。

## <a name="next-steps"></a>后续步骤
在下面的文章中, 我们建议安全控制和活动, 它们可帮助你设计和部署安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [部署安全的应用程序](secure-deploy.md)
