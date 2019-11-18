---
title: 使用扩展的部署后配置
description: 了解如何使用 Azure 资源管理器模板扩展提供部署后配置。
author: mumian
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: jgao
ms.openlocfilehash: c7896efb453cac478202efedb268c4bc838aef3a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150370"
---
# <a name="provide-post-deployment-configurations-by-using-extensions"></a>使用扩展提供部署后配置

模板扩展是小型应用程序，用于在 Azure 资源上提供部署后配置和自动化任务。 最受欢迎的扩展是虚拟机扩展。 请参阅[适用于 Windows 的虚拟机扩展和功能](../virtual-machines/extensions/features-windows.md)或[适用于 Linux 的虚拟机扩展和功能](../virtual-machines/extensions/features-linux.md)。

## <a name="extensions"></a>扩展

现有的扩展如下：

- [Microsoft.Compute/virtualMachines/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachines/extensions)
- [Microsoft.Compute virtualMachineScaleSets/extensions](https://docs.microsoft.com/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions)
- [Microsoft.HDInsight clusters/extensions](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/2018-06-01-preview/clusters/extensions)
- [Microsoft.Sql servers/databases/extensions](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions) 
- [Microsoft.Web/sites/siteextensions](https://docs.microsoft.com/azure/templates/microsoft.web/2016-08-01/sites/siteextensions)

若要找出可用的扩展，请浏览到[模板参考](https://docs.microsoft.com/azure/templates/)。 在“按标题筛选”中，输入“扩展”。

若要了解如何使用这些扩展，请参阅：

- [教程：通过 Azure 资源管理器模板部署虚拟机扩展](./resource-manager-tutorial-deploy-vm-extensions.md)。
- [教程：通过 Azure 资源管理器模板导入 SQL BACPAC 文件](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [教程：使用 Azure 资源管理器模板部署虚拟机扩展](./resource-manager-tutorial-deploy-vm-extensions.md)
