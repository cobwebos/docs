---
title: 在 Azure 自动化中管理 Python 2 包
description: 本文介绍如何在 Azure 自动化中管理 Python 2 包。
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/25/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 81ce9cb2667ce9f21d7c18a92e417e47768d7efb
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407925"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>在 Azure 自动化中管理 Python 2 包

通过 Azure 自动化，可以在 Azure 和 Linux 混合 Runbook 辅助角色上运行 Python 2 runbook。 为了帮助简化 runbook，可以使用 Python 包导入所需的模块。 本文介绍如何在 Azure 自动化中管理和使用 Python 包。

## <a name="import-packages"></a>导入包

在自动化帐户中，选择“共享资源”下的“Python 2 包”。 单击“+ 添加 Python 2 包”。

![添加 Python 包](media/python-packages/add-python-package.png)

在“添加 Python 包”页中，选择要上传的本地包。 包可以是 `.whl` 文件或 `.tar.gz` 文件。 选择后，单击“确定”以上传包。

![添加 Python 包](media/python-packages/upload-package.png)

一旦导入包之后，它将列出在**Python 2 包**在自动化帐户中的页。 如果需要删除包，请选择包，然后在包页面中选择“删除”。

![包列表](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>导入具有依赖项包

Azure 自动化不能导入过程解决 python 包的依赖项。 有两种方法及其所有依赖项包导入。 以下步骤之一，需要使用将包导入到自动化帐户。

### <a name="manually-download"></a>手动下载

与计算机上 Windows 64 位[下，python2.7](https://www.python.org/downloads/release/latest/python2)并[pip](https://pip.pypa.io/en/stable/)安装，运行以下命令以下载程序包和所有依赖项：

```
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

一旦下载的程序包，您可以将它们导入自动化帐户。

### <a name="runbook"></a>Runbook

导入 python runbook [pypi 入 Azure 自动化帐户中的导入 Python 2 包](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)从库到自动化帐户。 请确保在运行设置设置为**Azure**使用参数来启动 runbook。 Runbook 需要一个运行方式帐户用于要工作的自动化帐户。 对于每个参数，请确保开始其交换机中的以下列表和图像所示：

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

![包列表](media/python-packages/import-python-runbook.png)

Runbook 可以指定哪个包下载，例如， `Azure` （第四个参数） 将下载所有 Azure 模块和所有依赖项，即大约 105。

在 runbook 完成后可以检查**Python 2 包**页**共享资源**在自动化帐户来验证这些包已正确导入。

## <a name="use-a-package-in-a-runbook"></a>在 runbook 中使用包

一旦您已导入包，您现在可以使用它在 runbook 中。 下面的示例使用 [Azure 自动化实用工具包](https://github.com/azureautomation/azure_automation_utility)。 使用此包，可以轻松地通过 Azure 自动化使用 Python。 若要使用包，请按照 GitHub 存储库中的说明操作，并使用 `from azure_automation_utility import get_automation_runas_credential` 将其添加到 runbook，例如导入用于检索运行方式帐户的函数。

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

## <a name="develop-and-test-runbooks-offline"></a>脱机开发并测试 runbook

若要脱机开发并测试 Python 2 runbook，可使用 GitHub 上的[Azure Automation python emulated assets](https://github.com/azureautomation/python_emulated_assets)（Azure 自动化 python 模拟资产）模块。 此模块使你能够引用凭据、变量、连接和证书等共享资源。

## <a name="next-steps"></a>后续步骤

若要开始使用 Python 2 Runbook，请参阅[第一个 Python 2 Runbook](automation-first-runbook-textual-python2.md)
