---
title: 在 Azure Stack 中将 API 版本配置文件与 Python 配合使用 | Microsoft Docs
description: 了解如何在 Azure Stack 中将 API 版本配置文件与 Python 配合使用。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 23b5b5d79f0f905d7c4a173247232ede2cad2877
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412441"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>在 Azure Stack 中将 API 版本配置文件与 Python 配合使用

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="python-and-api-version-profiles"></a>Python 与 API 版本配置文件

Python SDK 支持 API 版本配置文件将不同的云平台（例如 Azure Stack 和 Azure 公有云）用作目标。 可以使用 API 配置文件为混合云创建解决方案。 Python SDK 支持以下 API 配置文件：

1. **latest**  
    该配置文件以 Azure 平台中所有服务提供程序的最新 API 版本为目标。
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    该配置文件以 Azure Stack 支持的资源提供程序 API 版本为目标。

    有关 API 配置文件和 Azure Stack 的详细信息，请参阅[管理 Azure Stack 中的 API 版本配置文件](azure-stack-version-profiles.md)。

## <a name="install-azure-python-sdk"></a>安装 Azure Python SDK

1.  从[官方站点](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)安装 Git。
2.  有关安装 Python SDK 的说明，请参阅[面向 Python 开发人员的 Azure](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python)。
3.  如果不可用，请创建订阅，并保存订阅 ID 供以后使用。 有关创建订阅的说明，请参阅[在 Azure Stack 中创建套餐的订阅](../azure-stack-subscribe-plan-provision-vm.md)。 
4.  创建服务主体并保存其 ID 和机密。 有关为 Azure Stack 创建服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../azure-stack-create-service-principals.md)。 
5.  确保服务主体在订阅上具有“参与者/所有者”角色。 有关如何将角色分配到服务主体的说明，请参阅[提供对 Azure Stack 的应用程序访问权限](../azure-stack-create-service-principals.md)。

## <a name="prerequisites"></a>必备组件

若要将 Python Azure SDK 与 Azure Stack 配合使用，必须提供以下值，然后使用环境变量来设置值。 请参阅表后针对操作系统的说明，了解如何设置环境变量。 

| 值 | 环境变量 | 说明 |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| 租户 ID | AZURE_TENANT_ID | Azure Stack [租户 ID](../azure-stack-identity-overview.md) 的值。 |
| 客户端 ID | AZURE_CLIENT_ID | 在本文档上一部分创建服务主体时保存的服务主体应用程序 ID。 |
| 订阅 ID | AZURE_SUBSCRIPTION_ID | 
  [订阅 ID](../azure-stack-plan-offer-quota-overview.md#subscriptions) 用于访问 Azure Stack 中的套餐。 |
| 客户端机密 | AZURE_CLIENT_SECRET | 创建服务主体时保存的服务主体应用程序机密。 |
| 资源管理器终结点 | ARM_ENDPOINT | 参阅 [Azure Stack 资源管理器终结点](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint)。 |


## <a name="python-samples-for-azure-stack"></a>适用于 Azure Stack 的 Python 示例 

可以使用以下代码示例对 Azure Stack 中的虚拟机执行常见管理任务。

代码示例展示了如何执行以下任务：

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

若要查看执行这些操作的代码，请检查 GitHub 存储库 [virtual-machines-python-manage](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88) 中的 Python 脚本 **Hybrid/unmanaged-disks/example.py** 中的 **run_example()** 函数。

每个操作都清晰地带有注释和一个 print 函数。
这些示例不一定按以上列表中显示的顺序执行。


## <a name="run-the-python-sample"></a>运行 Python 示例

1.  如果还没有 Python，请[安装 Python](https://www.python.org/downloads/)。

    此示例（和 SDK）与 Python 2.7、3.4、3.5 和 3.6 兼容。

2.  对于 Python 开发，通常建议使用虚拟环境。 
    有关详细信息，请参阅 https://docs.python.org/3/tutorial/venv.html
    
    在 Python 3 上，请使用“venv”模块安装并初始化虚拟环境（对于 Python 2.7，必须安装 [virtualenv](https://pypi.python.org/pypi/virtualenv)）：

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  克隆存储库。

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  使用 pip 安装依赖项。

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  创建要用于 Azure Stack 的[服务主体](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)。 确保该服务主体在你的订阅上具有[参与者/所有者角色](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)。

6.  设置以下变量并将这些环境变量导出到当前 shell 中。 

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  为了运行此示例，必须在 Azure Stack 市场中存在 Ubuntu 16.04-LTS 和 windows Server 2012 R2 Datacenter 映像。 可以[从 Azure 下载](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item)这些映像或者将其[添加到平台映像存储库](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image)。

8. 运行示例。

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>说明

你可能会尝试使用 `virtual_machine.storage_profile.os_disk` 检索 VM 的 OS 磁盘。
在某些情况下，这可能能够实现你的目的，但请注意，它提供的是 `OSDisk` 对象。
若要像 `example.py` 那样更新 OS 磁盘的大小，需要的是 `OSDisk` 对象而不是 `Disk` 对象。
`example.py` 使用以下信息获取 `Disk` 对象：

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>后续步骤

- [Azure Python 开发中心](https://azure.microsoft.com/develop/python/)
- [Azure 虚拟机文档](https://azure.microsoft.com/services/virtual-machines/)
- [虚拟机的学习路径](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- 如果你没有 Microsoft Azure 订阅，可以获取免费试用帐户[此处](http://go.microsoft.com/fwlink/?LinkId=330212)。
