---
title: "如何配置应用程序代理应用程序以使用 Kerberos 约束委派 | Microsoft Docs"
description: "如何为 Azure AD 应用程序代理应用程序配置 Kerberos 约束委派。"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 3a768c30cb874d42d7b4fbd2eeaa6c0e23904e10
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-an-application-proxy-application-to-use-kerberos-constrained-delegation"></a>如何配置应用程序代理应用程序以使用 Kerberos 约束委派

用于为发布的应用程序实现 SSO 的方法因应用程序不同而稍有不同，而 Azure 应用程序代理直接提供的一个选项是 Kerberos 约束委派 (KCD)。 在此选项中，连接器主机配置为代表用户向后端应用程序执行受约束的 kerberos 身份验证。

启用 KCD 的实际过程相对直接，通常只需要大致了解各种部件和促进 SSO 的身份验证流即可。 有助于针对 KCD SSO 的异常状态进行故障排除的优质信息源却非常有限。

因此，本文尝试提供一站式参考，帮助排查和自我修复一些最常见的问题。 同时为诊断更复杂、更麻烦的实现提供额外指导。

请注意，本文作出以下假设：

-   Azure 应用程序代理已按照[文档](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable)内容进行部署，面向非 KCD 应用程序的常规访问权限按预期方式运行。

-   发布的目标应用程序以 kerberos 的 IIS 和 Microsoft 实现为基础。

