---
title: 在 Microsoft Azure 上开发安全的应用程序
description: 本文讨论在 Web 应用项目的实现和验证阶段要考虑的最佳做法。
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
ms.openlocfilehash: 7818ae36c785311466d2fb26ce45dcf50983145d
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283480"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上开发安全的应用程序
本文介绍开发云应用程序时要考虑的安全活动和控制措施， 并涵盖 Microsoft [安全开发生命周期 (SDL)](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx) 的实现和验证阶段要考虑的安全问题和概念。 目标在于帮助定义可用于开发更安全的应用程序的活动和 Azure 服务。

本文介绍以下 SDL 阶段：

- 实现
- 验证

## <a name="implementation"></a>实现
实现阶段的重点在于确立早期预防最佳做法，以及从代码中检测和消除安全问题。
假设你的应用程序将以预期外的方式使用。 这有助于防止意外或故意滥用你的应用程序。

### <a name="perform-code-reviews"></a>执行代码评审

在签入代码前，执行[代码评审](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)可提高整体代码质量并降低创建 bug 的风险。 可使用 [Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts) 来管理代码评审过程。

### <a name="perform-static-code-analysis"></a>执行静态代码分析

[静态代码分析](https://owasp.org/www-community/controls/Static_Code_Analysis)（也称为源代码分析）通常作为代码评审的一部分执行。 静态代码分析通常指运行静态代码分析工具，以通过使用[污点检查](https://en.wikipedia.org/wiki/Taint_checking)和[数据流分析](https://en.wikipedia.org/wiki/Data-flow_analysis)等方法来发现未运行代码中的潜在漏洞。

Azure 市场提供[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)，这些工具可以执行静态代码分析并协助进行代码评审。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>验证和清理应用程序的每个输入

将所有输入视为不受信任以保护应用程序，使其免受最常见 Web 应用漏洞的影响。 不受信任的数据是注入攻击的工具。 应用程序的输入包括 URL 中的参数、用户的输入、数据库或 API 中的数据以及用户可能操作的任何传入内容。 在应用程序以任何方式使用数据（包括重新向用户显示数据）前，应用程序应[验证](https://owasp.org/www-project-proactive-controls/v3/en/c5-validate-inputs)数据在语法和语义上是否有效。

在数据流的早期阶段验证输入可确保仅格式正确的数据才能进入工作流。 你不会希望格式不正确的数据保留在数据库中或在下游组件中触发故障。

黑名单和白名单是执行输入语法验证的两种常规方法：

  - 黑名单尝试检查给定用户输入是否包含“已知恶意”的内容。

  - 白名单尝试检查给定用户输入是否与一组“已知良好”的输入匹配。 基于字符的白名单是白名单的一种形式，其中应用程序会检查用户输入是否仅包含“已知良好”的字符或输入是否与已知格式匹配。
    例如，这可能会涉及检查用户名是否仅包含字母数字字符或是否仅包含两个数字。

白名单是用于生成安全软件的首选方法。
黑名单容易出错，因为无法想到潜在错误输入的完整列表。

在服务器上而不是在客户端（或在服务器上和客户端）执行此任务。

### <a name="verify-your-applications-outputs"></a>验证应用程序的输出

以直观方式或在文档中呈现的所有输出始终都应进行编码和转义。 [转义](https://owasp.org/www-community/Injection_Theory#Escaping_.28aka_Output_Encoding.29)（也称为输出编码）用于帮助确保不受信任的数据不会成为注入攻击的工具。 转义与数据验证相结合，可提供分层防御机制，从而整体提高系统的安全性。

转义可确保所有内容都显示为输出。 转义还使解释器知道不应执行数据，因此可防止攻击发生。 这是另一种常见攻击方法，称为跨站点脚本 (XSS)。

如果使用来自第三方的 Web 框架，则可以使用 [OWASP XSS 预防速查表](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)来验证网站上用于输出编码的选项。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>与数据库联系时使用参数化查询

切勿在代码中“动态”创建内联数据库查询并将其直接发送到数据库。 插入到应用程序中的恶意代码可能会导致数据库被盗、擦除或修改。 应用程序也可能被用于在托管数据库的操作系统上运行恶意操作系统命令。

相反，请使用参数化查询或存储过程。 使用参数化查询时，可以安全地从代码中调用过程并向其传递字符串，而无需担心它将被视为查询语句的一部分。

### <a name="remove-standard-server-headers"></a>删除标准服务器标头

Server、X-Powered-By 和 X-AspNet-Version 等标头会透露有关服务器和基础技术的信息。 我们建议取消显示这些标头，以免留下应用程序的指纹。
请参阅[在 Azure 网站中删除标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>隔离生产数据

生产数据（又称为“真实”数据）不应用于开发、测试或业务预期目的以外的任何其他目的。 应将经过屏蔽（[匿名化](https://en.wikipedia.org/wiki/Data_anonymization)）的数据集用于所有开发和测试。

这意味着可访问真实数据的用户会减少，从而缩小攻击面。 这也意味着查看个人数据的员工会减少，从而消除保密方面的潜在威胁。

### <a name="implement-a-strong-password-policy"></a>实现强密码策略

若要防范暴力破解和基于字典的猜测，必须实现强密码策略，以确保用户创建复杂密码（例如，最小长度为 12 个字符且需要包含字母数字字符和特殊字符）。

可以使用标识框架来创建和强制实施密码策略。 Azure AD B2C 通过提供[内置策略](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)、[自助式密码重置](../../active-directory-b2c/user-flow-self-service-password-reset.md)等来帮助进行密码管理。

若要防范针对默认帐户的攻击，请验证所有密钥和密码是否可替换，以及它们是否是在安装资源后生成或被替换。

如果应用程序必须自动生成密码，请确保生成的密码是随机的并具有高熵。

### <a name="validate-file-uploads"></a>验证文件上传

如果应用程序允许[文件上传](https://owasp.org/www-community/vulnerabilities/Unrestricted_File_Upload)，请考虑可针对此风险活动采取的预防措施。 许多攻击的第一步是将一些恶意代码引入受到攻击的系统。 使用文件上传可帮助攻击者完成此任务。 OWASP 提供用于验证文件的解决方案，以确保要上传的文件是安全的。

反恶意软件保护有助于识别和删除病毒、间谍软件及其他恶意软件。 可安装 [Microsoft Antimalware](../fundamentals/antimalware.md) 或 Microsoft 合作伙伴的终结点保护解决方案（[Trend Micro](https://www.trendmicro.com/azure/)、[Broadcom](https://www.broadcom.com/products)、[McAfee](https://www.mcafee.com/us/products.aspx)、[Windows Defender](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) 和 [Endpoint Protection](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)）。

[Microsoft Antimalware](../fundamentals/antimalware.md) 包括实时保护、计划扫描、恶意软件修正、签名更新、引擎更新、示例报告和排除事件收集等功能。 可将 Microsoft 反恶意软件和合作伙伴解决方案与 [Azure 安全中心](../../security-center/security-center-partner-integration.md)集成，以方便部署和内置检测（警报和事件）。

### <a name="dont-cache-sensitive-content"></a>不缓存敏感内容

不要在浏览器中缓存敏感内容。 浏览器可能出于缓存和历史记录的目的存储信息。 缓存的文件存储在某个文件夹中，例如，如果使用 Internet Explorer，该文件夹为“临时 Internet 文件”。 再次浏览这些页面时，浏览器会从缓存显示这些页面。 如果向用户显示了敏感信息（地址、信用卡详细信息、身份证号或用户名），这些信息可能会存储在浏览器的缓存中，并可通过查看浏览器的缓存或直接按下浏览器的“后退”按钮检索到。

## <a name="verification"></a>验证
验证阶段涉及确保代码符合在之前阶段中建立的安全和隐私原则的全面工作。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>查找并修复应用程序依赖项中的漏洞

扫描应用程序及其依赖项库，以识别所有已知的易受攻击的组件。 可用于执行此扫描的产品包括 [OWASP Dependency Check](https://www.owasp.org/index.php/OWASP_Dependency_Check)、[Snyk](https://snyk.io/) 和 [Black Duck](https://www.blackducksoftware.com/)。

由 [Tinfoil Security](https://www.tinfoilsecurity.com/) 提供支持的漏洞扫描功能可用于 Azure 应用服务 Web 应用。 [通过应用服务进行的 Tinfoil Security 扫描](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)为开发人员和管理员提供了一种快速、集成且经济的方式，可在恶意行动者利用漏洞前发现并解决这些漏洞。

> [!NOTE]
> 还可以[将 Tinfoil Security 与 Azure AD 集成](../../active-directory/saas-apps/tinfoil-security-tutorial.md)。 将 Tinfoil Security 与 Azure AD 集成可提供以下优势：
>  - 在 Azure AD 中，可以控制谁有权访问 Tinfoil Security。
>  - 用户可以使用其 Azure AD 帐户自动登录到 Tinfoil Security（单一登录）。
>  - 可在一个中心位置（即 Azure 门户）管理帐户。

### <a name="test-your-application-in-an-operating-state"></a>在运行状态下测试应用程序

动态应用程序安全测试 (DAST) 是在运行状态下测试应用程序以发现安全漏洞的过程。 DAST 工具在程序执行时对其进行分析，以发现安全漏洞（例如内存损坏、不安全的服务器配置、跨站点脚本、用户权限问题、SQL 注入）和其他关键安全问题。

DAST 与静态应用程序安全测试 (SAST) 不同。 SAST 工具在代码未在执行时分析源代码或代码的编译版本，以发现安全漏洞。

最好在安全专业人员（[渗透测试人员](../fundamentals/pen-testing.md)或漏洞评估员）的协助下执行 DAST。 如果没有安全专业人员，则可以在接受一些培训后借助 Web 代理扫描程序自己执行 DAST。 在早期阶段插入 DAST 扫描程序可确保不会在代码中引入明显的安全问题。 有关 Web 应用漏洞扫描程序的列表，请访问 [OWASP](https://owasp.org/www-community/Vulnerability_Scanning_Tools) 站点。

### <a name="perform-fuzz-testing"></a>执行模糊测试

在[模糊测试](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)中，通过有意将格式不正确的数据或随机数据引入应用程序来引发程序故障。 引发程序故障有助于在发布应用程序前揭示潜在的安全问题。

[安全风险检测](https://www.microsoft.com/en-us/security-risk-detection/)是 Microsoft 独有的模糊测试服务，用于发现软件中的安全关键 bug。

### <a name="conduct-attack-surface-review"></a>执行攻击面评审

在代码执行完成后评审攻击面有助于确保已考虑对应用程序或系统进行的任何设计或实现更改。 它有助于确保已评审和缓解由于更改而创建的任何新攻击途径，包括威胁模型。

可通过扫描应用程序来生成攻击面概览。 Microsoft 提供名为 [Attack Surface Analyzer](https://www.microsoft.com/download/details.aspx?id=24487) 的攻击面分析工具。 你可从许多商业动态测试和漏洞扫描工具或服务中进行选择，其中包括 [OWASP Zed Attack Proxy Project](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、[Arachni](http://arachni-scanner.com/)、[Skipfish](https://code.google.com/p/skipfish/) 和 [w3af](http://w3af.sourceforge.net/)。 这些扫描工具可抓取应用并映射可通过 Web 访问的应用程序部分。 你也可以在 Azure 市场中搜索类似的[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>执行安全渗透测试

确保应用程序的安全与测试任何其他功能一样重要。 使[渗透测试](../fundamentals/pen-testing.md)成为生成和部署过程的标准组成部分。 针对已部署的应用程序计划定期安全测试和漏洞扫描，并监视打开的端口、终结点和攻击活动。

### <a name="run-security-verification-tests"></a>运行安全验证测试

[Secure DevOps Kit for Azure](https://azsk.azurewebsites.net/index.html) (AzSK) 包含用于多个 Azure 平台服务的 SVT。 定期运行这些 SVT，以确保 Azure 订阅和组成应用程序的不同资源处于安全状态。 还可以使用 AzSK 的持续集成/持续部署( CI/CD) 扩展功能自动执行这些测试，该功能使 SVT 可以作为 Visual Studio 扩展使用。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们就可以帮助设计和部署安全应用程序的安全控件和活动提供建议。

- [设计安全的应用程序](secure-design.md)
- [部署安全的应用程序](secure-deploy.md)
