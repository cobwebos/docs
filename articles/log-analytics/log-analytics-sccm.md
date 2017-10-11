---
title: "将配置管理器连接到 Log Analytics | Microsoft Docs"
description: "本文介绍将配置管理器连接到 Log Analytics 以及开始分析数据的步骤。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: banders
ms.openlocfilehash: 62d31ed486458245156f7fc832294d662c62991e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="connect-configuration-manager-to-log-analytics"></a>将配置管理器连接到 Log Analytics
可以将 System Center Configuration Manager 连接到 Log Analytics 以同步设备集合数据。 这就使得来自配置管理器层次结构的数据可在 OMS 中使用。

## <a name="prerequisites"></a>先决条件

Log Analytics 支持 System Center Configuration Manager 当前分支，版本 1606 和更高版本。  

## <a name="configuration-overview"></a>配置概述
以下步骤总结了将配置管理器连接到 Log Analytics 的过程。  

1. 在 Azure 管理门户中，将配置管理器注册为 Web 应用程序和/或 Web API 应用程序，并确保有在 Azure Active Directory 中进行注册时收到的客户端 ID 和客户端密钥。 如需了解如何完成此步骤的详细信息，请参阅[使用门户创建可访问资源的 Active Directory 应用程序和服务主体](../azure-resource-manager/resource-group-create-service-principal-portal.md)。
2. 在 Azure 管理门户中，[为配置管理器（已注册的 Web 应用）提供访问 OMS 的权限](#provide-configuration-manager-with-permissions-to-oms)。
3. 在配置管理器中，[使用添加 OMS 连接向导来添加连接](#add-an-oms-connection-to-configuration-manager)。
4. 在配置管理器中，如果密码或客户端密钥曾过期或丢失，可以[更新连接属性](#update-oms-connection-properties)。
5. 使用 OMS 门户中的信息，在运行配置管理器服务连接点站点系统角色的计算机上[下载并安装 Microsoft Monitoring Agent](#download-and-install-the-agent)。 代理将配置管理器数据发送到 OMS。
6. 在 Log Analytics 中，以计算机组的形式[从配置管理器导入收集的数据](#import-collections)。
7. 在 Log Analytics 中，以[计算机组](log-analytics-computer-groups.md)的形式查看配置管理器中的数据。

如需了解有关将配置管理器连接到 OMS 的详细信息，请参阅[将配置管理器中的数据同步到 Microsoft Operations Management Suite](https://technet.microsoft.com/library/mt757374.aspx)。

## <a name="provide-configuration-manager-with-permissions-to-oms"></a>为配置管理器提供访问 OMS 的权限
下列过程介绍如何为 Azure 管理门户提供访问 OMS 的权限。 具体而言，必须向资源组中的用户授予*参与者角色*，以允许 Azure 管理门户将配置管理器连接到 OMS。

> [!NOTE]
> 必须为配置管理器指定 OMS 中的权限。 否则，在配置管理器中使用配置向导时会收到一条错误消息。
>
>

1. 打开 [Azure 门户](https://portal.azure.com/)，并单击“浏览” > “Log Analytics (OMS)”，打开 Log Analytics (OMS) 边栏选项卡。  
2. 在“**Log Analytics (OMS)**”边栏选项卡上，单击“**添加**”，打开“**OMS 工作区**”边栏选项卡。  
   ![OMS 边栏选项卡](./media/log-analytics-sccm/sccm-azure01.png)
3. 在“OMS 工作区”边栏选项卡上，提供以下信息，并单击“确定”。

   * **OMS 工作区**
   * **订阅**
   * **资源组**
   * **位置**
   * **定价层**  
     ![OMS 边栏选项卡](./media/log-analytics-sccm/sccm-azure02.png)  

     > [!NOTE]
     > 以上示例将创建一个新的资源组。 在本示例中，该资源组仅用于为配置管理器提供对 OMS 工作区的访问权限。
     >
     >
4. 单击“浏览” > “资源组”，打开“资源组”边栏选项卡。
5. 在“资源组”边栏选项卡中，单击上面创建的资源组，打开资源组名称&lt;&gt;设置边栏选项卡。  
   ![资源组设置边栏选项卡](./media/log-analytics-sccm/sccm-azure03.png)
6. 在“资源组名称”&lt;&gt;设置边栏选项卡中，单击“访问控制 (IAM)”，以打开“资源组名称”&lt;&gt;用户边栏选项卡。  
   ![资源组用户边栏选项卡](./media/log-analytics-sccm/sccm-azure04.png)  
7. 在“资源组名称”&lt;&gt;用户边栏选项卡中，单击“添加”以打开“添加访问”边栏选项卡。
8. 在“添加访问”边栏选项卡中，单击“选择角色”，并选择“参与者”角色。  
   ![选择角色](./media/log-analytics-sccm/sccm-azure05.png)  
9. 单击“添加用户”，选择配置管理器用户，单击“选择”，并单击“确定”。  
   ![添加用户](./media/log-analytics-sccm/sccm-azure06.png)  

## <a name="add-an-oms-connection-to-configuration-manager"></a>将 OMS 连接添加到配置管理器
若要添加 OMS 连接，配置管理器环境必须有针对联机模式配置的服务连接点[](https://technet.microsoft.com/library/mt627781.aspx)。

1. 在配置管理器的“管理”工作区中，选择“OMS 连接器”。 这会打开**添加 OMS 连接向导**。 选择“下一步”。
2. 上**常规**屏幕上，确认已完成以下操作，并且你具有每个项的详细信息，然后选择**下一步**。

   1. 在 Azure 管理门户中，已经将 Configuration Manager 注册为 Web 应用和/或 Web API 应用，并且有[在注册时收到的客户端 ID](../active-directory/active-directory-integrating-applications.md)。
   2. 在 Azure 管理门户中，已经为 Azure Active Directory 中注册的应用创建了应用密钥。  
   3. 在 Azure 管理门户中，已为注册的 Web 应用提供访问 OMS 的权限。  
      ![连接到 OMS 向导常规页](./media/log-analytics-sccm/sccm-console-general01.png)
3. 在“**Azure Active Directory**”屏幕上，通过提供“**租户**”、“**客户端 ID**”，以及“**客户端密钥**”来配置到 OMS 的连接设置，并选择“**下一步**”。  
   ![连接到 OMS 向导 Azure Active Directory 页](./media/log-analytics-sccm/sccm-wizard-tenant-filled03.png)
4. 如果已成功完成所有其他过程，则“**OMS 连接配置**”屏幕上的信息会自动出现在此页中。 其中应显示用于“Azure 订阅”、“Azure 资源组”，以及“Operations Management Suite 工作区”的连接设置信息。  
   ![连接到 OMS 向导 OMS 连接页](./media/log-analytics-sccm/sccm-wizard-configure04.png)
5. 此向导将使用已输入的信息连接到 OMS 服务。 选择要与 OMS 同步的设备集合，并单击“**添加**”。  
   ![选择集合](./media/log-analytics-sccm/sccm-wizard-add-collections05.png)
6. 在“摘要”屏幕验证连接设置，并选择“下一步”。 “**进度**屏幕”会显示连接状态，最终应为**完成**。

> [!NOTE]
> 必须将 OMS 连接到层次结构中的顶层站点。 如果先将 OMS 连接到独立主站点，再将管理中心站点添加到你的环境，则必须在新层次结构中删除并重新创建 OMS 连接。
>
>

将配置管理器链接到 OMS 后，可以添加或删除集合，并查看 OMS 连接的属性。

## <a name="update-oms-connection-properties"></a>更新 OMS 连接属性
如果密码或客户端密钥曾过期或丢失，将需要手动更新 OMS 连接属性。

1. 在配置管理器中，导航到“**云服务**”，并选择“**OMS 连接器**”，打开“**OMS 连接属性**”页。
2. 在此页中，单击“**Azure Active Directory**”选项卡，查看“**租户**”、“**客户端 ID**”、“**客户端密钥过期**”。 如果“客户端密钥”已过期，则对其进行“验证”。

## <a name="download-and-install-the-agent"></a>下载并安装代理
1. 在 OMS 门户中，从 OMS 下载代理安装程序文件。[](log-analytics-windows-agents.md#download-the-agent-setup-file-from-oms)
2. 可使用以下方法之一，在运行配置管理器服务连接点站点系统角色的计算机上安装和配置代理：
   * [使用安装程序安装代理](log-analytics-windows-agents.md#install-the-agent-using-setup)
   * [使用命令行安装代理](log-analytics-windows-agents.md#install-the-agent-using-the-command-line)
   * [使用 Azure 自动化中的 DSC 安装代理](log-analytics-windows-agents.md#install-the-agent-using-dsc-in-azure-automation)

## <a name="import-collections"></a>导入集合
将 OMS 连接添加到配置管理器并在运行配置管理器服务连接点站点系统角色的计算机上安装代理之后，下一步是将集合以计算机组的形式从配置服务器导入 OMS 中。

启用导入后，每隔 3 小时检索一次集合成员身份信息，以保持最新的集合成员身份。 可以随时选择禁用导入。

1. 在 OMS 门户中，单击“设置”。
2. 单击“**计算机组**”选项卡，并单击“**SCCM**”选项卡。
3. 选择“导入 Configuration Manager 集合成员身份”，并单击“保存”。  
   ![计算机组 - SCCM 选项卡](./media/log-analytics-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>查看配置管理器中的数据
将 OMS 连接添加到配置管理器并在运行配置管理器服务连接点站点系统角色的计算机上安装了代理之后，来自代理的数据将发送到 OMS。 在 OMS 中，配置管理器集合[以计算机组](log-analytics-computer-groups.md)的形式显示。 可以从“设置”中“计算机组”下方的“配置管理器”页查看这些组。

在导入集合后，可以看到已检测到的具有集合成员身份的计算机数。 此外还可以看到已导入的集合数。

![计算机组 - SCCM 选项卡](./media/log-analytics-sccm/sccm-computer-groups02.png)

单击上面任意一项，都会打开“搜索”，相应显示所有已导入的组，或者属于每个组的所有计算机。 使用[日志搜索](log-analytics-log-searches.md)，可以针对配置管理器数据开始深度分析。

## <a name="next-steps"></a>后续步骤
* 使用[日志搜索](log-analytics-log-searches.md)查看有关配置管理器数据的详细信息。