-   服务器和应用程序主机驻留在单个 Active Directory 域中。 有关跨域和林方案的详细信息可在我们的 [KCD 白皮书](http://aka.ms/KCDPaper)中找到。

-   主题应用程序发布在启用预身份验证的 Azure 租户中，并且用户需要通过基于表单的身份验证方法进行 Azure 身份验证。 本文未涵盖富客户端身份验证方案，但在将来的某个时候会添加这些内容。

## <a name="prerequisites"></a>先决条件

Azure 应用程序代理可部署到几乎任何类型的基础结构或环境中，而体系结构无疑会因组织的不同而不同。 引发与 KCD 相关的问题的一个最常见原因不是环境本身，而是简单的配置错误或疏忽大意。

有鉴于此，我们的建议是，在开始排除故障前始终确保满足我们在主要文章[将 KCD SSO 与应用程序代理配合使用](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)中列出的所有先决条件。

尤其是关于在 2012R2 上配置 KCD 一节中列出的先决条件，因为此配置采用完全不同的方法在以前版本的 Windows 上配置 KCD，但同时请记住其他几个注意事项：

-   域成员服务器有时会使用特定的域控制器来打开安全通道对话框， 然后随时移动到另一个对话，因此，连接器主机通常不应限制为仅与特定的本地站点 DC 通信。

-   与以上情况类似，跨域方案依赖于将连接器主机定向至 DC 的引荐，而这些 DC 可能位于本地网络范围之外。 在此方案中，另一件同等重要的事情是确保允许流量通往位于其他域的 DC，否则委派将失败。

-   应尽可能避免在连接器主机和 DC 之间放置任何正在使用的 IPS/IDS 设备，因为这些设备有时会过度侵入并干扰核心 RPC 流量。

虽然我们很想快速有效地解决问题，但这需要时间，因此应尽可能在最简单的方案中试用和测试委派。 引入的变量越多，需要应对的变量也越多。 例如，将测试限制为测试单个连接器可节省宝贵的时间，并且在问题解决后可添加其他连接器。

某些环境因素可能也会引发问题，因此还要尽可能减小用于测试的体系结构。 例如，内部防火墙 ACL 配置错误并不少见，所以尽可能让来自允许的连接器的所有流量直接流向 DC 和后端应用程序。 

实际上，放置连接器的绝对最佳位置应尽可能靠近目标。 在测试时在内联放置防火墙只会增加不必要的复杂性，并会延长调查时间。

那么，到底什么是 KCD 问题？ 在 KCD SSO 失败时会有一些典型的迹象，指示出现问题的最初迹象通常显示在浏览器中。

   ![KCD 配置不正确的错误](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

   ![授权因缺少权限而失败](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

全部失败都与执行 SSO 失败的症状相同，最终将拒绝用户对应用程序的访问权限。

## <a name="troubleshooting"></a>故障排除

如何排除故障取决于问题和观察到的症状。 在进行进一步操作之前，我们建议访问以下链接，因为它们包含可能尚未发现的有用信息：

-   [应用程序代理问题和错误消息故障排除](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)

-   [Kerberos 错误和症状](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#kerberos-errors)

-   [在本地标识和云标识不相同的情况下使用 SSO](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd#working-with-sso-when-on-premises-and-cloud-identities-are-not-identical)

如果已进行到这步，则一定存在主要问题。 需要更深入了解，因此先将流分为可进行故障排除的三个不同阶段。

**客户端预身份验证** - 外部用户通过浏览器进行 Azure 身份验证。

能够进行 Azure 预身份验证是让 KCD SSO 运行的必要条件。 如果存在任何问题，应首先对此进行测试和解决。 请注意，预身份验证阶段与 KCD 或发布的应用程序无关。 应该可以轻松更正任何差异，只需通过对主题帐户进行健全性检查，确认该帐户在 Azure 中存在并且未被禁用/阻止。 浏览器中的错误响应通常提供了足够的描述，可以让人了解原因。 如果不确定，还可以查看我们其他的故障排除文档。

**委派服务** - Azure 代理连接器代表用户从 KDC（Kerberos 分发中心）获取 kerberos 服务票证。

客户端和 Azure 前端之间的外部通信除了确保 KCD 正常工作，不应与其有任何关系。 如此一来，可通过用于获取 kerberos 票证的有效用户 ID 来提供 Azure 代理服务。 没有此 ID，KCD 将无法运行并将失败。

如前所述，浏览器错误消息通常会提供有关操作失败原因的有用线索。 请务必记下活动 ID 和响应中的时间戳，因为这样便可将行为与 Azure 代理事件日志中的实际事件关联起来。

   ![KCD 配置不正确的错误](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

在事件日志中看到的相应条目也会在事件 13019 或 12027 中看到。 可以在“应用程序和服务日志”&gt;“Microsoft”&gt;“AadApplicationProxy”&gt;“连接器”&gt;“管理员”中找到连接器事件日志。

   ![应用程序代理事件日志中的事件 13019](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

   ![应用程序代理事件日志中的事件 12027](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

-   将内部 DNS 中的 A 记录用于应用程序地址，而非 CName。

-   再次确认已向连接器主机授予对指定目标帐户 SPN 进行委派的权利，并已选择“使用任何身份验证协议”。 这在我们的主要文章 [SSO 配置](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd)中已做介绍

-   通过从任意域成员主机上的 cmd 提示符发布 **setspn -x**，验证 AD 中只存在一个 SPN 实例。

-   查看是否在强制实施域策略以限制[发布的 kerberos 令牌的最大大小](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/)，因为这将阻止连接器获取被视为过大的令牌。

为了获取关于这些问题的更详细信息，下一步是使用网络跟踪捕获连接器主机和域 KDC 之间的交换内容。 可以在[深入了解故障排除白皮书](https://aka.ms/proxytshootpaper)中找到有关信息。

如果票证看起来正常，则应在日志中看到一个事件，声明身份验证因应用程序返回 401 而失败。 通常，这表示目标应用程序已拒绝票证，所以请继续转到下一个阶段。

**目标应用程序** - 连接器提供的 kerberos 票证的使用者

在此阶段，连接器应该已将 kerberos 服务票证发送至后端，作为第一个应用程序请求中的标头。

-   通过使用门户中定义的应用程序内部 URL，验证该应用程序可从连接器主机上的浏览器直接访问。 然后就可以成功登录。 有关此操作的详细信息可在连接器“故障排除”页面上找到。

-   还是在连接器主机上，通过执行以下操作之一，确认浏览器和应用程序之间的身份验证在使用 kerberos：

1.  在 Internet Explorer 中运行开发人员工具（“F12”），或从连接器主机上使用 [Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/)。 使用内部 URL 转到应用程序，然后检查在应用程序的响应中返回的提供的 WWW 授权标题，以确保存在 negotiate 或 kerberos。 作为响应从浏览器返回到应用程序的后续 kerberos blob 通常以“YII”开头，这是一个好迹象，表明 kerberos 在发生作用。 另一方面，NTLM 总是以“TlRMTVNTUAAB”开头，通过 Base64 解码后读作 NTLMSSP。 如果在 blob 的开头看到“TlRMTVNTUAAB”，则表示该 Kerberos **不**可用。 如果未看到，Kerberos 有可能可用。

  * 请注意，如果使用 Fiddler，此方法需要暂时禁用针对 IIS 中应用程序配置的扩展保护。

     ![浏览器网络检查窗口](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

    *图：*因为它不以“TIRMTVNTUAAB”开头，所以此示例表示 Kerberos 可用。 这是不以“YII”开头的 Kerberos Blob 的示例。

2.  暂时从 IIS 站点的提供程序列表中删除 NTLM，并从连接器主机上的 IE 直接访问应用。 当 NTLM 不在提供程序列表中时，应仅能够通过 Kerberos 访问应用程序。 如果此操作失败，则表示应用程序配置存在问题，并且 Kerberos 身份验证不起作用。

如果 Kerberos 不可用，则检查 IIS 中的应用程序身份验证设置，确保 negotiate 列在最顶端，下方紧挨着 NTLM。 （Not Negotiate:kerberos 或 Negotiate:PKU2U）。 仅在 Kerberos 起作用时继续。

   ![Windows 身份验证提供程序](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)
   
-   既然有了 Kerberos 和 NTLM，我们暂时在门户中禁用应用程序的预身份验证。 查看是否可以使用外部 URL 从 Internet 访问它。 系统应会提示进行身份验证，并且应该可以使用在上一步中使用的相同帐户进行身份验证。 如果不可以，则表示是后端应用程序出现问题，而非 KCD。

-   现在在门户中重新启用预身份验证，并尝试通过外部 URL 连接到应用程序，以通过 Azure 进行身份验证。 如果 SSO 失败，应在浏览器中看到禁止的错误消息，并可在日志中看到事件 13022：

    *Microsoft AAD 应用程序代理连接器无法对用户进行身份验证，因为后端服务器回应给 Kerberos 身份验证尝试的是 HTTP 401 错误。*

    ![HTTTP 401 禁止错误](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

-   如下所示，通过导航到 IIS，查看 IIS 应用程序以确保将已配置的应用程序池配置为使用在 AD 中配置 SPN 所使用的相同帐户

    ![IIS 应用程序配置窗口](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

    知道标识后，从 cmd 提示符发出以下命令，确保此帐户肯定通过该 SPN 进行配置。 例如，**setspn – q http/spn.wacketywack.com**

    ![SetSPN 命令窗口](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

-   检查根据门户中的应用程序设置定义的 SPN 与根据应用程序的应用池使用的目标 AD 帐户配置的 SPN 是同一个 SPN。

   ![Azure 门户中的 SPN 配置](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)
   
-   转到 IIS，为应用程序选择“配置编辑器”选项，并导航至 **system.webServer/security/authentication/windowsAuthentication** 以确保 **UseAppPoolCredentials** 设置为 true。

   ![IIS 配置应用池凭据选项](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

将内核模式保留为启用状态虽然在提升 Kerberos 操作的性能方面很有用，但也导致所请求的服务的票证可使用计算机帐户解密。 这也称为本地系统，因此当应用程序在场中多个服务器上托管时，将它设置为 true 可中断 KCD。

-   作为额外检查，还可以禁用**扩展**保护。 过去遇到过的情况已经证明，如果应用程序作为默认网站的子文件夹发布，在特定配置中启用扩展保护会使 KCD 中断。 其本身配置为仅限匿名身份验证，使整个对话框呈灰色，表示子对象不会继承任何当前活动设置。 但是我们建议尽量启用该选项，所以在各种测试后，不要忘了将此设置恢复为启用状态。

这些额外检查应可让你以正确的方式开始使用已发布的应用程序。 可以继续并加快同样配置为委派的额外连接器，但是如果没有进展，我们建议阅读更详细的技术演练 [Azure AD 应用程序代理故障排除完整指南](https://aka.ms/proxytshootpaper)

如果仍无法进一步解决问题，支持人员将非常乐意帮助你从这一步继续下去。 直接在门户中创建支持票证，我们的工程师将联系你。

## <a name="other-scenarios"></a>其他方案

-   在将请求发送至应用程序前，Azure 应用程序代理将请求 Kerberos 票证。 某些第三方应用程序（例如 Tableau Server）不使用这种验证身份的方法，而是进行需要更常规的协商。 首个请求为匿名请求，允许应用程序响应通过 401 支持的身份验证类型。

-   双跃点身份验证 - 常用于应用程序分层的情形，具有都需要验证身份的后端和前端，例如 SQL 报告服务。

## <a name="next-steps"></a>后续步骤
[在托管域上配置 Kerberos 约束委派 (KCD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-enable-kcd)
