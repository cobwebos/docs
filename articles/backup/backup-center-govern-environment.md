---
title: 使用备份中心管理备份空间
description: 了解如何管理 Azure 环境，以确保所有资源都符合备份中心的备份方式。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994792"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>使用备份中心管理备份空间

备份中心有助于管理 Azure 环境，以确保所有资源都符合备份的要求。 下面是备份中心的一些调控功能：

* 查看并分配用于备份的 Azure 策略

* 查看未配置备份的所有数据源。

## <a name="supported-scenarios"></a>支持的方案

* 有关支持的和不支持的方案的详细列表，请参阅 [支持矩阵](backup-center-support-matrix.md) 。

## <a name="azure-policies-for-backup"></a>用于备份的 Azure 策略

若要查看所有可用于备份的 [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview) ，请选择 " **用于备份的 azure 策略** " 菜单项。 这会显示可分配给订阅和资源组的所有适用于备份的内置和自定义 [Azure 策略定义](policy-reference.md) 。

通过选择任何定义，可 [将策略分配](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) 给作用域。

![选择 Azure 策略定义](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>可保护的数据源

通过选择 "可保护的数据 **源** " 菜单项，可以查看尚未配置为进行备份的所有数据源。 可以按数据源订阅、资源组、位置、类型和标记筛选列表。 确定了需要备份的数据源后，可以右键单击相应的网格项，然后选择 " **备份** "，为资源配置备份。

![可保护的数据源菜单](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [使用备份中心执行操作](backup-center-actions.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
