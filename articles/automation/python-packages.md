---
title: 在 Azure 自动化中管理 Python 2 包
description: 本文介绍如何在 Azure 自动化中管理 Python 2 包。
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.custom: tracking-python
ms.openlocfilehash: f3ba52c1396928d8c76fb85fda3f29c625e60919
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84561851"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>在 Azure 自动化中管理 Python 2 包

通过 Azure 自动化，可以在 Azure 和 Linux 混合 Runbook 辅助角色上运行 Python 2 runbook。 为了帮助简化 runbook，可以使用 Python 包导入所需的模块。 本文介绍如何在 Azure 自动化中管理和使用 Python 包。

## <a name="import-packages"></a>导入包

在 Azure 自动化帐户中，选择“共享资源”下的“Python 2 包” 。 单击“+ 添加 Python 2 包”。

![添加 Python 包](media/python-packages/add-python-package.png)

在“添加 Python 2 包”页中，选择要上传的本地包。 包可以是 .whl 或 .tar.gz 文件 。 选择包后，单击“确定”以上传。

![添加 Python 包](media/python-packages/upload-package.png)

导入包后，它将列在 Azure 自动化帐户中的“Python 2 包”页面上。 如果需要移除包，请选择包并单击“删除”。

![包列表](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>导入具有依赖项的包

在导入过程中，Azure 自动化不会解析 Python 包的依赖项。 可以通过两种方式导入包及其所有依赖项。 只需使用以下步骤之一，即可将包导入到 Azure 自动化帐户中。

### <a name="manually-download"></a>手动下载

在安装有 [Python2.7](https://www.python.org/downloads/release/latest/python2) 和 [pip](https://pip.pypa.io/en/stable/) 的 Windows 64 位计算机上，运行以下命令以下载包及其所有依赖项：

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

下载包后，可将其导入到你的自动化帐户中。

### <a name="runbook"></a>Runbook

 要获取 runbook，请[将 Python 2 包从 pypi 导入到 Azure 自动化帐户中](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)，从库导入到 Azure 自动化帐户中。 请确保“运行设置”设置为“Azure”并启动具有参数的 runbook。 runbook 需要有一个运行方式帐户，Azure 自动化帐户才能正常使用。 对于每个参数，请确保按照以下列表和图片中所示，通过开关进行启动：

* -s\<subscriptionId\>
* -g\<resourceGroup\>
* -a\<automationAccount\>
* -m\<modulePackage\>

![包列表](media/python-packages/import-python-runbook.png)

runbook 允许你指定下载的包。 例如，使用 `Azure` 参数将下载所有 Azure 模块和所有依赖项（大约 105 个）。

完成 runbook 后，可以在 Azure 自动化帐户中的“共享资源”下查看 Python 2 包，以验证是否已正确导入包。 

## <a name="use-a-package-in-a-runbook"></a>在 runbook 中使用包

导入包后，可以在 runbook 中使用。 以下示例使用 [Azure 自动化实用工具包](https://github.com/azureautomation/azure_automation_utility)。 使用此包，可以轻松地通过 Azure 自动化使用 Python。 要使用该包，请按照 GitHub 存储库中的说明进行，并将其添加到 runbook。 例如，可以使用 `from azure_automation_utility import get_automation_runas_credential` 导入用于检索运行方式帐户的函数。

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

要脱机开发并测试你的 Python 2 runbook，可以使用 GitHub 上的 [Azure Automation Python 模拟资产](https://github.com/azureautomation/python_emulated_assets)。 此模块使你能够引用凭据、变量、连接和证书等共享资源。

## <a name="next-steps"></a>后续步骤

要准备 Python runbook，请参阅[创建 Python runbook](learn/automation-tutorial-runbook-textual-python2.md)。
