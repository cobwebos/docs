---
title: 快速入门：使用 Azure Monitor 从混合 Linux 计算机收集数据
description: 本快速入门介绍如何为 Azure 外部运行的 Linux 计算机部署 Log Analytics 代理，并通过 Azure Monitor 日志启用数据收集。
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: magoedte
ms.custom: mvc, seo-javascript-september2019
ms.openlocfilehash: cdaca0fd3e314ecb5c0b68438eaaf87fbfb699b0
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933030"
---
# <a name="collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>使用 Azure Monitor 从混合环境中的 Linux 计算机收集数据

[Azure Monitor](../overview.md) 可以直接从环境中的物理或虚拟 Linux 计算机将数据收集到 Log Analytics 工作区中，以便进行详细分析和关联。 安装 [Log Analytics 代理](../platform/log-analytics-agent.md)可让 Azure Monitor 从数据中心或其他云环境收集数据。 本快速入门介绍如何通过几个简单步骤，从 Linux 服务器中配置和收集数据。 有关 Azure Linux VM 的信息，请参阅[收集有关 Azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md)。  

若要了解支持的配置，请参阅[支持的 Windows 操作系统](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems)和[网络防火墙配置](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)。
 
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。 

## <a name="create-a-workspace"></a>创建工作区

1. 在 Azure 门户中，选择“所有服务”。  在资源列表中，键入“Log Analytics”  。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”  。

    ![Azure 门户](media/quick-collect-azurevm/azure-portal-01.png)<br>  

2. 选择“创建”  ，然后为以下各项选择选项：

   * 为新的 Log Analytics 工作区  提供名称，如 DefaultLAWorkspace  。  
   * 如果选择的默认值不合适，请从下拉列表中选择要链接到的**订阅**。
   * 对于“资源组”  ，选择包含一个或多个 Azure 虚拟机的现有资源组。  
   * 选择向其部署 VM 的“位置”  。  如需其他信息，请参阅[提供 Log Analytics 的区域](https://azure.microsoft.com/regions/services/)。
   * 如果在 2018 年 4 月 2 日后创建的新订阅中创建工作区，则它将自动使用“每 GB”  定价计划，并且不提供用于选择定价层的选项。  如果是为 4 月 2 日之前创建的现有订阅创建工作区，或者是为绑定到现有 EA 注册的订阅创建工作区，则可以选择首选定价层。  有关特定层的其他信息，请参阅 [Log Analytics 定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。
  
        ![创建 Log Analytics 资源边栏选项卡](media/quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. 在“Log Analytics 工作区”窗格中提供所需的信息后，选择“确定”。    

在验证信息和创建工作区时，可以在菜单中的“通知”下面跟踪操作进度  。 

## <a name="obtain-workspace-id-and-key"></a>获取工作区 ID 和密钥

在安装适用于 Linux 的 Log Analytics 代理前，需要先获得 Log Analytics 工作区的工作区 ID 和秘钥。  代理包装器脚本需要使用此信息来正确配置代理，并确保它能与 Azure Monitor 成功通信。

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. 在 Azure 门户左上角选择“所有服务”  。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics 工作区”  。

2. 在 Log Analytics 工作区列表中，选择前面创建的工作区。 （可能已将其命名为 **DefaultLAWorkspace**。）

3. 选择“高级设置”： 

    ![Log Analytics 高级设置](media/quick-collect-azurevm/log-analytics-advanced-settings-01.png) 
 
4. 选择“已连接的源”，然后选择“Linux 服务器”   。

5. “工作区 ID”和“主密钥”右侧的值   。 将它们复制并粘贴到喜爱的编辑器中。

## <a name="install-the-agent-for-linux"></a>安装适用于 Linux 的代理

以下步骤配置在 Azure 和 Azure 政府云中用于 Log Analytics 的代理。  

>[!NOTE]
>无法将适用于 Linux 的 Log Analytics 代理配置为向多个 Log Analytics 工作区报告。  

如果 Linux 计算机需要通过代理服务器与 Log Analytics 通信，可以在命令行中指定代理配置，方法是包括 `-p [protocol://][user:password@]proxyhost[:port]`。  *proxyhost* 属性接受代理服务器的完全限定域名或 IP 地址。 

例如： `https://user01:password@proxy01.contoso.com:30443`

1. 若要配置 Linux 计算机以连接至 Log Analytics 工作区，请运行以下命令，并提供先前复制的工作区 ID 和主密钥。 以下命令将下载代理、验证其校验和并将其安装好。 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    以下命令包括 `-p` 代理参数和示例语法。

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. 若要配置 Linux 计算机以连接至 Azure 政府云中的 Log Analytics 工作区，请运行以下命令，并提供先前所复制的工作区 ID 和主密钥。 以下命令将下载代理、验证其校验和并将其安装好。 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    以下命令包括 `-p` 代理参数和示例语法。

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. 运行以下命令重启代理： 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>收集的事件和性能数据

Azure Monitor 可以从你为长期分析和报告指定的 Linux syslog 和性能计数器中收集事件。 检测到特定的状况时，它还可以采取措施。 首先，请按照下列步骤操作，配置 Linux Syslog 以及几个常见性能计数器中收集事件。  

1. 在 Azure 门户左下角选择“更多服务”  。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics 工作区”  。

2. 选择“数据”  ，然后选择 **Syslog**。  

3. 可以通过键入日志名称来添加 syslog。 输入“Syslog”，然后选择加号 ( **+** )。   

4. 在表中，取消选中严重性“信息”  、“通知”  和“调试”  。 

5. 选择页面顶部的“保存”来保存配置。 

6. 选择“Linux 性能数据”  ，在 Linux 计算机上启用性能计数器收集。 

7. 首次为新的 Log Analytics 工作区配置 Linux 性能计数器时，可以选择快速创建几个通用的计数器。 将这些计数器在一个复选框中依次列出。

    ![选中的默认 Windows 性能计数器](media/quick-collect-azurevm/linux-perfcounters-default.png)

    选择“将下列配置应用到我的计算机”  ，然后选择“添加选定的性能计数器”  。 随即会添加它们，并且通过 10 秒收集示例间隔进行预设。  

8. 选择页面顶部的“保存”来保存配置。 

## <a name="view-data-collected"></a>查看收集的数据

现已启用数据收集，开始运行简单的日志搜索示例，查看来自目标计算机的部分数据。  

1. 在所选工作区中，从左侧窗格中选择“日志”  。

2. 在日志查询页上，在查询编辑器中键入 `Perf`，然后选择“运行”  。
 
    ![Log Analytics 日志搜索](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    例如，下图中的查询返回了 10,000 条性能记录。 结果会大大减少。

    ![Log Analytics 日志搜索结果](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>清理资源

如无需再使用，可从 Linux 计算机中删除代理，并删除 Log Analytics 工作区。  

若要删除代理，请在 Linux 计算机上运行以下命令。 *--purge* 参数可彻底删除代理及其配置。

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

若要删除工作区，请选择前面创建的 Log Analytics 工作区，在资源页上选择“删除”  。

![删除 Log Analytics 资源](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>后续步骤

从本地 Linux 计算机上收集操作和性能数据后，现在可轻松开始浏览、分析免费收集的数据，并对它们采取措施  。  

若要了解如何查看和分析数据，请继续本教程。

> [!div class="nextstepaction"]
> [在 Log Analytics 中查看或分析数据](../../azure-monitor/learn/tutorial-viewdata.md)
