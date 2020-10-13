---
title: 将 Configuration Manager 连接到 Azure Monitor |Microsoft Docs
description: 本文介绍将 Configuration Manager 连接到 Azure Monitor 中的工作区以及开始分析数据的步骤。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 20d55de7ef0f7b7f49b922409f8cb61874fae0f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448151"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>将 Configuration Manager 连接到 Azure Monitor
可将 Microsoft Endpoint Configuration Manager 环境连接到 Azure Monitor 以同步设备集合数据，并在 Azure Monitor 和 Azure 自动化中引用这些集合。  

## <a name="prerequisites"></a>必备条件

Azure Monitor 支持 Configuration Manager 当前分支（版本 1606 及更高版本）。

>[!NOTE]
>连接 Configuration Manager 和 Log Analytics 工作区的功能是可选的且在默认情况下未启用。 必须在使用前启用此功能。 有关详细信息，请参阅[启用更新中的可选功能](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options)。

## <a name="configuration-overview"></a>配置概述

以下步骤总结了使用 Azure Monitor 配置 Configuration Manager 的步骤。  

1. 在 Azure Active Directory 中，将配置管理器注册为 Web 应用程序和/或 Web API 应用，并确保有在 Azure Active Directory 中进行注册时收到的客户端 ID 和客户端密钥。 如需了解如何完成此步骤的详细信息，请参阅[使用门户创建可访问资源的 Active Directory 应用程序和服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)。

