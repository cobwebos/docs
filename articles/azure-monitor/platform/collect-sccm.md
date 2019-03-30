---
title: 连接到 Azure 监视的配置管理器 |Microsoft Docs
description: 本文介绍的步骤将配置管理器连接到 Azure Monitor 中的工作区，并开始分析数据。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: e5cf89b7fe01946de9944a7026ec448cd55dd6dc
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630671"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>连接到 Azure 监视的配置管理器
可以同步设备集合数据将 System Center Configuration Manager 环境连接到 Azure Monitor 并引用 Azure Monitor 和 Azure 自动化中的这些集合。  

## <a name="prerequisites"></a>必备组件

Azure Monitor 支持 System Center Configuration Manager current branch 版本 1606年和更高版本。  

## <a name="configuration-overview"></a>配置概述
以下步骤总结了使用 Azure Monitor 配置 Configuration Manager 集成的步骤。  

1. 在 Azure 门户中，将配置管理器注册为 Web 应用程序和/或 Web API 应用，并确保有在 Azure Active Directory 中进行注册时收到的客户端 ID 和客户端密钥。 如需了解如何完成此步骤的详细信息，请参阅[使用门户创建可访问资源的 Active Directory 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。
2. 在 Azure 门户中，[授予 Configuration Manager （已注册的 web 应用） 访问 Azure Monitor 的权限](#grant-configuration-manager-with-permissions-to-log-analytics)。
3. 在配置管理器中，使用“添加 OMS 连接”向导来添加连接。
4. 在配置管理器中，如果密码或客户端密钥曾过期或丢失，可以更新连接属性。
5. 在运行 Configuration Manager 服务连接点站点系统角色的计算机上[下载并安装 Microsoft Monitoring Agent](#download-and-install-the-agent)。 代理将 Configuration Manager 数据发送到 Azure Monitor 中的 Log Analytics 工作区。
6. 在 Azure Monitor[导入集合从 Configuration Manager](#import-collections)为计算机组。
7. 在 Azure 监视器中查看数据从 Configuration Manager 作为[的计算机组](computer-groups.md)。

你可以阅读更多有关 Configuration Manager 连接到在 Azure Monitor[数据从 Configuration Manager 同步到 Microsoft Log Analytics 工作区](https://technet.microsoft.com/library/mt757374.aspx)。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>为 Configuration Manager 授予访问 Log Analytics 的权限
在以下过程中，你将在 Log Analytics 工作区中，向前面为 Configuration Manager 创建的 AD 应用程序和服务主体授予“参与者”角色。  如果你还没有一个工作区，请参阅[Azure Monitor 中创建一个工作区](../../azure-monitor/learn/quick-create-workspace.md)继续操作之前。  这样，Configuration Manager 便可以执行身份验证并连接到 Log Analytics 工作区。  

> [!NOTE]
> 您必须指定权限的 Log Analytics 工作区中为 Configuration Manager。 否则，在配置管理器中使用配置向导时会收到一条错误消息。
>

1. 在 Azure 门户中，单击左上角的“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
2. 在 Log Analytics 工作区列表中，选择要修改的工作区。
3. 在左窗格中，选择“访问控制(IAM)”。
4. 在“访问控制(IAM)”页面中，单击“添加角色分配”，随即出现“添加角色分配”窗格。
5. 在“添加角色分配”窗格中的“角色”下拉列表内，选择“参与者”角色。  
6. 在“将访问权限分配到”下拉列表中，选择前面在 AD 中创建的 Configuration Manager 应用程序，然后单击“确定”。  

## <a name="download-and-install-the-agent"></a>下载并安装代理
查看文章[连接 Windows 计算机连接到 Azure 中的 Azure Monitor](agent-windows.md)若要了解可用于承载 Configuration Manager 服务连接点的计算机上安装 Microsoft Monitoring Agent 的方法站点系统角色。  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>将 Log Analytics 连接添加到配置管理器
若要添加 Log Analytics 连接，配置管理器环境必须有针对联机模式配置的[服务连接点](https://technet.microsoft.com/library/mt627781.aspx)。

1. 在配置管理器的“管理”工作区中，选择“OMS 连接器”。 这会打开**添加 Log Analytics 连接向导**。 选择“**下一步**”。

   >[!NOTE]
   >OMS 现称为 Log Analytics 是 Azure Monitor 的一项功能。
   
2. 在“常规”屏幕上，确认已完成以下操作，并且具有每个项的详细信息，然后选择“下一步”。

   1. 在 Azure 门户中，已经将配置管理器注册为 Web 应用程序和/或 Web API 应用，并且有[在注册时收到的客户端 ID](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md)。
   2. 在 Azure 门户中，已经为 Azure Active Directory 中注册的应用创建了应用密钥。  
   3. 在 Azure 门户中，已提供注册的 web 应用的权限访问 Azure Monitor 中的 Log Analytics 工作区。  
      ![连接到 Log Analytics 向导常规页](./media/collect-sccm/sccm-console-general01.png)
3. 上**Azure Active Directory**屏幕上，配置到 Log Analytics 工作区的连接设置，从而您**租户**，**客户端 ID**，以及**客户端密钥**，然后选择**下一步**。  
   ![连接到 Log Analytics 向导 Azure Active Directory 页](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. 如果已成功完成所有其他过程，则“**OMS 连接配置**”屏幕上的信息会自动出现在此页中。 其中应显示用于“Azure 订阅”、“Azure 资源组”，以及“Operations Management Suite 工作区”的连接设置信息。  
   ![连接到 Log Analytics 向导 Log Analytics 连接页](./media/collect-sccm/sccm-wizard-configure04.png)
5. 向导将连接到 Log Analytics 工作区使用已输入的信息。 选择要与服务同步的设备集合，并单击“添加”。  
   ![选择集合](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. 在“摘要”屏幕验证连接设置，并选择“下一步”。 “**进度**屏幕”会显示连接状态，最终应为**完成**。

> [!NOTE]
> 必须将顶层站点连接到 Azure Monitor 层次结构中。 如果您连接到 Azure Monitor 的独立主站点并再将管理中心站点添加到你的环境，你必须删除并重新创建新的层次结构中的连接。
>
>

配置管理器链接到 Azure Monitor 后，可以添加或删除集合，并查看连接的属性。

## <a name="update-log-analytics-workspace-connection-properties"></a>更新 Log Analytics 工作区连接属性
如果密码或客户端机密密钥曾过期或丢失，将需要手动更新 Log Analytics 连接属性。

1. 在配置管理器中，导航到“云服务”，并选择“OMS 连接器”，打开“OMS 连接属性”页。
2. 在此页中，单击“**Azure Active Directory**”选项卡，查看“**租户**”、“**客户端 ID**”、“**客户端密钥过期**”。 如果“客户端密钥”已过期，则对其进行“验证”。

## <a name="import-collections"></a>导入集合
已添加到 Configuration Manager 的 Log Analytics 连接并安装代理之后运行 Configuration Manager 服务连接的计算机上点的站点系统角色下, 一步是导入集合从 Configuration Manager 在 Azure 中监视计算机组。

完成从层次结构导入设备连接的初始配置后，每隔 3 小时检索一次集合成员身份信息，以保持最新的集合成员身份。 随时可以选择禁用此功能。

1. 在 Azure 门户中，单击左上角的“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。
2. 在 Log Analytics 工作区列表中，选择 Configuration Manager 注册到的工作区。  
3. 选择“高级设置”。
4. 依次选择“计算机组”、“SCCM”。  
5. 选择“导入 Configuration Manager 集合成员身份”，并单击“保存”。  
   ![计算机组 - SCCM 选项卡](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>查看配置管理器中的数据
已添加到 Configuration Manager 的 Log Analytics 连接并运行 Configuration Manager 服务连接点站点系统角色的计算机上安装了代理后，来自代理的数据发送到 Azure Monitor 中的 Log Analytics 工作区。 在 Azure Monitor Configuration Manager 集合将显示为[的计算机组](../../azure-monitor/platform/computer-groups.md)。 可以从“设置”>“计算机组”下的“Configuration Manager”页查看这些组。

在导入集合后，可以看到已检测到的具有集合成员身份的计算机数。 此外还可以看到已导入的集合数。

![计算机组 - SCCM 选项卡](./media/collect-sccm/sccm-computer-groups02.png)

单击上面任意一项，都会打开“搜索”，相应显示所有已导入的组，或者属于每个组的所有计算机。 使用[日志搜索](../../azure-monitor/log-query/log-query-overview.md)，可以针对配置管理器数据开始深度分析。

## <a name="next-steps"></a>后续步骤
* 使用[日志搜索](../../azure-monitor/log-query/log-query-overview.md)查看有关配置管理器数据的详细信息。
