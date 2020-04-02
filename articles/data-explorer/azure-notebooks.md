---
title: 使用 Azure 笔记本分析 Azure 数据资源管理器中的数据
description: 本主题演示如何使用 Azure 笔记本创建查询
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522402"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>使用 Azure 笔记本分析 Azure 数据资源管理器中的数据

[Azure 笔记本](https://notebooks.azure.com/)是一种 Azure 云服务，它简化了创建和共享[Jupyter 笔记本](https://jupyter.org/)。 Azure 笔记本使组合文档、代码和运行代码的结果变得容易。

> [!Note]
> * 以下过程使用 Azure 笔记本环境中的 Python 客户端来查询 Azure 数据资源管理器。 但是，您也可以使用[KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic)查询 Azure 数据资源管理器。
> * 某些用户报告使用 Edge 进行身份验证的问题;在此类问题得到解决之前，请使用其他浏览器。

## <a name="create-a-project"></a>创建项目

1. 在浏览器中打开[Azure 笔记本](https://notebooks.azure.com/)并登录。

1. 在标题中选择 **"我的项目**"选项卡。 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. 选择 **= 新项目**。
    
1. 在 **"创建新项目"** 对话框中：
    1. 输入**项目名称**。
    1. 清除 **"公共**"复选框。
        >[!Important]
        > 如果不清除"公共"复选框，您的项目将在打开的 Internet 上公开。
    1. 选择“创建”  。
    
    ![创建新项目](media/azurenotebooks/an-create-new-project-blank.png)

    项目 ID 将自动创建。

## <a name="create-a-notebook"></a>创建笔记本

1. 在新项目中，创建笔记本。 笔记本应使用[受支持的语言](https://github.com/Azure/azure-kusto-python#minimum-requirements)。
要创建笔记本，请选择 **"新建"** 并选择 **"笔记本**"。

    ![创建新笔记本](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. 在"**创建新笔记本"** 对话框中，输入笔记本名称。

1. 选择**Python 3.6**并选择 **"新建**"。
    
    ![创建新笔记本](media/azurenotebooks/an-create-new-notebook.png) 
    
1. 在项目中，选择新笔记本。

    ![选择新笔记本](media/azurenotebooks/an-select-notebook.png)

    等待，直到 Python 内核初始化。 当填充的圆图标更改为空心圆图标时，您可以继续。

    ![内核初始化](media/azurenotebooks/an-python-init-icon.png)

1. 要导入系统模块，请输入以下命令并选择 **"运行**：
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > 要运行单元格，还可以按 Shift_Enter。

1.  要导入 SDK 类，请输入以下命令并选择 **"运行**：
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  要生成连接字符串并启动 Kusto 客户端，请输入以下命令并选择 **"运行**：  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. 在提示符下，打开新的浏览器选项卡。 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 
   
1. 输入授权代码，并登录您的 AAD （@microsoft.com） 帐户。 Kusto 客户端`kc`现在可以使用您的标识向 Azure 数据资源管理器进行身份验证。

1. 返回到笔记本以查看身份验证结果。 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>执行库斯托查询

1. 输入库斯托查询并选择 **"运行**"。 例如：

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>后续步骤

* [库斯图-python GitHub 存储库](https://github.com/Azure/azure-kusto-python)
