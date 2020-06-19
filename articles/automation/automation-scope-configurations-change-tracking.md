---
title: 使用 Azure 自动化更改跟踪和库存的作用域配置
description: 本文介绍在使用更改跟踪和库存时如何使用作用域配置。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832157"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>使用更改跟踪和库存的作用域配置

本文介绍了在 VM 上使用[更新管理](automation-update-management.md)功能时如何使用作用域配置。 

## <a name="sign-in-to-azure"></a>登录 Azure

通过 https://portal.azure.com 登录到 Azure 门户。

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>检查作用域配置

更新管理在 Log Analytics 工作区中使用作用域配置来确定要启用该更新管理的计算机。 作用域配置是包含一个或多个已保存搜索的组，该组用来将功能的作用域限制为特定计算机。 访问作用域配置：

1. 在“相关资源”下的自动化帐户中，选择“工作区” 。 

2. 选择“工作区数据源”下的工作区，然后选择“作用域配置” 。

3. 如果所选工作区尚未启用“更新管理”功能，则它会创建 `MicrosoftDefaultScopeConfig-ChangeTracking` 作用域配置。 

4. 如果所选工作区已启用该功能，则不会重新进行部署，也不会向其添加作用域配置。 

5. 选择任意作用域配置中的省略号，然后单击“编辑”。 

6. 在“编辑”窗格中，选择“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。

## <a name="view-a-saved-search"></a>查看保存的搜索

将计算机添加到更改跟踪和库存时，它也会被添加到工作区中的已保存的搜索中。 已保存的搜索是包含目标计算机的查询。

1. 导航到你的 Log Analytics 工作区并在“常规”下选择“保存的搜索” 。 更新管理使用的已保存的搜索是：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. 选择保存的搜索来查看用来填充组的查询。 下图显示了查询及其结果：

    ![保存的搜索](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>后续步骤

* 要使用更改跟踪和库存，请参阅[管理更改跟踪和库存](change-tracking-file-contents.md)。
* 要排查常见功能问题，请参阅[排查更改跟踪和库存问题](troubleshoot/change-tracking.md)。