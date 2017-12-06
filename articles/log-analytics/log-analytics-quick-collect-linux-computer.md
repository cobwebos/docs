---
title: "使用 Azure Log Analytics 从本地 Linux 计算机上收集数据 | Microsoft Docs"
description: "了解如何为 Linux 部署 Log Analytics 代理，并使用 Log Analytics 从该操作系统启用数据收集。"
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 66748adc49ef921ab8adb5306b2a483234b076ae
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>从托管在环境中的 Linux 计算机上收集数据
[Azure Log Analytics](log-analytics-overview.md) 可直接从物理或虚拟 Linux 计算机及环境中的其他资源收集数据，并将数据置于单个存储库进行详细的分析和关联。  本快速入门介绍如何通过几个简单步骤，从 Linux 计算机中配置或收集数据。  有关 Azure Linux VM 的信息，请参阅以下主题[收集 Azure 虚拟机的相关数据](log-analytics-quick-collect-azurevm.md)。  

如果还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="log-in-to-azure-portal"></a>登录到 Azure 门户
登录 Azure 门户 ([https://portal.azure.com](https://portal.azure.com))。 

## <a name="create-a-workspace"></a>创建工作区
1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。<br><br> ![Azure 门户](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. 单击“创建”，然后为以下各项选择选项：

  * 为新的 OMS 工作区提供名称，如 DefaultLAWorkspace。 
  * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
  * 对于“资源组”，选择包含一个或多个 Azure 虚拟机的现有资源组。  
  * 选择向其部署 VM 的“位置”。  如需其他信息，请参阅[提供 Log Analytics 的区域](https://azure.microsoft.com/regions/services/)。
  * 可从 Log Analytics 中三个不同的“定价层”进行选择，但本快速入门将选择“免费”层。  有关特定层的其他信息，请参阅 [Log Analytics 定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. 在“OMS 工作区”窗格上提供所需信息后，单击“确定”。  

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度。 

## <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥
在安装适用于 Linux 的 OMS 代理前，需要先获得 Log Analytics 工作区的工作区 ID 和秘钥。  代理包装器脚本需要使用此信息来正确配备代理，并确保它能与 Log Analytics 成功通信。  

1. 在 Azure 门户中，单击左下角的“更多服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 工作区列表中，选择之前创建的 DefaultLAWorkspace。
3. 选择“高级设置”。<br><br> ![Log Analytics 高级设置](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. 选择“已连接的源”，然后选择“Linux 服务器”。   
5. “工作区 ID”和“主密钥”右侧的值。 将它们复制并粘贴到喜爱的编辑器中。   

## <a name="install-the-agent-for-linux"></a>安装适用于 Linux 的代理
以下步骤配置在 Azure 和 Azure 政府云中用于 Log Analytics 的代理。  

>[!NOTE]
>无法将适用于 Linux 的 OMS 代理配置为向多个 Log Analytics 工作区报告。  

1. 要配置 Linux 计算机以连接至 Log Analytics，请运行以下命令，并提供先前复制的工作区 ID 和主密钥。  此命令将下载代理、验证其校验和并对其进行安装。 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. 要配置 Linux 计算机以连接至 Azure 政府云中的 Log Analytics，请运行以下命令，并提供先前所复制的工作区 ID 和主密钥。  此命令将下载代理、验证其校验和并对其进行安装。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>配置代理与代理服务器进行通信

如果 Linux 计算机需要通过代理服务器与 Log Analytics 通信，请执行以下步骤。  代理配置值具有以下语法 `[protocol://][user:password@]proxyhost[:port]`。

1. 运行以下命令编辑文件 `/etc/opt/microsoft/omsagent/proxy.conf`，并针对特定设置更改相关的值。

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. 运行以下命令重启代理： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>收集的事件和性能数据
Log Analytics 可从 Linux Syslog 以及指定用于长期分析的性能计数器中收集事件，并在检测到特定条件时采取措施。  首先，请按照下列步骤操作，配置 Linux Syslog 以及几个常见性能计数器中收集事件。  

1. 选择“Syslog”。  
2. 可通过键入日志名称添加事件日志。  键入“Syslog”，然后单击加号 +。  
3. 在表中，取消选中严重性“信息”、“通知”和“调试”。 
4. 单击页面顶部的“保存”来保存配置。
5. 选择“Linux 性能数据”，在 Windows 计算机上启用性能计数器收集。 
6. 首次为新的 Log Analytics 工作区配置 Linux 性能计数器时，可以选择快速创建几个通用的计数器。 将这些计数器在一个复选框中依次列出。<br><br> ![默认选中的 Windows 性能计数器](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png)。<br><br> 单击“添加所选性能计数器”。  随即会添加它们，并且通过 10 秒收集示例间隔进行预设。  
7. 单击页面顶部的“保存”来保存配置。

## <a name="view-data-collected"></a>查看收集的数据
现已启用数据收集，开始运行简单的日志搜索示例，查看来自目标计算机的部分数据。  

1. 在 Azure 门户中，导航到 Log Analytics 并选择之前创建的工作区。
2. 单击“日志搜索”磁贴并在“日志搜索”窗格上的查询字段中键入 `Perf`，然后按 Enter 或单击查询字段右侧的搜索按钮。<br><br> ![Log Analytics 日志搜索查询示例](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> 例如，下图中的查询返回了 735 条性能记录。<br><br> ![Log Analytics 日志搜索结果](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>清理资源
如无需再使用，可从 Linux 计算机中删除代理，并删除 Log Analytics 工作区。  

按以下步骤删除代理。

1. 将 Linux 代理[通用脚本](https://github.com/Microsoft/OMS-Agent-for-Linux/releases)下载到计算机。
2. 在计算机上运行 bundle .sh file w the --purge 参数，彻底删除代理及其配置。

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

若要删除工作区，请选择前面创建的 Log Analytics 工作区，在资源页上单击“删除”。<br><br> ![删除 Log Analytics 资源](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>后续步骤
从本地 Linux 计算机上收集操作和性能数据后，现在可轻松开始浏览、分析免费收集的数据，并对它们采取措施。  

若要了解如何查看和分析数据，请继续本教程。   

> [!div class="nextstepaction"]
> [在 Log Analytics 中查看或分析数据](log-analytics-tutorial-viewdata.md)
