---
title: 为混合 Windows 计算机配置 Azure Log Analytics 代理 | Microsoft Docs
description: 本快速入门介绍如何为 Azure 外部运行的 Windows 计算机部署 Log Analytics 代理，并通过 Log Analytics 启用数据收集。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 15885137b9559bf34fb2b985398401af09caa629
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65602980"
---
# <a name="configure-the-log-analytics-agent-for-windows-computers-in-a-hybrid-environment"></a>在混合环境中为 Windows 计算机配置 Log Analytics 代理
[Azure Log Analytics](../../azure-monitor/platform/agent-windows.md) 可直接从物理或虚拟 Windows 计算机收集数据，并将数据置于单个存储库进行详细的分析和关联。 Log Analytics 可从数据中心或其他云环境收集数据。 本快速入门介绍如何通过几个简单步骤，从 Windows 计算机中配置或收集数据。  有关 Azure Windows VM 的信息，请参阅[收集有关 Azure 虚拟机的数据](../../azure-monitor/learn/quick-collect-azurevm.md)。  

若要了解支持的配置，请参阅[支持的 Windows 操作系统](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)和[网络防火墙配置](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements)。
 
如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-workspace"></a>创建工作区
1. 在 Azure 门户中，选择“所有服务”。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics”：

    ![Azure 门户](media/quick-collect-windows-computer/azure-portal-01.png)
  
