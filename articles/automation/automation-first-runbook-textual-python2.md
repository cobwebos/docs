---
title: 我在 Azure 自动化中的第一个 Python Runbook
description: 本教程指导创建、测试和发布一个简单的 Python Runbook。
services: automation
ms.subservice: process-automation
ms.date: 03/19/2019
ms.topic: conceptual
ms.openlocfilehash: b788e5bda65702305ed6f6b001b57c28939aa875
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405913"
---
# <a name="my-first-python-runbook"></a>我的第一个 Python Runbook

> [!div class="op_single_selector"]
> - [图形](automation-first-runbook-graphical.md)
> - [PowerShell](automation-first-runbook-textual-powershell.md)
> - [PowerShell 工作流](automation-first-runbook-textual.md)
> - [Python](automation-first-runbook-textual-python2.md)

本教程介绍了如何在 Azure 自动化中创建 [Python Runbook](automation-runbook-types.md#python-runbooks)。 首先从测试和发布的简单 Runbook 开始。 然后，通过修改 Runbook 来实际管理 Azure 资源，这种情况下会启动 Azure 虚拟机。 最后，通过添加 Runbook 参数使此 Runbook 更稳健。

> [!NOTE]
> 不支持使用 Webhook 启动 Python runbook。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要以下各项：

- Azure 订阅。 如果您还没有，您可以[激活您的 MSDN 订阅者权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或注册[一个免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- [自动化帐户](automation-offering-get-started.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。 此帐户必须有权启动和停止虚拟机。
- Azure 虚拟机。 需停止和启动该虚拟机，因此其不应为生产用 VM。

## <a name="create-a-new-runbook"></a>创建新的 Runbook

首先创建一个输出文本 Hello World 的简单 Runbook**。

1. 在 Azure 门户中，打开自动化帐户。

    通过自动化帐户页面可快速查看此帐户中的资源。 应该已拥有某些资产。 大多数此类资产都是自动包括在新的自动化帐户中的模块。 还应具有在 [“先决条件”](#prerequisites)中提到的凭证资产。<br>

1. 在 **"流程自动化**"下选择**Runbook**以打开 Runbook 列表。
1. 选择 **"添加运行簿**"以创建新的 Runbook。
1. 将该 Runbook 命名为 MyFirstRunbook-Python****。
1. 为**Runbook 类型**选择**Python 2。**
1. 单击“创建”以创建 Runbook 并打开文本编辑器 **** 。

## <a name="add-code-to-the-runbook"></a>将代码添加到 Runbook

现在添加一个简单的命令来打印文本`Hello World`。

```python
print("Hello World!")
```

单击“保存”保存 runbook****。

## <a name="test-the-runbook"></a>测试 Runbook

在发布 Runbook 使其可在生产中使用之前，需对其进行测试以确保其能正常工作。 测试 Runbook 时，你可以运行其“草稿”版本并以交互方式查看其输出。

1. 单击“测试窗格” **** 打开测试窗格。
1. 单击“启动”以启动测试 **** 。 这应该是唯一的已启用选项。
1. 将创建一个 [Runbook 作业](automation-runbook-execution.md) 并显示其状态。
   作业状态以"已排队"开头，表示正在等待云中的 Runbook 工作人员可用。 在某个辅助角色认领此作业后，作业状态将变为“正在启动”，然后当 Runbook 实际开始运行时，此状态将变为“正在运行”。
1. Runbook 作业完成后，会显示其输出。 在这种情况下，您应该看到`Hello World`。
1. 关闭测试窗格以返回到画布。

## <a name="publish-and-start-the-runbook"></a>发布和启动 Runbook

创建的 Runbook 仍处于“草稿”模式。 需要首先发布此 Runbook 才能在生产中运行它。
发布 Runbook 时，使用草稿版本覆盖现有已发布版本。
在此示例中，因此才创建了 Runbook，因此还没有已发布版本。

1. 单击“发布”以发布该 Runbook，并在出现提示时单击“是”********。
1. 如果向左滚动以查看 Runbook 页上的**Runbook，** 则应看到**已发布的****创作状态**。
1. 向右滚动查看“MyFirstRunbook-Python”**** 窗格。
   顶部的选项允许您启动 Runbook、查看 Runbook 或计划它在未来某个时间启动。
2. 单击 **"开始"，** 然后在"开始运行簿"边栏选项卡打开时单击"**确定**"。
3. 为您创建的 Runbook 作业打开作业窗格。 您可以关闭此窗格，但让我们将其保持打开状态，以便可以监视作业的进度。
1. 作业状态显示在“作业摘要”中并且与测试该 Runbook 时看到的状态相匹配****。
2. 运行簿状态显示"已完成"后，单击"**输出**"。 将打开"输出"窗格，您可以在其中查看`Hello World`。
3. 关闭“输出”窗格。
4. 单击“所有日志”打开 Runbook 作业的“流”窗格****。 应仅在输出流`Hello World`中看到。 但是，如果 Runbook 写入 Runbook 作业，则此窗格可以显示 Runbook 作业的其他流，如"详细"和"错误"。
5. 关闭“流”窗格和“作业”窗格以返回到“MyFirstRunbook-Python”窗格。
6. 单击“作业”打开此 Runbook 的“作业”页****。 此页列出此 Runbook 创建的所有作业。 由于只运行该作业一次，应该只会看到一个列出的作业。
7. 可以单击此作业，打开在启动 Runbook 时查看过的“作业”窗格。 此窗格允许您返回时间并查看为特定 Runbook 创建的任何作业的详细信息。

## <a name="add-authentication-to-manage-azure-resources"></a>添加身份验证来管理 Azure 资源

已经测试并发布 Runbook，但到目前为止它不执行任何有用的操作。 需要让其管理 Azure 资源。
为此，脚本必须使用来自自动化帐户的凭据进行身份验证。 如需帮助，可以使用 [Azure 自动化实用工具包](https://github.com/azureautomation/azure_automation_utility)更轻松地进行身份验证以及与 Azure 资源交互。

> [!NOTE]
> 自动化帐户必须使用服务主体功能创建，才能有"运行为证书"。
> 如果自动化帐户不是使用服务主体创建的，则可以按照 Python 的 Azure[管理库进行身份验证](/azure/python/python-sdk-azure-authenticate)中所述进行身份验证。

1. 通过单击 MyFirstRunbook-Python 窗格上的“编辑”打开文本编辑器****。

2. 添加以下代码以对 Azure 进行身份验证：

   ```python
   import os
   from azure.mgmt.compute import ComputeManagementClient
   import azure.mgmt.resource
   import automationassets

   def get_automation_runas_credential(runas_connection):
       from OpenSSL import crypto
       import binascii
       from msrestazure import azure_active_directory
       import adal

       # Get the Azure Automation RunAs service principal certificate
       cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
       pks12_cert = crypto.load_pkcs12(cert)
       pem_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM,pks12_cert.get_privatekey())

       # Get run as connection information for the Azure Automation service principal
       application_id = runas_connection["ApplicationId"]
       thumbprint = runas_connection["CertificateThumbprint"]
       tenant_id = runas_connection["TenantId"]

       # Authenticate with service principal certificate
       resource ="https://management.core.windows.net/"
       authority_url = ("https://login.microsoftonline.com/"+tenant_id)
       context = adal.AuthenticationContext(authority_url)
       return azure_active_directory.AdalAuthentication(
       lambda: context.acquire_token_with_client_certificate(
               resource,
               application_id,
               pem_pkey,
               thumbprint)
       )

   # Authenticate to Azure using the Azure Automation RunAs service principal
   runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
   azure_credential = get_automation_runas_credential(runas_connection)
   ```

## <a name="add-code-to-create-python-compute-client-and-start-the-vm"></a>添加代码以创建 Python Compute 客户端并启动 VM

若要使用 Azure VM，请创建 [Python 的 Azure Compute 客户端](https://docs.microsoft.com/python/api/azure-mgmt-compute/azure.mgmt.compute.computemanagementclient)实例。

使用计算客户端启动 VM。 将以下代码添加到 Runbook：

```python
# Initialize the compute management client with the RunAs credential and specify the subscription to work against.
compute_client = ComputeManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"])
)


print('\nStart VM')
async_vm_start = compute_client.virtual_machines.start(
    "MyResourceGroup", "TestVM")
async_vm_start.wait()
```

包含`MyResourceGroup`VM 的资源组的名称在哪里，`TestVM`并且是要启动的 VM 的名称。

再次测试并运行 Runbook，以便看到它启动 VM。

## <a name="use-input-parameters"></a>使用输入参数

Runbook 当前对资源组和 VM 的名称使用硬编码值。
现在我们来添加用于从输入参数获取这些值的代码。

使用 `sys.argv` 变量获取参数值。
将以下代码添加到 Runbook，使其紧随其他 `import` 语句之后：

```python
import sys

resource_group_name = str(sys.argv[1])
vm_name = str(sys.argv[2])
```

它将导入 `sys` 模块，并创建两个变量以用于保存资源组名称和 VM 名称。
请注意，参数列表的元素 `sys.argv[0]` 是脚本的名称，不是用户的输入。

现在可以修改 Runbook 的最后两行，以使用输入参数值，而不使用硬编码的值：

```python
async_vm_start = compute_client.virtual_machines.start(
    resource_group_name, vm_name)
async_vm_start.wait()
```

启动 Python Runbook 时（在"测试"窗格上或作为已发布的 Runbook），可以在 **"参数**"下的"开始运行簿"页中输入参数的值。

在第一个框中开始输入值后，将出现第二个值，等等，以便根据需要输入尽可能多的参数值。

这些值可用于数组中的脚本，`sys.argv`就像您刚刚添加的代码一样。

输入资源组的名称作为第一个参数的值，输入 VM 的名称作为第二个参数的值。

![输入参数值](media/automation-first-runbook-textual-python/runbook-python-params.png)

单击“确定”**** 启动 Runbook。 Runbook 运行，并启动指定的 VM。

## <a name="error-handling-in-python"></a>Python 中的错误处理

还可以使用以下约定从 Python Runbook 中检索各种流，包括警告、错误和调试流。

```python
print("Hello World output") 
print("ERROR: - Hello world error")
print("WARNING: - Hello world warning")
print("DEBUG: - Hello world debug")
print("VERBOSE: - Hello world verbose")
```

以下示例显示了 `try...except` 块中使用的此约定。

```python
try:
    raise Exception('one', 'two')
except Exception as detail:
    print 'ERROR: Handling run-time error:', detail
```

> [!NOTE]
> Azure 自动化不支持`sys.stderr`。

## <a name="next-steps"></a>后续步骤

- 要开始使用 PowerShell 运行簿，请参阅[我的第一个 PowerShell 运行簿](automation-first-runbook-textual-powershell.md)。
- 要开始使用图形 Runbook，请参阅[我的第一个图形运行簿](automation-first-runbook-graphical.md)。
- 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)。
- 要了解有关 Runbook 类型及其优点和限制的更多信息，请参阅[Azure 自动化 Runbook 类型](automation-runbook-types.md)。
- 若要了解如何使用 Python 进行 Azure 开发，请参阅[适用于 Python 开发人员的 Azure](/azure/python/)。
- 若要查看 Python 2 runbook 示例，请参阅 [Azure 自动化 GitHub](https://github.com/azureautomation/runbooks/tree/master/Utility/Python)。
