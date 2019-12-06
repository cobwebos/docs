---
title: 排查 Azure FarmBeats 问题
description: 本文介绍了如何对 Azure FarmBeats 进行故障排除。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0a4fb337adfb2f4e6b8edb86ac620103e929c3a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842129"
---
# <a name="troubleshoot-azure-farmbeats"></a>排查 Azure FarmBeats 问题

本文提供常见 Azure FarmBeats 问题的解决方案。

有关更多帮助，请在 farmbeatssupport@microsoft.com与我们联系。 请确保电子邮件中包含 "部署部署*日志*文件"。

若要下载文件*日志*文件，请执行以下操作：

1. 选择突出显示的图标，然后在下拉列表中选择 "**下载**"。

    ![项目 FarmBeats](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

1. 在下一窗格中，输入你的部署*日志*文件的路径。 例如，输入**farmbeats-deployer**。

## <a name="sensor-telemetry"></a>传感器遥测

### <a name="cant-view-telemetry-data"></a>无法查看遥测数据

**症状**：设备或传感器已部署，并且你已将 FarmBeats 与设备合作伙伴链接，但无法在 FarmBeats 上获取或查看遥测数据。

**纠正操作**： 

1. 请参阅 FarmBeats Datahub 资源组。   
1. 选择**事件中心**（DatafeedEventHubNamespace），然后检查传入消息数。
1. 执行下列操作之一：   
   * 如果没有*传入消息*，请联系你的设备伙伴。  
   * 如果有*传入消息*，请联系 farmbeatssupport@microsoft.com。 附加 Datahub 和加速器日志，并捕获遥测数据。

若要了解如何下载日志，请参阅["手动收集日志"](#collect-logs-manually)部分。  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>没有 Azure 事件中心连接字符串

**纠正操作**： 

1. 在 Datahub Swagger 中转到合作伙伴 API。
1. 选择 "获取 ** >  > ** "**执行**"。
1. 请注意你感兴趣的传感器伙伴的合作伙伴 ID。
1. 返回到合作伙伴 API，并选择 "**获取/\<ID" >** 。
1. 指定步骤3中的合作伙伴 ID，然后选择 "**执行**"。
   
   API 响应应包含事件中心连接字符串。

### <a name="device-appears-offline"></a>设备显示脱机

**症状**：设备已安装，并且你已将 FarmBeats 链接到设备伙伴。 设备处于联机状态并发送遥测数据，但它们显示为脱机状态。

**纠正操作**：没有为此设备配置报告间隔。 若要设置报表间隔，请与设备制造商联系。 

### <a name="error-deleting-a-device"></a>删除设备时出错

删除设备时，可能会遇到以下常见错误方案之一：  

**消息**： "传感器中引用了设备：有一个或多个传感器与设备关联。 删除传感器，然后删除设备。 "  

**含义**：设备与在场中部署的多个传感器相关联。   

**纠正操作**：  

1. 通过加速器删除与设备关联的传感器。  
1. 如果要将传感器与其他设备关联，请要求设备伙伴执行相同操作。  
1. 使用 `DELETE API` 调用删除设备，并将 force 参数设置为*true*。  

**消息**： "设备在设备中被引用为 ParentDeviceId：有一个或多个设备作为子设备与此设备关联。 请删除它们，然后删除此设备。 "  

**意思**：你的设备具有与之关联的其他设备。  

**纠正操作**

1. 删除与此特定设备关联的设备。  
1. 删除特定设备。  

    > [!NOTE]
    > 如果传感器与设备相关联，则无法删除该设备。 有关如何删除关联的传感器的详细信息，请参阅[获取传感器合作伙伴的传感器数据](get-sensor-data-from-sensor-partner.md)部分中的 "删除传感器" 部分。


## <a name="issues-with-jobs"></a>作业问题

### <a name="farmbeats-internal-error"></a>FarmBeats 内部错误

**消息**： "FarmBeats 内部错误，请参阅故障排除指南以了解更多详细信息。"

**纠正操作**：此问题可能是由于数据管道中的暂时性故障导致的。 请重新创建该作业。 如果错误仍然存在，请在 FarmBeats 论坛上的文章中添加错误消息，或联系 FarmBeatsSupport@microsoft.com。

## <a name="accelerator-troubleshooting"></a>加速器疑难解答

### <a name="access-control"></a>访问控制

**问题**：你在添加角色分配时收到错误。

**消息**： "找不到匹配的用户"。

**纠正操作**：检查要为其添加角色分配的电子邮件 ID。 电子邮件 ID 必须与在 Active Directory 中为该用户注册的 ID 完全匹配。 如果错误仍然存在，请在 FarmBeats 论坛上的文章中添加错误消息，或联系 FarmBeatsSupport@microsoft.com。

### <a name="unable-to-log-in-to-accelerator"></a>无法登录到加速器

**消息**： "错误：你无权调用服务。 请与管理员联系以获得授权。 "

**纠正操作**：请求管理员授权你访问 FarmBeats 部署。 为此，可通过在快捷键的 "**设置**" 窗格中执行 RoleAssignment Api 或访问控制来完成。  

如果已授予访问权限，并且遇到此错误，请通过刷新页面重试。 如果错误仍然存在，请在 FarmBeats 论坛上的文章中添加错误消息，或联系 FarmBeatsSupport@microsoft.com。

![项目 FarmBeats](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>加速器问题  

**问题**：您收到了一个加速器错误，导致无法确定的原因。

**消息**： "错误：发生未知错误。"

**纠正操作**：如果使页处于空闲状态的时间太长，则会出现此错误。 刷新页面。  

如果错误仍然存在，请在 FarmBeats 论坛上的文章中添加错误消息，或联系 FarmBeatsSupport@microsoft.com。

**问题**： FarmBeats 加速器不显示最新版本，即使在升级 FarmBeatsDeployment 之后。

**纠正操作**：此错误是由于浏览器中的服务辅助角色持久性导致的。 请执行以下操作：
1. 关闭所有已打开加速器的浏览器选项卡，然后关闭浏览器窗口。 
1. 启动浏览器的新实例，并重新加载加速器 URI。 此操作将加载新版本的加速器。

## <a name="sentinel-imagery-related-issues"></a>Sentinel：与图像相关的问题

### <a name="wrong-username-or-password"></a>错误的用户名或密码

**作业失败消息**： "需要完全身份验证才能访问此资源"。

**纠正操作**： 

执行下列操作之一：
* 重新运行安装程序，以便升级具有正确用户名和密码的 Datahub。
* 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后查看作业是否成功。

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub：错误的 URL 或站点不可访问 

**作业失败消息**： "糟糕，出现了问题。 您尝试访问的页面暂时不可用。 " 

**纠正操作**：
1. 在浏览器中打开[Sentinel](https://scihub.copernicus.eu/dhus/) ，查看网站是否可访问。 
1. 如果无法访问该网站，请检查是否有任何防火墙、公司网络或其他阻止软件阻止访问该网站，然后执行必要的步骤以允许使用 Sentinel URL。 
1. 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后查看作业是否成功。  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel 服务器：按下进行维护

**作业失败消息**： "Copernicus 开放式访问中心即将恢复！ 抱歉，我们目前正在进行维护。 稍后我们将重新联机！ " 

**纠正操作**：

如果在 Sentinel 服务器上执行了任何维护活动，则会出现此问题。

1. 如果任何作业或管道由于正在执行维护而失败，请在一段时间后重新提交作业。 

   有关任何计划内或非计划的 Sentinel 维护活动的信息，请访问[Copernicus 开放式 Access Hub 新闻](https://scihub.copernicus.eu/news/)站点。  
1. 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后查看作业是否成功。

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel：达到了最大连接数

**作业失败消息**： "用户 '\<用户名 > ' 实现的两个并发流的最大数目。"

**这意味着**，如果由于已达到连接的最大数量而导致作业失败，则将在另一个软件部署中使用同一个 Sentinel 帐户。

**纠正操作**：尝试下列任一操作：
* 创建新的 Sentinel 帐户，然后重新运行安装程序以使用新的 Sentinel 用户名和密码升级 Datahub。  
* 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后查看作业是否成功。

### <a name="sentinel-server-refused-connection"></a>Sentinel 服务器：已拒绝连接 

**作业失败消息**： "服务器拒绝连接： http://172.30.175.69:8983/solr/dhus 。" 

**纠正操作**：如果在 Sentinel 服务器上执行了任何维护活动，则会出现此问题。 
1. 如果任何作业或管道由于正在执行维护而失败，请在一段时间后重新提交作业。 

   有关任何计划内或非计划的 Sentinel 维护活动的信息，请访问[Copernicus 开放式 Access Hub 新闻](https://scihub.copernicus.eu/news/)站点。  
1. 重新运行失败的作业，或在5到7天的日期范围内运行附属索引作业，然后查看作业是否成功。

## <a name="collect-logs-manually"></a>手动收集日志

[安装并部署 Azure 存储资源管理器]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)。

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>收集 Datahub 中的 Azure 数据工厂作业日志
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在**搜索**框中，搜索 FarmBeats Datahub 资源组。

    > [!NOTE]
    > 选择在 FarmBeats 部署过程中指定的 Datahub 资源组。

1. 在**资源组**仪表板上，搜索*datahublogs\** 存储帐户。 例如，搜索**datahublogsmvxmq**。  

1. 在 "**名称**" 列中，选择要查看**存储**帐户仪表板的存储帐户。
1. 在**datahubblogs\*** 窗格中，选择 "**在资源管理器中打开**" 以查看**打开 Azure 存储资源管理器**应用程序。
1. 在左窗格中，选择 " **Blob 容器**"，然后选择 "**作业日志**"。
1. 在 "**作业日志**" 窗格中，选择 "**下载**"。
1. 将日志下载到计算机上的本地文件夹。
1. 通过电子邮件将下载的 .zip 文件发送到 farmbeatssupport@microsoft.com。

    ![项目 FarmBeats](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>在加速器中收集 Azure 数据工厂作业日志 

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在**搜索**框中，搜索 FarmBeats 加速器资源组。

    > [!NOTE]
    > 选择在 FarmBeats 部署过程中指定的加速器资源组。

1. 在**资源组**仪表板上，搜索*存储\** 存储帐户。 例如，搜索**storagedop4k\*** 。
1. 在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
1. 在**存储\*** 窗格中，选择 "**在资源管理器中打开**" 以打开 Azure 存储资源管理器应用程序。
1. 在左窗格中，选择 " **Blob 容器**"，然后选择 "**作业日志**"。
1. 在 "**作业日志**" 窗格中，选择 "**下载**"。
1. 将日志下载到计算机上的本地文件夹。
1. 通过电子邮件将下载的 .zip 文件发送到 farmbeatssupport@microsoft.com。


### <a name="collect-datahub-app-service-logs"></a>收集 Datahub 应用服务日志

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在**搜索**框中，搜索 FarmBeats Datahub 资源组。

    > [!NOTE]
    > 选择在 FarmBeats 部署过程中指定的 Datahub 资源组。

1. 在资源组中，搜索*datahublogs\** 存储帐户。 例如，搜索**fordatahublogsmvxmq\*** 。
1. 在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
1. 在**datahubblogs\*** 窗格中，选择 "**在资源管理器中打开**" 以打开 Azure 存储资源管理器应用程序。
1. 在左窗格中，选择 " **Blob 容器**"，然后选择 " **appinsights**"。
1. 在 " **appinsights** " 窗格中，选择 "**下载**"。
1. 将日志下载到计算机上的本地文件夹。
1. 通过电子邮件将下载的 .zip 文件发送到 farmbeatssupport@microsoft.com。

### <a name="collect-accelerator-app-service-logs"></a>收集加速器应用服务日志

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在**搜索**框中，搜索 FarmBeats 加速器资源组。

    > [!NOTE]
    > 选择在 FarmBeats 部署期间提供的 FarmBeats 加速器资源组。

1. 在资源组中，搜索*存储\** 存储帐户。 例如，搜索**storagedop4k\*** 。
1. 在 "**名称**" 列中选择存储帐户，查看**存储帐户**仪表板。
1. 在**存储\*** 窗格中，选择 "**在资源管理器中打开**" 以打开 Azure 存储资源管理器应用程序。
1. 在左窗格中，选择 " **Blob 容器**"，然后选择 " **appinsights**"。
1. 在 " **appinsights** " 窗格中，选择 "**下载**"。
1. 将日志下载到计算机上的本地文件夹。
1. 通过电子邮件将下载的文件夹发送到 farmbeatssupport@microsoft.com。

## <a name="known-issues"></a>已知问题

## <a name="batch-related-issues"></a>与批处理相关的问题

**错误消息**： "已达到指定订阅的 Batch 帐户的区域帐户配额"。

**纠正操作**：增加配额，或删除未使用的批处理帐户并重新运行部署。

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory （Azure AD）相关问题

**错误消息**： "无法将所需设置更新为 Azure AD 应用 d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0：权限不足，无法完成操作。 确保为 Azure AD 应用正确地配置了上述设置。 "

**意思**： Azure AD 应用注册配置未正确完成。  

**纠正操作**：向 IT 管理员（具有租户读取访问权限的人员）询问用于创建 Azure AD 应用注册的[脚本](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts)。 此脚本还会自动执行配置步骤。

**错误消息**： "无法在此租户中创建新的 Active Directory 应用程序"\<应用程序名称\>"：另一个具有相同属性标识符 uri 值的对象已存在。"

**意思**：已经存在具有相同名称的 Azure AD 应用注册。

**纠正操作**：删除现有 Azure AD 应用注册，或重复使用它进行安装。 如果要重复使用现有 Azure AD 应用注册，请将应用程序 ID 和客户端密码传递到安装程序并重新部署。

## <a name="issues-with-the-inputjson-file"></a>输入 json 文件的问题

**错误**：从*输入 json*文件读取输入时出错。

**纠正操作**：此问题通常是由于为安装程序指定了正确的*输入*文件路径或名称时出错引起的。 做出适当的更正，然后重试重新部署。

**错误**：分析*输入*文件中的值时出错。

**纠正操作**：此问题主要是由于*输入 json*文件中的值不正确引起的。 做出适当的更正，然后重试部署。

## <a name="high-cpu-usage"></a>CPU 使用率较高

**错误**：收到一个表示*CPU 使用率较高*的电子邮件警报。 

**纠正操作**： 
1. 请参阅 FarmBeats Datahub 资源组。
1. 选择应用服务。  
1. 请参阅向上缩放[应用服务定价页](https://azure.microsoft.com/pricing/details/app-service/windows/)，然后选择适当的定价层。

## <a name="next-steps"></a>后续步骤

如果仍然遇到 FarmBeats 问题，请联系我们的[支持论坛](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats)。