2. 选择“创建”，然后提供以下详细信息：

   * 输入新 **Log Analytics 工作区**的名称， 例如 **DefaultLAWorkspace**。
   * 选择要链接到的**订阅**。 如果不想要使用默认订阅，请从列表中选择另一个订阅。
   * 对于“资源组”，请选择包含一个或多个 Azure 虚拟机的现有资源组。  
   * 选择向其部署 VM 的“位置”。 下面是[支持 Log Analytics 的区域](https://azure.microsoft.com/regions/services/)列表。  
   * 如果在 2018 年 4 月 2 日之后创建的订阅中创建工作区，该工作区会自动使用“按 GB”定价计划。 无法选择定价层。 如果在 2018 年 4 月 2 日之前创建的订阅中创建工作区，或者在绑定到现有 EA 注册的订阅中创建工作区，请选择要使用的定价层。 有关定价层的信息，请参阅 [Log Analytics 定价详细信息](https://azure.microsoft.com/pricing/details/log-analytics/)。

        ![创建 Log Analytics 资源](media/quick-collect-windows-computer/create-loganalytics-workspace-02.png)<br>  

3. 在“Log Analytics 工作区”窗格中提供所需的信息后，选择“确定”。  

在验证信息后创建工作区时，可以在菜单中的“通知”下跟踪操作进度。

## <a name="get-the-workspace-id-and-key"></a>获取工作区 ID 和密钥
在安装适用于 Windows 的 Microsoft Monitoring Agent 之前，需要先获得 Log Analytics 工作区的工作区 ID 和密钥。 安装向导需要使用此信息来正确配置代理，并确保它能与 Log Analytics 通信。  

1. 在 Azure 门户左上角选择“所有服务”。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics”。
2. 在 Log Analytics 工作区列表中，选择前面创建的工作区。 （可能已将其命名为 **DefaultLAWorkspace**。）
3. 选择“高级设置”：

    ![Log Analytics 高级设置](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
  
4. 选择“已连接的源”，然后选择“Windows 服务器”。
5. 复制“工作区 ID”和“主密钥”右侧的值。 将其粘贴到你偏爱的编辑器中。

## <a name="install-the-agent-for-windows"></a>安装适用于 Windows 的代理
以下步骤将安装并配置 Azure 和 Azure 政府版中用于 Log Analytics 的代理。 你将使用 Microsoft Monitoring Agent 安装程序在计算机上安装该代理。

1. 延续前面的步骤，在“Windows 服务器”页上，选择要下载的 **Windows 代理**版本。 选择适用于你的 Windows 操作系统处理器体系结构的版本。
2. 运行安装程序在计算机上安装该代理。
2. 在“欢迎”页上，选择“下一步”。
3. 在“许可条款”页面上阅读许可协议，然后选择“我接受”。
4. 在“目标文件夹”页面上更改或保留默认安装文件夹，然后选择“下一步”。
5. 在“代理安装选项”页上，将代理连接到 Azure Log Analytics，然后选择“下一步”。
6. 在“Azure Log Analytics”页上完成以下步骤：
   1. 粘贴前面复制的“工作区 ID”和“工作区密钥(主密钥)”。 如果计算机应向 Azure 政府版中的 Log Analytics 工作区报告，请在“Azure 云”下拉列表中选择“Azure 美国政府”。  
   2. 如果计算机需要通过代理服务器来与 Log Analytics 服务通信，请选择“高级”并提供代理服务器的 URL 和端口号。 如果代理服务器要求身份验证，请输入用于在代理服务器上进行身份验证的用户名和密码，然后选择“下一步”。  
7. 添加配置设置后，选择“下一步”：

    ![Microsoft Monitoring Agent 安装](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

8. 在“准备安装”页上检查所做的选择，并选择“安装”。
9. 在“配置已成功完成”页上，选择“完成”。

完成安装和设置后，Microsoft Monitoring Agent 将显示在控制面板中。 可以检查配置，并验证代理是否已连接到 Log Analytics。 连接后，在“Azure Log Analytics”选项卡上，代理将显示以下消息：Microsoft Monitoring Agent 已成功连接到 Microsoft Log Analytics 服务。<br><br> ![MMA 连接状态](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>收集的事件和性能数据
Log Analytics 可从 Windows 事件日志以及性能计数器中收集指定的事件用于长期分析和报告。 检测到特定的状况时，它还可以采取措施。 首先，请按照下列步骤操作，配置 Windows 事件日志以及几个常见性能计数器中收集事件。  

1. 在 Azure 门户左下角选择“更多服务”。 在搜索框中输入 **Log Analytics**。 键入时，列表会根据输入的内容进行筛选。 选择“Log Analytics”。
2. 选择“高级设置”：

    ![Log Analytics 高级设置](media/quick-collect-windows-computer/log-analytics-advanced-settings-01.png)
 
3. 选择“数据”，然后选择“Windows 事件日志”。  
4. 可通过输入日志名称来添加事件日志。 输入“系统”，然后选择加号 (**+**)。  
5. 在表中，选择“错误”和“警告”严重性。
6. 在页面顶部选择“保存”。
7. 选择“Windows 性能计数器”，在 Windows 计算机上启用性能计数器收集。
8. 首次为新的 Log Analytics 工作区配置 Windows 性能计数器时，可以选择快速创建几个通用的计数器。 每个选项将会列出，其旁边带有一个复选框：

    ![Windows 性能计数器](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    选择“添加所选性能计数器”。 随即会添加这些计数器，并为其预设 10 秒收集采样间隔。

9. 在页面顶部选择“保存”。

## <a name="view-collected-data"></a>查看收集的数据
启用数据收集后，让我们开始运行一个简单的日志搜索，以查看来自目标计算机的某些数据。  

1. 在 Azure 门户中的选定工作区内，选择“日志”磁贴。  
2. 在“日志搜索”窗格中的查询框内输入“性能”，然后单击查询框顶部的“运行”：
 
    ![Log Analytics 日志搜索](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    例如，下图中的查询返回了 735 条性能记录：

    ![Log Analytics 日志搜索结果](media/quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>清理资源
如果不再需要，可从计算机中删除该代理，并删除 Log Analytics 工作区。  

若要删除代理，请完成以下步骤：

1. 打开控制面板。
2. 打开“程序和功能”。
3. 在“程序和功能”中选择“Microsoft Monitoring Agent”，然后选择“卸载”。

若要删除前面创建的 Log Analytics 工作区，请将其选中，然后在资源页上选择“删除”：

![删除 Log Analytics 工作区](media/quick-collect-windows-computer/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>后续步骤
从 Windows 计算机收集操作和性能数据时，可以轻松地开始免费浏览、分析和处理收集的数据。  

若要了解如何查看和分析数据，请继续学习以下教程：

> [!div class="nextstepaction"]
> [在 Log Analytics 中查看或分析数据](tutorial-viewdata.md)
