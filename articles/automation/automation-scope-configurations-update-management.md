---
title: 使用 Azure 自动化更新管理的作用域配置
description: 本文介绍在使用更新管理时如何使用作用域配置。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832021"
---
# <a name="work-with-scope-configurations-for-update-management"></a>使用更新管理的作用域配置

本文介绍了在 VM 上使用[更新管理](automation-update-management.md)功能时如何使用作用域配置。 

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>检查作用域配置

更新管理在 Log Analytics 工作区中使用作用域配置来确定要启用该功能的计算机。 作用域配置是包含一个或多个已保存搜索的组，该组用来将功能的作用域限制为特定计算机。 访问作用域配置：

1. 在“相关资源”下的自动化帐户中，选择“工作区” 。 

2. 选择“工作区数据源”下的工作区，然后选择“作用域配置” 。

3. 如果所选工作区尚未启用“更新管理”功能，则它会创建 `MicrosoftDefaultScopeConfig-Updates` 作用域配置。 

4. 如果所选工作区已启用该功能，则不会重新进行部署，也不会向其添加作用域配置。 

5. 选择任意作用域配置中的省略号，然后单击“编辑”。 

6. 在“编辑”窗格中，选择“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。

## <a name="view-a-saved-search"></a>查看保存的搜索

将计算机添加到更新管理时，它也会被添加到工作区中的已保存的搜索中。 已保存的搜索是包含目标计算机的查询。

1. 导航到你的 Log Analytics 工作区并在“常规”下选择“保存的搜索”。  更新管理使用的已保存的搜索是：

|名称     |类别  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

2. 选择保存的搜索来查看用来填充组的查询。 下图显示了查询及其结果：

    ![保存的搜索](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>后续步骤

* 要使用此功能，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要对功能错误进行故障排除，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理错误进行故障排除，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 若要对 Linux 更新代理错误进行故障排除，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。