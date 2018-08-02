---
title: 使用应用程序代理应用程序时出现“无法访问此企业应用程序”错误 | Microsoft Docs
description: 如何解决使用 Azure AD 应用程序代理应用程序时的常见访问问题。
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 80d25e7751e7fb676887879eefae85e5a6a04cd1
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2018
ms.locfileid: "39367158"
---
# <a name="cant-access-this-corporate-application-error-when-using-an-application-proxy-application"></a>使用应用程序代理应用程序时出现“无法访问此企业应用程序”错误

本文可帮助解决 Azure AD 应用程序代理应用程序中的“无法访问此公司应用”错误的常见问题。

## <a name="overview"></a>概述
当你看到此错误时，请在错误页上找到状态代码。 该代码可能是以下状态代码之一：

-   **网关超时**：应用程序代理服务无法访问连接器。 此错误通常指示连接器分配、连接器本身或者与连接器相关的网络规则存在问题。

-   **错误的网关**：连接器无法访问后端应用程序。 此错误可能表示应用程序配置错误。

-   **禁止访问**：用户无权访问此应用程序。 在 Azure Active Directory 中用户未分配到此应用程序时，或者在后端上用户没有访问此应用程序的权限时，可能会发生此错误。

若要查找代码，可查看错误消息左下角的“状态代码”字段中的文本。 查找页面底部是否有任何其他提示。

   ![网关超时错误](./media/application-proxy/connection-problem.png)

有关如何诊断这些错误的根本原因以及建议的修复的详细信息，请参阅以下相应部分。

## <a name="gateway-timeout-errors"></a>网关超时错误

当服务尝试访问连接器，但未能在超时时段内访问时，会发生网关超时。 此错误通常是由于应用程序分配到连接器未正常工作的连接器组，或者连接器所需的一些端口未打开所致。


## <a name="bad-gateway-errors"></a>“错误的网关”错误

“错误的网关”错误指示连接器无法访问后端应用程序。 请确保已发布正确的应用程序。 导致此错误的常见错误有：

-   内部 URL 中存在拼写错误或错误

-   未发布应用程序的根 URL。 例如，发布 <http://expenses/reimbursement> 但尝试访问 <http://expenses>

-   Kerberos 约束委派 (KCD) 配置问题

-   后端应用程序问题

## <a name="forbidden-errors"></a>禁止访问错误

如果出现禁止访问错误，则表明用户未分配到该应用程序。 此错误可能会出现在 Azure Active Directory 中或后端应用程序上。

要了解如何将用户分配到 Azure 中的应用程序，请参阅[配置文档](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal#add-a-test-user)。

如果确认用户已分配到 Azure 中的应用程序，请检查后端应用程序中的用户配置。 如果你正在使用 Kerberos 约束委派/集成 Windows 身份验证，请参阅“KCD 故障排除”页，以了解指导原则。

## <a name="check-the-applications-internal-url"></a>检查应用程序的内部 URL

作为第一个快捷步骤，请通过“企业应用程序”打开此应用程序，并选择“应用程序代理”菜单， 进行仔细检查并修复内部 URL。 确认此内部 URL 是否是使用本地网络来访问应用程序的。

## <a name="check-the-application-is-assigned-to-a-working-connector-group"></a>检查应用程序是否已分配到正常工作的连接器组

若要确认应用程序已分配到正常工作的连接器组：

1.  通过转到“Azure Active Directory”，并依次单击“企业应用程序”和“所有应用程序”，在门户中打开应用程序。 打开应用程序，并从左侧菜单中选择“应用程序代理”。

2.  查看“连接器组”字段。 如果组中没有活动连接器，将看到一条警告。 如果未显示任何警告，则继续“验证所有必需端口都已列入允许列表”。

3.  如果显示错误的连接器组，请使用下拉列表选择正确的组，并确认不再显示任何警告。 如果显示预期的连接器组，请单击警告消息以打开“连接器管理”页面。

4.  此处，有几种方法可进一步深入探讨：

  * 将活动连接器移入该组：如果存在某个活动连接器应属于该组，并对目标后端应用程序具有视距，则可以将此连接器移入指定的组。 若要执行此操作，请单击“连接器”。 在“连接器组”字段中，使用下拉列表选择正确的组，并单击“保存”。

  * 为该组下载新的连接器：从该页面可获取指向[下载新连接器](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/Download)的链接。 将连接器安装在直接与后端应用程序相连的机器上。 通常，需要在应用程序所在的同一台服务器上安装连接器。 使用“下载连接器”链接将连接器下载到目标计算机上。 接下来，单击“连接器”并使用“连接器组”下拉列表，确保它属于正确的组。

  * 调查非活动连接器：如果连接器显示为非活动，它将无法访问服务。 此错误通常是因为某些必需的端口遭到阻止。 若要解决此问题，需继续“验证所有必需端口都已列入允许列表”。

使用上述步骤确保应用程序分配到具有正常工作的连接器的组之后，再次测试该应用程序。 如果它仍无法正常工作，则继续执行下一部分。

## <a name="check-all-required-ports-are-whitelisted"></a>检查所有必需的端口是否都已列入允许列表

若要确认所有必需的端口都已打开，请参阅有关打开端口的文档。 如果所有必需的端口都已打开，则继续下一部分。

## <a name="check-for-other-connector-errors"></a>检查其他连接器错误

如果上述步骤未能解决此问题，下一步是查找连接器本身的问题或错误。 可以在[故障排除文档](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot#connector-errors)中查看一些常见错误。 

也可以直接查看连接器日志以标识任何错误。 很多错误消息都提供了具体的修复建议。 若要查看日志，请参阅[连接器文档](manage-apps/application-proxy-connectors.md#under-the-hood)。

## <a name="additional-resolutions"></a>其他解决方法

如果上述方法未能解决该问题，存在几个不同的可能原因。 若要确定问题：

如果应用程序配置为使用集成 Windows 身份验证 (IWA)，则在不使用单一登录的情况下测试应用程序。 如果未配置，则转到下一段落。 要在不使用单一登录的情况下检查应用程序，可通过“企业应用程序”打开应用程序，并转到“单一登录”菜单。 将下拉列表从“集成 Windows 身份验证”更改为“已禁用 Azure AD 单一登录”。 

此时打开浏览器，并尝试重新访问该应用程序。 系统应该会提示进行身份验证，并进入应用程序。 如果你能够进行身份验证，则表示已启用单一登录的 Kerberos 约束委派 (KCD) 配置存在问题。 有关详细信息，请参阅“KCD 故障排除”页。

如果继续看到此错误，请转到装有连接器的计算机，打开浏览器并尝试访问用于应用程序的内部 URL。 连接器的作用类似于同一台计算机中的另一个客户端。 如果无法访问应用程序，则需要调查该计算机无法访问应用程序的原因，或使用能够访问应用程序的服务器上的连接器。

如果可以从该计算机访问应用程序，则查找连接器本身的问题或错误。 可以在[故障排除文档](manage-apps/application-proxy-troubleshoot.md#connector-errors)中查看一些常见错误。 也可以直接查看连接器日志以标识任何错误。 我们的很多错误消息都能共享更具体的修复建议。 若要了解如何查看日志，请参阅[我们的连接器文档](manage-apps/application-proxy-connectors.md#under-the-hood)。

## <a name="next-steps"></a>后续步骤
[了解 Azure AD 应用程序代理连接器](manage-apps/application-proxy-connectors.md)
