---
title: 开发 Microsoft Azure 上的安全应用程序
description: 本文介绍在你的 web 应用程序项目的实施和验证阶段过程中考虑的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: f4add4bf07178aa616e86f8a64b313630466824f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653261"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上开发安全的应用程序
在本文中，我们介绍的安全活动和用于开发云应用程序时，请考虑控件。 安全问题和概念，可在 Microsoft 的实现和验证阶段过程中考虑[安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)介绍。 旨在帮助您定义活动和可用于开发更安全的应用程序的 Azure 服务。

本文介绍以下 SDL 几个阶段：

- 实现
- 验证

## <a name="implementation"></a>实现
实现阶段的重点是要建立的早期防护最佳做法，并将检测并从代码中删除安全问题。
假定你不想使用的方法，将用于您的应用程序。 这有助于防止意外或故意误用您的应用程序。

### <a name="perform-code-reviews"></a>执行代码评审

签入代码之前，请执行[代码评审](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)提高整体代码质量并减少创建 bug 的风险。 可以使用[Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts)来管理代码审阅过程。

### <a name="perform-static-code-analysis"></a>执行静态代码分析

[静态代码分析](https://www.owasp.org/index.php/Static_Code_Analysis)(也称为*源代码分析*) 通常执行的代码评审的一部分。 静态代码分析通常是指运行静态代码分析工具来使用技术，例如在非运行的代码中查找潜在的漏洞[不检查](https://en.wikipedia.org/wiki/Taint_checking)并[数据流分析](https://en.wikipedia.org/wiki/Data-flow_analysis)。

Azure Marketplace 产品/服务[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)，执行静态代码分析，并帮助进行代码评审。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>验证并审查你的应用程序的每个输入

将所有输入视为不受信任，以从最常见的 web 应用程序漏洞保护应用程序。 不受信任的数据是注入式攻击的工具。 输入你的应用程序包含在 URL 中，用户数据从数据库或从 API 中，输入参数和任何内容传递中的用户可能无法操作。 应用程序应[验证](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)之前应用程序以任何方式 （包括向用户显示） 使用的数据，数据是语法和语义上有效。

验证早期输入数据流中的数据以确保只有正确的数据进入工作流。 您不希望保留在你的数据库或触发下游组件中的出现故障的格式不正确数据。

Blacklisting 和允许列表是执行输入的语法验证的两种常规方法：

  - 将列入方块列表会尝试检查给定的用户输入不包含"已知恶意"的内容。

  - 尝试检查给定的用户输入匹配的"已知的有用"输入一组列入白名单。 基于字符的允许列表是一种形式的允许列表应用程序在其中检查用户输入包含唯一"已知好的"字符或输入匹配的已知的格式。
    例如，这可能涉及检查用户名包含字母数字字符或它包含两个数字。

列入允许列表是用于构建安全软件的首选的方法。
将列入方块列表很容易出现错误，因为它是不可能可以看作是有可能错误输入的完整列表。

完成这项工作在服务器上，不在客户端 （或在服务器上和客户端上）。

### <a name="verify-your-applications-outputs"></a>验证应用程序的输出

应始终编码并转义可提供以可视方式或在文档内的任何输出。 [转义](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)，也称为*输出编码*，用于帮助确保不受信任的数据不会注入攻击的工具。 转义，结合数据验证、 提供分层的防御，从而提高整个系统的安全性。

转义可确保所有内容均显示为*输出。* 转义还允许解释器知道数据并不旨在执行，这样可以防止攻击无法正常工作。 这是名为的另一种攻击手段*跨站点脚本*(XSS)。

如果使用第三方的 web 框架，可以验证输出编码在网站上使用的选项[OWASP XSS 防护备忘单](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>请联系数据库时使用参数化的查询

永远不会在代码中创建动态"的内联数据库查询和其直接发送到数据库。 恶意代码插入到你的应用程序可能会导致你的数据库要被盗时擦除，或修改。 你的应用程序还可以用于承载您的数据库的操作系统上运行恶意操作系统命令。

相反，使用参数化的查询或存储的过程。 当使用参数化的查询时，可以安全地在代码中调用该过程，并将其传递一个字符串，而无需担心它将作为查询语句的一部分进行处理。

### <a name="remove-standard-server-headers"></a>删除标准服务器标头

和标头等服务器，X-提供支持的情况，量 X AspNet 版本显示有关服务器和基础技术的信息。 我们建议取消这些标头避免留下指纹应用程序。
请参阅[删除的 Azure 网站上的标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>生产数据互相分隔开来

不应进行开发、 测试或任何其他业务的预期目的使用你的生产数据或"实际"数据。 掩码 ([进行匿名处理](https://en.wikipedia.org/wiki/Data_anonymization)) 数据集应该是用于所有开发和测试。

这意味着较少的人有权访问实际数据，这样可以减小攻击面。 这还意味着较少的员工，请参阅保密性中消除发送潜在违规情况的个人数据。

### <a name="implement-a-strong-password-policy"></a>实现强密码策略

若要防范暴力破解和基于字典的猜测，必须实现强密码策略，以确保用户创建一个复杂的密码 （例如，12 个字符的最小长度和需要字母数字和特殊字符）。

标识框架可用于创建和强制实施密码策略。 Azure AD B2C 可帮助你使用密码管理，从而[内置策略](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows#create-a-password-reset-user-flow)，[自助服务密码重置](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-sspr)，和的详细信息。

若要防范攻击的默认帐户，验证所有密钥和密码都的可替换，并且它们正在生成或替换后安装的资源。

如果应用程序必须自动生成密码，请确保生成的密码是随机的并具有高熵。

### <a name="validate-file-uploads"></a>验证文件上传

如果你的应用程序允许[文件上传](https://www.owasp.org/index.php/Unrestricted_File_Upload)，请考虑用户可以针对这种风险活动执行的预防措施。 许多攻击的第一步是一些恶意代码进入受攻击的系统。 使用文件上传可帮助实现此目的，攻击者。 OWASP 提供用于验证的文件，确保要上传的文件的安全解决方案。

反恶意软件保护可帮助识别并删除病毒、 间谍软件和其他恶意软件。 可安装 [Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)或 Microsoft 合作伙伴的终结点保护解决方案（[Trend Micro](https://www.trendmicro.com/azure/)、[Symantec](https://www.symantec.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 和 [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection)）。

[Microsoft 反恶意软件](https://docs.microsoft.com/azure/security/azure-security-antimalware)包括实时保护、 计划的扫描、 恶意软件修正、 签名更新、 引擎更新、 示例报告、 和排除事件收集等功能。 可将 Microsoft 反恶意软件和合作伙伴解决方案与 [Azure 安全中心](https://docs.microsoft.com/azure/security-center/security-center-partner-integration)集成，以方便部署和内置检测（警报和事件）。

### <a name="dont-cache-sensitive-content"></a>不缓存敏感内容

不缓存敏感内容的浏览器上。 浏览器可以存储有关缓存和历史记录的信息。 缓存的文件存储在像 Temporary Internet Files 文件夹，在 Internet Explorer 的情况下的文件夹中。 同样，在浏览器时引用这些页面显示从其缓存的页。 如果向用户显示敏感信息 （地址、 信用卡的详细信息、 社会安全号码、 用户名），则信息可能存储在浏览器的缓存中而无法检索的通过检查浏览器的缓存或只需按下浏览器的**返回**按钮。

## <a name="verification"></a>验证
验证阶段包括全面的努力来确保代码满足上述阶段中建立的安全和隐私原则。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>查找并修复应用程序依赖项中的漏洞

扫描您的应用程序和其依赖库来标识任何已知的易受攻击的组件。 可以用来执行此扫描的产品包括[OWASP 依赖关系检查](https://www.owasp.org/index.php/OWASP_Dependency_Check)，[Snyk](https://snyk.io/)，并[Black Duck](https://www.blackducksoftware.com/)。

漏洞扫描为后盾[Tinfoil Security](https://www.tinfoilsecurity.com/)适用于 Azure 应用服务 Web 应用。 [通过应用服务的 tinfoil Security 扫描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)提供开发人员和管理员快速、 集成且经济实惠的方案的发现和解决漏洞之前是恶意行动者可以充分利用这些, 方法。

> [!NOTE]
> 此外可以[将 Tinfoil Security 与 Azure AD 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tinfoil-security-tutorial)。 将 Tinfoil Security 与 Azure AD 集成提供以下优势：
>  - 在 Azure AD 中，可以控制谁有权访问 Tinfoil Security。
>  - 你的用户可以自动登录到 Tinfoil Security （单一登录） 通过使用其 Azure AD 帐户。
>  - 您可以管理你的帐户在一个统一的中心位置，在 Azure 门户中。

### <a name="test-your-application-in-an-operating-state"></a>在正常运行状态中测试应用程序

动态应用程序安全测试 (DAST) 是处于运行状态以查找安全漏洞测试的应用程序的过程。 DAST 工具分析程序执行以查找安全漏洞，如内存损坏、 不安全的服务器配置、 跨站点脚本、 用户权限问题、 SQL 注入和其他关键安全问题时。

DAST 与静态的应用程序安全测试 (SAST) 不同。 SAST 工具分析源代码或为了查找安全漏洞不执行代码时编译的代码版本。

执行 DAST，最好是安全专业人员的帮助 ([渗透测试人员](https://docs.microsoft.com/azure/security/azure-security-pen-testing)或漏洞评估师)。 如果安全专业人员不可用，可以使用 web 代理扫描程序和一些培训自己执行 DAST。 插入 DAST 扫描程序在早期，以确保您看不到你的代码引入显而易见的安全问题。 请参阅[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)有关 web 应用程序漏洞扫描程序的列表的站点。

### <a name="perform-fuzz-testing"></a>执行模糊测试

在中[模糊测试](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)，谨慎地向应用程序引入了格式不正确或随机数据引入程序失败。 将引入程序故障可帮助应用程序发布之前显示潜在的安全问题。

[安全风险检测](https://docs.microsoft.com/security-risk-detection/)是 Microsoft 的唯一模糊测试在软件中查找安全关键的 bug 的服务。

### <a name="conduct-attack-surface-review"></a>执行攻击面检查

查看受攻击面之后代码补全功能可帮助确保任何设计或实现更改为应用程序或已被视为系统。 它有助于确保已查看并缓解由于更改，包括威胁模型，创建任何新攻击手段。

可以通过扫描应用程序生成受攻击面的图片。 Microsoft 提供了攻击面分析工具，称为[Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487)。 您可以从许多商业动态测试和漏洞扫描工具或服务，包括[OWASP Zed 攻击代理项目](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)， [Arachni](http://arachni-scanner.com/)， [Skipfish](https://code.google.com/p/skipfish/)，并[w3af](http://w3af.sourceforge.net/)。 这些扫描工具爬网，您的应用程序，并将映射应用程序通过 web 访问的部分。 您还可以搜索 Azure Marketplace 进行类似[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>执行安全渗透测试

确保应用程序的安全是与测试其他功能一样重要。 请[渗透测试](https://docs.microsoft.com/azure/security/azure-security-pen-testing)标准生成和部署过程的一部分。 计划定期安全测试和漏洞扫描已部署应用程序，并监视打开的端口、 终结点和攻击。

### <a name="run-security-verification-tests"></a>运行安全验证测试

[安全 DevOps 套件，适用于 Azure](https://azsk.azurewebsites.net/index.html) (AzSK) 包含 SVTs 的多个 Azure 平台的服务。 运行这些 SVTs 定期来确保你的 Azure 订阅以及包含应用程序的不同资源处于安全状态。 您可以使用 AzSK，使 SVTs 可用作 Visual Studio 扩展的持续集成/持续部署 (CI/CD) 扩展功能来自动完成这些测试。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议的安全控件和活动，有助于您设计和部署安全的应用程序。

- [设计安全的应用程序](secure-design.md)
- [部署安全的应用程序](secure-deploy.md)
