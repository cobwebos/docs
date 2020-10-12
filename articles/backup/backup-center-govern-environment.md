---
title: 使用备份中心管理备份空间
description: 了解如何管理 Azure 环境，以确保所有资源都符合备份中心的备份方式。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 8a16769be827bf34e5be82409d5b8eb014cdcf1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91614305"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>使用备份中心管理备份空间

备份中心有助于管理 Azure 环境，以确保所有资源都符合备份的要求。 下面是备份中心的一些调控功能：

* 查看并分配用于备份的 Azure 策略

* 在所有用于备份的内置 Azure 策略上查看资源的符合性。

* 查看未配置备份的所有数据源。

## <a name="supported-scenarios"></a>支持的方案

* 有关支持的和不支持的方案的详细列表，请参阅 [支持矩阵](backup-center-support-matrix.md) 。

## <a name="azure-policies-for-backup"></a>用于备份的 Azure 策略

若要查看所有可用于备份的 [Azure 策略](https://docs.microsoft.com/azure/governance/policy/overview) ，请选择 " **用于备份的 azure 策略** " 菜单项。 这会显示可分配给订阅和资源组的所有适用于备份的内置和自定义 [Azure 策略定义](policy-reference.md) 。

通过选择任何定义，可 [将策略分配](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) 给作用域。

![选择 Azure 策略定义](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="backup-compliance"></a>备份符合性

单击 "备份符合性" 菜单项可帮助你根据已分配给 Azure 环境的各种内置策略来查看资源的 [符合性](https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data) 。 你可以查看符合所有策略的资源的百分比，以及具有一个或多个不符合标准的策略。

![查看备份符合性](./media/backup-center-govern-environment/azure-policy-compliance.png)

## <a name="protectable-datasources"></a>可保护的数据源

通过选择 "可保护的数据 **源** " 菜单项，可以查看尚未配置为进行备份的所有数据源。 可以按数据源订阅、资源组、位置、类型和标记筛选列表。 确定了需要备份的数据源后，可以右键单击相应的网格项，然后选择 " **备份** "，为资源配置备份。

![可保护的数据源菜单](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>后续步骤

* [监视和操作备份](backup-center-monitor-operate.md)
* [使用备份中心执行操作](backup-center-actions.md)
* [获取有关备份的见解](backup-center-obtain-insights.md)
