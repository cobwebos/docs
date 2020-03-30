---
title: 在 Microsoft Azure 上开发安全应用程序
description: 本文讨论了在 Web 应用程序项目的实现和验证阶段需要考虑的最佳做法。
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
ms.openlocfilehash: 9d98660230e0ab9f4edcd9a7af8a3797106dd17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255660"
---
# <a name="develop-secure-applications-on-azure"></a>在 Azure 上开发安全的应用程序
在本文中，我们将介绍开发云应用程序时需要考虑的安全活动和控制措施。 介绍了在 Microsoft[安全开发生命周期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)实施和验证阶段需要考虑的安全问题和概念。 目标是帮助您定义可用于开发更安全应用程序的活动和 Azure 服务。

本文将介绍以下 SDL 阶段：

- 实现
- 验证

## <a name="implementation"></a>实现
实施阶段的重点是建立早期预防的最佳做法，并从代码中发现和删除安全问题。
假设您的应用程序将以您不希望使用的方式使用。 这有助于您防止意外或故意误用您的应用程序。

### <a name="perform-code-reviews"></a>执行代码评审

在签入代码之前，请执行[代码评审](https://docs.microsoft.com/azure/devops/learn/devops-at-microsoft/code-reviews-not-primarily-finding-bugs)以提高总体代码质量并降低创建 Bug 的风险。 您可以使用[Visual Studio](https://docs.microsoft.com/azure/devops/repos/tfvc/get-code-reviewed-vs?view=vsts)来管理代码评审过程。

### <a name="perform-static-code-analysis"></a>执行静态代码分析

[静态代码分析](https://www.owasp.org/index.php/Static_Code_Analysis)（也称为*源代码分析*）通常作为代码评审的一部分执行。 静态代码分析通常是指使用[污点检查和](https://en.wikipedia.org/wiki/Taint_checking)[数据流分析](https://en.wikipedia.org/wiki/Data-flow_analysis)等技术运行静态代码分析工具，以查找非运行代码中的潜在漏洞。

Azure 应用商店提供[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1&search=code%20review)，用于执行静态代码分析并协助代码评审。

### <a name="validate-and-sanitize-every-input-for-your-application"></a>验证和清理应用程序的每个输入

将所有输入视为不受信任的输入，以保护应用程序免受最常见的 Web 应用程序漏洞的影响。 不受信任的数据是喷射攻击的工具。 应用程序的输入包括 URL 中的参数、来自用户的输入、来自数据库或 API 的数据以及用户可能操作的任何传入内容。 应用程序应在应用程序以任何方式使用数据之前[验证](https://www.owasp.org/index.php/OWASP_Proactive_Controls_2016#4:_Validate_All_Inputs)数据在语法和语义上是否有效（包括将其显示回用户）。

在数据流的早期验证输入，以确保只有正确格式的数据才进入工作流。 您不希望格式不正确的数据保留在数据库中或触发下游组件中的故障。

黑名单和白名单是执行输入语法验证的两种常规方法：

  - 黑名单尝试检查给定的用户输入是否不包含"已知恶意"内容。

  - 白名单尝试检查给定用户输入是否与一组"已知良好"输入匹配。 基于字符的白名单是一种白名单形式，其中应用程序检查用户输入是否仅包含"已知良好"字符或输入与已知格式匹配。
    例如，这可能涉及检查用户名是否仅包含字母数字字符，或者是否仅包含两个数字。

白名单是构建安全软件的首选方法。
黑名单容易出错，因为不可能想到可能错误输入的完整列表。

在服务器上执行此操作，而不是在客户端（或在服务器上和客户端上）执行此工作。

### <a name="verify-your-applications-outputs"></a>验证应用程序的输出

在视觉上或文档中呈现的任何输出应始终进行编码和转义。 [转义](https://www.owasp.org/index.php/Injection_Theory#Escaping_.28aka_Output_Encoding.29)（也称为*输出编码*）用于帮助确保不受信任的数据不是喷射攻击的工具。 转义与数据验证相结合，提供分层防御，以提高整个系统的安全性。

转义可确保所有内容都显示为*输出。* 转义还可以让解释器知道数据不是要执行的，这可以防止攻击起作用。 这是另一种称为*跨站点脚本*（XSS） 的常见攻击技术。

如果您使用的是来自第三方的 Web 框架，则可以使用[OWASP XSS 预防备忘单](https://github.com/OWASP/CheatSheetSeries/blob/master/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.md)验证您在网站上的输出编码选项。

### <a name="use-parameterized-queries-when-you-contact-the-database"></a>联系数据库时使用参数化查询

切勿在代码中"动态"创建内联数据库查询，并直接将其发送到数据库。 插入到应用程序中的恶意代码可能会导致数据库被盗、擦除或修改。 您的应用程序还可用于在承载数据库的操作系统上运行恶意操作系统命令。

而是使用参数化查询或存储过程。 使用参数化查询时，可以从代码安全地调用该过程并将其传递给字符串，而不必担心它将被视为查询语句的一部分。

### <a name="remove-standard-server-headers"></a>删除标准服务器标头

服务器、X-供电-By 和 X-AspNet 版本等标头显示有关服务器和基础技术的信息。 我们建议您禁止这些标头，以避免对应用程序进行指纹识别。
请参阅[删除 Azure 网站上的标准服务器标头](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)。

### <a name="segregate-your-production-data"></a>隔离生产数据

您的生产数据或"真实"数据不应用于开发、测试或业务意图以外的任何其他目的。 应使用屏蔽（[匿名）](https://en.wikipedia.org/wiki/Data_anonymization)数据集进行所有开发和测试。

这意味着访问您的真实数据的人更少，这减少了您的攻击面。 这也意味着更少的员工看到个人数据，从而消除了潜在的保密违规。

### <a name="implement-a-strong-password-policy"></a>实施强密码策略

要防御暴力猜测和基于字典的猜测，必须实施强密码策略，以确保用户创建复杂的密码（例如，最小长度为 12 个字符，并且需要字母数字和特殊字符）。

您可以使用标识框架创建和强制执行密码策略。 Azure AD B2C 通过提供[内置策略](../../active-directory-b2c/tutorial-create-user-flows.md#create-a-password-reset-user-flow)、[自助服务密码重置](../../active-directory-b2c/user-flow-self-service-password-reset.md)等，帮助您进行密码管理。

要防御对默认帐户的攻击，请验证所有密钥和密码是否可替换，以及它们在安装资源后生成或替换。

如果应用程序必须自动生成密码，请确保生成的密码是随机的，并且它们具有高熵。

### <a name="validate-file-uploads"></a>验证文件上载

如果你的应用程序允许[文件上传](https://www.owasp.org/index.php/Unrestricted_File_Upload)，请考虑您可以采取的预防措施，为这种危险的活动。 在许多攻击中，第一步是将一些恶意代码放入受攻击的系统。 使用文件上载可帮助攻击者完成此目的。 OWASP 提供了用于验证文件的解决方案，以确保要上载的文件是安全的。

反恶意软件保护有助于识别和删除病毒、间谍软件和其他恶意软件。 您可以安装[微软反恶意软件](../fundamentals/antimalware.md)或微软合作伙伴的端点保护解决方案（[趋势微](https://www.trendmicro.com/azure/)[，Broadcom，](https://www.broadcom.com/products)[迈克菲](https://www.mcafee.com/us/products.aspx)[，Windows防御器](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10)，和[端点保护](https://docs.microsoft.com/configmgr/protect/deploy-use/endpoint-protection)）。

[Microsoft 反恶意软件](../fundamentals/antimalware.md)包括实时保护、计划扫描、恶意软件修复、签名更新、引擎更新、示例报告和排除事件收集等功能。 可将 Microsoft 反恶意软件和合作伙伴解决方案与 [Azure 安全中心](../../security-center/security-center-partner-integration.md)集成，以方便部署和内置检测（警报和事件）。

### <a name="dont-cache-sensitive-content"></a>不缓存敏感内容

不要在浏览器上缓存敏感内容。 浏览器可以存储缓存和历史记录的信息。 缓存的文件存储在一个文件夹中，如临时 Internet 文件文件夹，在 Internet 资源管理器的情况下。 再次引用这些页面时，浏览器将显示其缓存中的页面。 如果向用户显示敏感信息（地址、信用卡详细信息、社会保险号、用户名），则信息可能存储在浏览器的缓存中，并通过检查浏览器的缓存或只需按浏览器的 **"后退**"按钮即可检索。

## <a name="verification"></a>验证
验证阶段涉及全面努力确保代码符合前几个阶段确立的安全和隐私原则。

### <a name="find-and-fix-vulnerabilities-in-your-application-dependencies"></a>查找和修复应用程序依赖项中的漏洞

扫描应用程序及其相关库以标识任何已知的易受攻击的组件。 可用于执行此扫描的产品包括[OWASP 依赖项检查](https://www.owasp.org/index.php/OWASP_Dependency_Check)[、Snyk](https://snyk.io/)和[黑鸭](https://www.blackducksoftware.com/)。

由[Tinfoil 安全支持](https://www.tinfoilsecurity.com/)的漏洞扫描可用于 Azure 应用服务 Web 应用。 [Tinfoil 安全扫描通过 App 服务](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)为开发人员和管理员提供了一种快速、集成且经济的方法，在恶意参与者利用漏洞之前发现和解决漏洞。

> [!NOTE]
> 您还可以[将锡箔安全性与 Azure AD 集成](../../active-directory/saas-apps/tinfoil-security-tutorial.md)。 将 Tinfoil 安全性与 Azure AD 集成，为您提供了以下优势：
>  - 在 Azure AD 中，您可以控制谁有权访问锡箔安全。
>  - 用户可以通过使用其 Azure AD 帐户自动登录到 Tinfoil 安全（单一登录）。
>  - 您可以在单个中心位置 Azure 门户中管理帐户。

### <a name="test-your-application-in-an-operating-state"></a>以操作状态测试应用程序

动态应用程序安全测试 （DAST） 是一个在操作状态下测试应用程序以查找安全漏洞的过程。 DAST 工具在程序执行时分析程序，以查找安全漏洞，如内存损坏、不安全服务器配置、跨站点脚本编写、用户权限问题、SQL 注入和其他关键安全问题。

DAST 不同于静态应用程序安全测试 （SAST）。 SAST 工具在代码未执行时分析代码的源代码或编译版本，以便查找安全漏洞。

执行 DAST，最好在安全专业人员（[渗透测试仪](../fundamentals/pen-testing.md)或漏洞评估员）的协助下执行 DAST。 如果安全专业人员不可用，您可以使用 Web 代理扫描仪和一些培训自行执行 DAST。 尽早插入 DAST 扫描仪，以确保不会在代码中引入明显的安全问题。 有关 Web 应用程序漏洞扫描程序的列表，请参阅[OWASP](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools)网站。

### <a name="perform-fuzz-testing"></a>执行模糊测试

在[模糊测试中](https://cloudblogs.microsoft.com/microsoftsecure/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)，通过故意将格式错误或随机的数据引入应用程序来诱导程序失败。 诱导程序故障有助于在应用程序发布之前揭示潜在的安全问题。

[安全风险检测](https://docs.microsoft.com/security-risk-detection/)是 Microsoft 独特的模糊测试服务，用于查找软件中的安全关键错误。

### <a name="conduct-attack-surface-review"></a>执行攻击表面检查

在代码完成后查看攻击面有助于确保已考虑对应用程序或系统的任何设计或实现更改。 它有助于确保已审查和缓解因更改而创建的任何新攻击媒介，包括威胁模型。

您可以通过扫描应用程序来构建攻击面的图片。 微软提供了一个攻击面分析工具，称为[攻击表面分析器](https://www.microsoft.com/download/details.aspx?id=24487)。 您可以从许多商业动态测试和漏洞扫描工具或服务中进行选择，包括[OWASP Zed攻击代理项目](https://www.owasp.org/index.php/OWASP_Zed_Attack_Proxy_Project)、[阿拉奇尼](http://arachni-scanner.com/)、[斯基普鱼和](https://code.google.com/p/skipfish/)[w3af。](http://w3af.sourceforge.net/) 这些扫描工具对应用进行爬网并映射可通过 Web 访问的应用程序的各个部分。 您还可以在 Azure 应用商店中搜索类似的[开发人员工具](https://azuremarketplace.microsoft.com/marketplace/apps/category/developer-tools?page=1)。

### <a name="perform-security-penetration-testing"></a>执行安全渗透测试

确保应用程序的安全与测试任何其他功能一样重要。 使[渗透测试](../fundamentals/pen-testing.md)成为构建和部署过程的标准部分。 安排对已部署的应用程序进行定期安全测试和漏洞扫描，并监视打开的端口、端点和攻击。

### <a name="run-security-verification-tests"></a>运行安全验证测试

Azure （AzSK）[的安全 DevOps 工具包](https://azsk.azurewebsites.net/index.html)包含用于 Azure 平台多个服务的 SVT。 定期运行这些 SVT，以确保 Azure 订阅和组成应用程序的不同资源处于安全状态。 您还可以使用 AzSK 的连续集成/连续部署 （CI/CD） 扩展功能来自动执行这些测试，使 SVT 可作为 Visual Studio 扩展提供。

## <a name="next-steps"></a>后续步骤
在以下文章中，我们建议安全控制和活动，以帮助您设计和部署安全应用程序。

- [设计安全应用程序](secure-design.md)
- [部署安全应用程序](secure-deploy.md)
