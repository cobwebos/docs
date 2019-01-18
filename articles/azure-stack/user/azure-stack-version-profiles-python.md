---
title: 在 Azure Stack 中将 API 版本配置文件与 Python 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 Python 配合使用。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 8049db848e34b0aa9bc23f08169a8c63f765791a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389756"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Python 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="python-and-api-version-profiles"></a>Python 与 API 版本配置文件

Python SDK 支持 API 版本配置文件将不同的云平台（例如 Azure Stack 和 Azure 公有云）用作目标。 可以使用 API 配置文件为混合云创建解决方案。 Python SDK 支持以下 API 配置文件：

- **latest**  
    此配置文件以在 Azure 平台中的所有服务提供程序为都目标的最新的 API 版本。
- **2018-03-01-hybrid**     
    此配置文件以在 Azure Stack 平台中的所有资源提供程序为都目标的最新的 API 版本。
- **2017-03-09-profile**  
    此配置文件以支持 Azure Stack 的资源提供程序的最兼容的 API 版本为目标。

   有关 API 配置文件和 Azure Stack 的详细信息，请参阅[管理 Azure Stack 中的 API 版本配置文件](azure-stack-version-profiles.md)。

## <a name="install-the-azure-python-sdk"></a>安装 Azure Python SDK

1. 从[官方站点](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)安装 Git。
2. 了解如何安装 Python SDK，请参阅[面向 Python 开发人员的 Azure](/python/azure/python-sdk-azure-install?view=azure-python)。
3. 如果不可用，创建订阅并保存以供以后使用的订阅 ID。 有关创建订阅的说明，请参阅[在 Azure Stack 中创建产品/服务](../azure-stack-subscribe-plan-provision-vm.md)。
4. 创建服务主体并保存其 ID 和机密。 有关如何为 Azure Stack 创建服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../azure-stack-create-service-principals.md)。
5. 确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何将角色分配到服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>必备组件

若要对 Azure Stack 中使用 Python Azure SDK，必须提供以下值，并将使用环境变量的值。 在你的操作系统上设置环境变量的表后，请参阅说明。

| 值 | 环境变量 | 说明 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租户 ID | AZURE_TENANT_ID | Azure Stack [租户 ID](../azure-stack-identity-overview.md) 的值。 |
| 客户端 ID | AZURE_CLIENT_ID | 在本文上一部分中创建服务主体时保存主体的应用程序 ID 的服务。 |
| 订阅 ID | AZURE_SUBSCRIPTION_ID | [订阅 ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) 用于访问 Azure Stack 中的套餐。 |
| 客户端机密 | AZURE_CLIENT_SECRET | 创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | ARM_ENDPOINT | 请参阅[Azure Stack 资源管理器终结点](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)。 |
| 资源位置 | AZURE_RESOURCE_LOCATION | Azure Stack 环境的资源位置。

## <a name="python-samples-for-azure-stack"></a>适用于 Azure Stack 的 Python 示例

下面是一些可用于 Azure Stack 使用 Python SDK 的代码示例：

- [管理资源和资源组](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)。
- [管理存储帐户](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)。
- [管理虚拟机](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/)。

## <a name="python-manage-virtual-machine-sample"></a>Python 管理虚拟机示例

下面的代码示例可用于在 Azure Stack 中的虚拟机执行常见管理任务。 此代码示例介绍了为：

- 创建虚拟机：
  - 创建 Linux 虚拟机
  - 创建 Windows 虚拟机
- 更新虚拟机：
  - 扩展驱动器
  - 标记虚拟机
  - 附加数据磁盘
  - 分离数据磁盘
- 操作虚拟机：
  - 启动虚拟机
  - 停止虚拟机
  - 重新启动虚拟机
- 列出虚拟机
- 删除虚拟机

若要查看代码执行这些操作，请参阅**run_example()** Python 脚本中的函数**example.py** GitHub 存储库中[混合-计算-Python 的管理的 VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

每个操作都清晰地带有注释和一个 print 函数。 这些示例并不一定是此列表中显示的顺序。

## <a name="run-the-python-sample"></a>运行 Python 示例

1. 如果尚不存在，有[安装 Python](https://www.python.org/downloads/)。 此示例（和 SDK）与 Python 2.7、3.4、3.5 和 3.6 兼容。

2. 对于 Python 开发，通常建议使用虚拟环境。 有关详细信息，请参阅[Python 文档](https://docs.python.org/3/tutorial/venv.html)。

3. 在 Python 3 上，请使用“venv”模块安装并初始化虚拟环境（对于 Python 2.7，必须安装 [virtualenv](https://pypi.python.org/pypi/virtualenv)）：

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. 克隆存储库：

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. 安装使用 pip 的依赖项：

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. 创建要用于 Azure Stack 的[服务主体](../azure-stack-create-service-principals.md)。 确保该服务主体在你的订阅上具有[参与者/所有者角色](../azure-stack-create-service-principals.md#assign-a-role)。

7. 设置以下变量，并将这些环境变量导出到当前 shell 中：

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. 若要运行此示例，Ubuntu 16.04-LTS 和 windows Server 2012 R2 Datacenter 映像必须位于 Azure Stack marketplace。 这些可以是[从 Azure 下载](../azure-stack-download-azure-marketplace-item.md)，或添加到[平台映像存储库](../azure-stack-add-vm-image.md)。

9. 运行示例：

    ```python
    python example.py
    ```


## <a name="next-steps"></a>后续步骤

- [Azure Python 开发中心](https://azure.microsoft.com/develop/python/)
- [Azure 虚拟机文档](https://azure.microsoft.com/services/virtual-machines/)
- [虚拟机的学习路径](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- 如果你没有 Microsoft Azure 订阅，可以获取免费试用帐户[此处](https://go.microsoft.com/fwlink/?LinkId=330212)。
