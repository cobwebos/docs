---
title: 使用 Python Azure 堆栈中的 API 版本配置文件 |Microsoft 文档
description: 了解如何使用 Python Azure 堆栈中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>使用 Python Azure 堆栈中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="python-samples-for-azure-stack"></a>Azure 堆栈的 Python 示例 

以下代码示例可用于 Azure 堆栈中的虚拟机执行常见管理任务。

代码示例显示了到：

- 创建虚拟机：
    - 创建 Linux 虚拟机
    - 创建 Windows 虚拟机
- 更新虚拟机：
    - 展开一个驱动器
    - 标记虚拟机
    - 附加数据磁盘
    - 分离数据磁盘
- 运行虚拟机：
    - 启动虚拟机
    - 停止虚拟机
    - 重新启动虚拟机
- 列出虚拟机
- 删除虚拟机

若要查看要执行这些操作的代码，请查看**run_example()** Python 脚本中的函数**Hybrid/unmanaged-disks/example.py** GitHub 存储库中[虚拟机-python 的管理](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88)。

每个操作都清楚地标有注释和打印功能。
这些示例并不一定在上面的列表显示的顺序。


## <a name="run-the-python-sample"></a>运行的 Python 示例

1.  如果你还没有，[安装 Python](https://www.python.org/downloads/)。

    此示例 （和 SDK） 是与 Python 2.7，3.4、 3.5 和 3.6 兼容。

2.  Python 开发，一般建议是使用虚拟环境。 
    有关详细信息，请参阅 https://docs.python.org/3/tutorial/venv.html
    
    安装和初始化 Python 3 上的"venv"模块的虚拟环境 (必须安装[virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2.7):

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

4.  安装使用 pip 的依赖关系。

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  创建[服务主体](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals)要使用 Azure 堆栈。 请确保你的服务主体具有[参与者/所有者角色](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal)对你的订阅。

6.  设置以下变量并将这些环境变量导出到当前的 shell。 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  请注意，若要运行此示例，Ubuntu 16.04 LTS 和 windows Server 2012 R2 Datacenter 映像必须位于 Azure 堆栈市场位置。 这些可以是[从 Azure 下载](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item)或[到平台映像存储库添加](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image)。


8. 运行示例。

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>说明

你可能想要尝试使用检索 VM 的 OS 磁盘`virtual_machine.storage_profile.os_disk`。
在某些情况下，这可能预期的那样，但请注意它为您提供`OSDisk`对象。
若要为更新 OS 磁盘的大小、`example.py`不，你无需`OSDisk`对象但`Disk`对象。
`example.py` 获取`Disk`替换为以下对象：

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>后续步骤

- [Azure Python 开发中心](https://azure.microsoft.com/develop/python/)
- [Azure 虚拟机文档](https://azure.microsoft.com/services/virtual-machines/)
- [虚拟机的学习路径](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- 如果你没有 Microsoft Azure 订阅可以获取免费的试用帐户[此处](http://go.microsoft.com/fwlink/?LinkId=330212)。