2. 在 Azure Active Directory 中，[授予 Configuration Manager（已注册的 Web 应用）访问 Azure Monitor 的权限](#grant-configuration-manager-with-permissions-to-log-analytics)。

3. 在 Configuration Manager 中，使用“Azure 服务”向导来添加连接。****

4. 在运行 Configuration Manager 服务连接点站点系统角色的计算机上[下载并安装用于 Windows 的 Log Analytics 代理](#download-and-install-the-agent)。 该代理将 Configuration Manager 数据发送到 Azure Monitor 中的 Log Analytics 工作区。

5. 在 Azure Monitor 中，以计算机组的形式[从 Configuration Manager 导入集合](#import-collections)。

6. 在 Azure Monitor 中，以[计算机组](computer-groups.md)的形式从 Configuration Manager 查看数据。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>为 Configuration Manager 授予访问 Log Analytics 的权限

在以下过程中，你将在 Log Analytics 工作区中，向前面为 Configuration Manager 创建的 AD 应用程序和服务主体授予“参与者”角色。** 如果尚未创建工作区，请参阅[在 Azure Monitor 中创建工作区](../learn/quick-create-workspace.md)，然后继续。 这样，Configuration Manager 便可以执行身份验证并连接到 Log Analytics 工作区。  

> [!NOTE]
> 必须为 Configuration Manager 指定 Log Analytics 工作区中的权限。 否则，在配置管理器中使用配置向导时会收到一条错误消息。
>

1. 在 Azure 门户中，单击左上角的“所有服务”****。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。

2. 在 Log Analytics 工作区列表中，选择要修改的工作区。

3. 在左窗格中，选择“访问控制(IAM)”。****

4. 在“访问控制(IAM)”页面中，单击“添加角色分配”，随即出现“添加角色分配”窗格********。

5. 在“添加角色分配”窗格中的“角色”下拉列表内，选择“参与者”角色。************  

6. 在“将访问权限分配到”下拉列表中，选择前面在 AD 中创建的 Configuration Manager 应用程序，然后单击“确定”。********  

## <a name="download-and-install-the-agent"></a>下载并安装代理

查看[将 Windows 计算机连接到 Azure 中的 Azure Monitor 服务](agent-windows.md)一文，了解在托管 Configuration Manager 服务连接点站点系统角色的计算机上安装用于 Windows 的 Log Analytics 代理的可用方法。  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>将 Configuration Manager 连接到 Log Analytics 工作区

>[!NOTE]
> 若要添加 Log Analytics 连接，配置管理器环境必须有针对联机模式配置的[服务连接点](/configmgr/core/servers/deploy/configure/about-the-service-connection-point)。

> [!NOTE]
> 必须将层次结构中的顶层站点连接到 Azure Monitor。 如果先将独立主站点连接到 Azure Monitor，再将管理中心站点添加到你的环境，则必须在新层次结构中删除并重新创建连接。

1. 在 Configuration Manager 的“管理”**** 工作区中选择“云服务”****，然后选择“Azure 服务”。**** 

2. 右键单击“Azure 服务”，然后选择“配置 Azure 服务”。******** 此时会显示“配置 Azure 服务”**** 页。 
   
3. 在“常规”**** 屏幕上，确认已完成以下操作，并且具有每个项的详细信息，然后选择“下一步”****。

4. 在 Azure 服务向导的“Azure 服务”页上，执行以下操作：

    1. 指定 Configuration Manager 中的对象名称。
    2. 指定可选说明以帮助标识服务。
    3. 选择 Azure 服务 **OMS 连接器**

    >[!NOTE]
    >OMS 现在称为 Log Analytics，是 Azure Monitor 的一项功能。

5. 选择“下一步”继续转至 Azure 服务向导的 Azure 应用属性页。

6. 在 Azure 服务向导的“应用”页上，首先从列表中选择 Azure 环境，然后单击“导入”。********

7. 在“导入应用”页上，指定以下信息****：

    1. 指定应用的“Azure AD 租户名称”。****

    2. 对于“Azure AD 租户 ID”，请指定 Azure AD 租户。**** 可以在 Azure Active Directory 的“属性”页上找到此信息。**** 

    3. 对于“应用程序名称”，请指定应用程序名称。****

    4. 对于“客户端 ID”，请指定以前创建的 Azure AD 应用的应用程序 ID。****

    5. 对于“机密密钥”，请指定已创建的 Azure AD 应用的客户端机密密钥。****

    6. 对于“机密密钥到期时间”，请指定密钥的到期日期。****

    7. 对于“应用 ID URI”，请指定以前创建的 Azure AD 应用的应用 ID URI。****

    8. 选择“验证”，**** 此时右侧的结果会显示“成功验证!”。****

8. 在“配置”页上查看相关信息，验证“Azure 订阅”、“Azure 资源组”，以及“Operations Management Suite 工作区”字段是否已预先填充。如果已预先填充，则表明 Azure AD 应用程序在资源组中有足够的权限。**************** 如果字段为空，则表明应用程序没有所需权限。 选择要收集并转发到工作区的设备集合，然后选择“添加”。****

9. 查看“确认设置”页上的选项，然后选择“下一步”，开始创建并配置连接。********

10. 配置完成后，会显示“完成”页。**** 选择“关闭”。 

将 Configuration Manager 链接到 Azure Monitor 后，可以添加或删除集合，并查看连接的属性。

## <a name="update-log-analytics-workspace-connection-properties"></a>更新 Log Analytics 工作区连接属性

如果密码或客户端机密密钥过期或丢失，需要手动更新 Log Analytics 连接属性。

1. 在 Configuration Manager 的“管理”工作区中选择“云服务”****，然后选择“OMS 连接器”****，打开“OMS 连接属性”******** 页。
2. 在此页中，单击“**Azure Active Directory**”选项卡，查看“**租户**”、“**客户端 ID**”、“**客户端密钥过期**”。 如果“客户端密钥”**** 已过期，则对其进行“验证”****。

## <a name="import-collections"></a>导入集合

将 Log Analytics 连接添加到 Configuration Manager 并在运行 Configuration Manager 服务连接点站点系统角色的计算机上安装代理之后，下一步是将集合以计算机组的形式从配置服务器导入 Azure Monitor 中。

完成从层次结构导入设备连接的初始配置后，每隔 3 小时检索一次集合信息，以保持最新的集合成员身份。 随时可以选择禁用此功能。

1. 在 Azure 门户中，单击左上角的“所有服务”****。 在资源列表中，键入“Log Analytics”。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics 工作区”。
2. 在 Log Analytics 工作区列表中，选择 Configuration Manager 注册到的工作区。  
3. 选择“高级设置”。
4. 依次选择“计算机组”、“SCCM”。********  
5. 选择“导入 Configuration Manager 集合成员身份”****，并单击“保存”****。  
   
    ![计算机的屏幕截图： S C C M 的高级设置，其中包含导入 Configuration Manager 集合成员身份的选项。](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>查看配置管理器中的数据

将 Log Analytics 连接添加到 Configuration Manager 并在运行 Configuration Manager 服务连接点站点系统角色的计算机上安装了代理之后，来自代理的数据将发送到 Azure Monitor 中的 Log Analytics 工作区。 在 Azure Monitor 中，Configuration Manager 集合以[计算机组](./computer-groups.md)的形式显示。 可以从“设置”>“计算机组”**** 下的“Configuration Manager”**** 页查看这些组。

在导入集合后，可以看到已检测到的具有集合成员身份的计算机数。 此外还可以看到已导入的集合数。

![用于显示 "导入 Configuration Manager 收集成员身份" 选项的计算机组的 "高级设置" 的屏幕截图。](./media/collect-sccm/sccm-computer-groups02.png)

单击上面任意一项，都会打开日志查询编辑器，相应显示所有已导入的组，或者属于每个组的所有计算机。 使用[日志搜索](../log-query/log-query-overview.md)，可以针对集合成员身份数据执行进一步的深度分析。

## <a name="next-steps"></a>后续步骤

使用[日志搜索](../log-query/log-query-overview.md)查看有关配置管理器数据的详细信息。

