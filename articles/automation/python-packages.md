---
title: 在 Azure 自动化中管理 Python 2 包
description: 本文介绍如何在 Azure 自动化中管理 Python 2 包。
services: automation
ms.subservice: process-automation
ms.date: 02/25/2019
ms.topic: conceptual
ms.openlocfilehash: 9f52dfd92d430abffe5857d231898dd4b0e7745e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679921"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>在 Azure 自动化中管理 Python 2 包

通过 Azure 自动化，可以在 Azure 和 Linux 混合 Runbook 辅助角色上运行 Python 2 runbook。 为了帮助简化 runbook，可以使用 Python 包导入所需的模块。 本文介绍如何在 Azure 自动化中管理和使用 Python 包。

## <a name="import-packages"></a>导入包

在自动化帐户中，在**共享资源**下选择**Python 2 包**。 单击“+ 添加 Python 2 包”  。

![添加 Python 包](media/python-packages/add-python-package.png)

在“添加 Python 包”页中，选择要上传的本地包。 包可以是 **.whl**或 **.tar.gz**文件。 选择包后，单击 **"确定"** 以上载它。

![添加 Python 包](media/python-packages/upload-package.png)

导入包后，该包将列在自动化帐户中的 Python 2 包页面上。 如果需要删除包，请选择包并单击"**删除**"。

![包列表](media/python-packages/package-list.png)

## <a name="import-packages-with-dependencies"></a>导入带依赖项的包

Azure 自动化不在导入过程中解析 Python 包的依赖项。 可以通过两种方式导入包及其所有依赖项。 只需使用以下步骤之一才能将包导入自动化帐户。

### <a name="manually-download"></a>手动下载

在安装了 [python2.7](https://www.python.org/downloads/release/latest/python2) 和 [pip](https://pip.pypa.io/en/stable/) 的 Windows 64 位计算机上运行以下命令，以便下载包及其所有依赖项：

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

等到这些包下载以后，即可将其导入自动化帐户中。

### <a name="runbook"></a>Runbook

将 python runbook[导入 Python 2 包从 pypi 导入 Azure 自动化帐户](https://gallery.technet.microsoft.com/scriptcenter/Import-Python-2-packages-57f7d509)，从库导入自动化帐户。 确保运行设置设置为**Azure，** 然后使用参数启动 Runbook。 Runbook 需要一个"运行为"帐户，以便自动化帐户正常工作。 对于每个参数，请确保使用以下列表和图像中的开关启动它：

* -s\<订阅 Id\>
* -g\<资源组\>
* -一\<个自动化帐户\>
* -m\<模块包\>

![包列表](media/python-packages/import-python-runbook.png)

Runbook 允许您指定要下载的包。 例如，使用 参数`Azure`会下载所有 Azure 模块和所有依赖项（约 105）。

运行簿完成后，可以检查自动化帐户中的**共享资源**下的**Python 2 包**，以验证包是否正确导入。

## <a name="use-a-package-in-a-runbook"></a>在 runbook 中使用包

导入包后，可以在 Runbook 中使用它。 下面的示例使用[Azure 自动化实用程序包](https://github.com/azureautomation/azure_automation_utility)。 使用此包，可以轻松地通过 Azure 自动化使用 Python。 要使用包，请按照 GitHub 存储库中的说明将其添加到 Runbook。 例如，可以使用`from azure_automation_utility import get_automation_runas_credential`导入函数以检索"运行为"帐户。

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

要开始使用 Python 2 运行簿，请参阅[我的第一个 Python 2 运行簿](automation-first-runbook-textual-python2.md)。
