---
title: "渗透测试 | Microsoft Docs"
description: "本文概述了渗透测试 (pentest) 过程，以及如何针对 Azure 基础结构中运行的应用执行 pentest。"
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="pen-testing"></a>渗透测试
使用 Microsoft Azure 进行应用程序测试和部署的最大优势之一是，无需为了开发、测试和部署应用程序而汇总本地基础结构。 所有基础结构均由 Microsoft Azure 平台服务管理。 不必为请求、获取以及“搭架和堆叠”本地硬件担心。

这太棒了，但仍需要确保进行常规安全审慎调查。 需要做的事情之一就是对 Azure 中部署的应用程序进行渗透测试。

用户可能已经知道，Microsoft 将执行[对 Azure 环境的渗透测试](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)。 这有助于我们改进我们的平台，并指导我们在改善安全控制、引入新的安全控制和改进安全流程方面的操作行为。

我们不会对应用程序进行渗透测试，但我们知道希望并且需要对自己的应用程序执行渗透测试。 这是一件好事，因为增强应用程序的安全性可帮助使整个 Azure 生态系统更加安全。

当用户对应用程序进行渗透测试时，看起来就像是对我们进行一次攻击。 我们[持续监视](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx)各种攻击模式，并且会根据需要发起事件响应过程。 如果用户进行正当的渗透测试我们就触发事件响应，这对用户和我们都没有好处。

怎么办？

准备好对 Azure 托管的应用程序进行渗透测试时，用户可以选择[告知我们](https://portal.msrc.microsoft.com/en-us/engage/pentest)。 我们知道用户要执行特定的测试后，就不会无意中将用户关闭（例如阻止用户在测试时需要使用的 IP 地址），但前提是用户的测试遵循 [Microsoft 云统一渗透测试参与规则](https://technet.microsoft.com/en-us/mt784683)中所述的 Azure 渗透测试条款和条件。
可以执行的标准测试包括：

* 对终结点进行测试，以发现[开放 Web 应用程序安全项目 (OWASP) 的前 10 个漏洞](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* 终结点的[模糊测试](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/)
* 终结点的[端口扫描](https://en.wikipedia.org/wiki/Port_scanner)

用户不能执行的一类测试是任何类型的[拒绝服务 (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) 攻击。 其中包括：发起 DoS 攻击，或者执行相关的测试，以便确定、演示或模拟任何类型的 DoS 攻击。

做好对 Microsoft Azure 中托管的应用程序进行渗透测试的准备了吗？ 如果已做好准备，则可转到[渗透测试概述](https://technet.microsoft.com/library/mt784683.aspx)页，单击页面底部的“创建测试请求”按钮。 用户还会发现有关渗透测试条款和条件的详细信息，并可通过各种有用的链接报告 Azure 或其他 Microsoft 服务的相关安全缺陷。
