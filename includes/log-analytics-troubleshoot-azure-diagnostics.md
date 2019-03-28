---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58505832"
---
### <a name="troubleshoot-azure-diagnostics"></a>排查 Azure 诊断问题

如果收到以下错误消息，说明未注册 Microsoft.insights 资源提供程序：

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

若要注册资源提供程序，请在 Azure 门户中执行以下步骤：

1.  在左侧导航窗格中，单击“订阅”
2.  选择在错误消息中标识的订阅
3.  单击“资源提供程序”
4.  找到 *Microsoft.insights* 提供程序
5.  单击“注册”链接

![注册 microsoft.insights 资源提供程序](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

注册 *Microsoft.insights* 资源提供程序以后，可重试配置诊断。


在 PowerShell 中，如果收到以下错误消息，则需更新 PowerShell 版本：

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

更新你的 Azure PowerShell 版本，请按照中的说明[安装 Azure PowerShell](/powershell/azure/install-az-ps)一文。
