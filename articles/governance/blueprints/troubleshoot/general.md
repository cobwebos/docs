---
title: 排查常见错误
description: 了解如何排查创建和分配蓝图时出现的问题
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 40668fed2fcc2a04e39fa3a4d7e8e8923c75ae05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315514"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>排查使用 Azure 蓝图时出现的错误

创建或分配蓝图时可能会出现问题。 本文描述可能会发生的各种错误及其解决方法。

## <a name="finding-error-details"></a>查找错误详细信息

将蓝图分配到作用域是许多错误产生的原因。 分配失败时，蓝图会提供失败部署的详细信息。 此信息会指出存在的问题，以便可以修复问题并确保后续部署成功进行。

1. 单击“所有服务”，然后在左窗格中搜索并选择“策略”。 在“策略”页上，单击“蓝图”。

1. 选择左侧页面上的“已分配蓝图”，然后使用搜索框筛选分配的蓝图以找到失败的分配项。 还可以按“预配状态”列对分配表进行排序，集中查看失败的分配项。

1. 使用鼠标左键单击状态为“失败”的蓝图，或者右键单击然后选择“查看分配详细信息”。

1. 蓝图分配页面顶部有一个红色横幅警告，指出此分配已失败。 单击横幅任意位置可获取更多详细信息。

错误通常是由某个项目导致的，而不是由蓝图整体导致的。 如果某个项目创建密钥保管库但是 Azure 策略阻止密钥保管库创建，则整个分配将失败。

## <a name="general-errors"></a>常规错误

### <a name="policy-violation"></a>场景：策略冲突

#### <a name="issue"></a>问题

由于策略冲突而导致模板部署失败。

#### <a name="cause"></a>原因

如下多个原因可能会导致策略与部署相冲突：

- 正在创建的资源受到策略的限制（通常为 SKU 或位置限制）
- 部署是由策略配置的设置字段（通常带有标记）

#### <a name="resolution"></a>解决方法

更改蓝图，使其不与错误详细信息中的策略冲突。 如果无法进行此更改，替代方法是更改策略分配的作用域，以使蓝图不再与策略冲突。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

- 通过 [Azure 论坛](https://azure.microsoft.com/support/forums/)获取 Azure 专家的解答
- 与 [@AzureSupport](https://twitter.com/azuresupport)（Microsoft Azure 官方帐户）联系，它可以将 Azure 社区引导至适当的资源来改进客户体验：提供解答、支持和专业化服务。
- 如需更多帮助，可以提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 **获取支持**。