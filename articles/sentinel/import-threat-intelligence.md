---
title: 将威胁智能导入 Azure Sentinel |Microsoft Docs
description: 使用 Azure Sentinel 中的威胁智能源来分析数据、检测威胁，并生成警报和事件。 将威胁智能信息与工作簿可视化。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: e04d7fa1f319ca3969d8acdc0235e2838bb3a88d
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90994775"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>将威胁智能导入 Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>威胁情报简介

网络威胁情报 (CTI) 是描述系统和用户的已知现有或潜在威胁的信息。 这种类型的信息采用多种形式，包括详细介绍特定威胁参与者的动机、基础结构和技术的书面报表，以及与网络威胁关联的 IP 地址、域和文件哈希的特定观察。 组织使用 CTI 为异常活动提供必要的上下文，以便安全人员可以快速采取措施来保护其人员和资产。 CTI 可以来自多个位置，例如开源数据馈送、威胁情报共享社区、商业智能源和在组织中的安全调查过程中收集的本地智能。

在安全信息和事件管理 (SIEM) 解决方案，如 Azure Sentinel，最充分利用的 CTI 形式是威胁指标，通常称为折衷或 Ioc 的征兆。 威胁指标是指将观测值（如 Url、文件哈希或 IP 地址）与已知的威胁活动（如仿冒、僵尸网络或恶意软件）相关联的数据。 这种形式的威胁情报通常称为战术性威胁情报，因为它可以应用于较大规模的安全产品和自动化，以保护和检测组织的潜在威胁。 在 Azure Sentinel 中，你可以使用威胁指标来帮助检测你的环境中观测到的恶意活动，并向安全调查人员提供上下文以帮助通知响应。

可以通过以下活动将威胁情报 (TI) 集成到 Azure Sentinel 中：

- 使用各种 TI 平台的 **数据连接器** ，将 [威胁情报导入](./connect-threat-intelligence.md) Azure Sentinel。
- 在 " **日志** " 和 "Azure Sentinel" 的新 " **威胁智能** " 区域中查看和管理已导入的威胁智能。
- 使用内置的 **分析** 规则模板，可以使用导入的威胁智能生成安全警报和事件。
- 通过 **威胁情报工作簿**直观显示 Azure 中的威胁情报的关键信息。

威胁情报还在其他 Azure Sentinel 体验（如 **搜寻** 和 **笔记本**）中提供了有用的上下文，而在本文未涵盖的情况下，在 [Ian 中通过 Jupyter 笔记本上的 Hellen 对](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)这些体验进行了介绍。

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>用于威胁情报的 Azure Sentinel 数据连接器

就像 Azure Sentinel 中的所有其他事件数据一样，威胁指标是使用数据连接器导入的。 Azure Sentinel 中有两个数据连接器专门为威胁指标、 **威胁情报 TAXII** 和 **威胁智能平台**提供。 您可以单独使用数据连接器或同时使用这两个连接器，具体取决于您的组织在何处建立威胁指标。 我们来谈谈每个数据连接器。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>通过威胁情报平台数据连接器将威胁指标添加到 Azure Sentinel

许多组织使用威胁智能平台 (TIP) 解决方案来聚合来自各种来源的威胁指标源、组织平台中的数据，然后选择要应用于各种安全解决方案（例如网络设备、高级威胁防护解决方案或 Siem，如 Azure Sentinel）的威胁指标。 如果你的组织使用集成的 TIP 解决方案，如 MISP、Anomali ThreatStream、ThreatConnect 或 Palo Alto Networks MineMeld，则 **威胁情报平台数据连接器** 可让你使用 TIP 将威胁指标导入 Azure Sentinel。 由于连接器与 [Microsoft Graph Security TIINDICATORS API](https://docs.microsoft.com/graph/api/resources/tiindicator) 配合使用来完成此操作，因此，任何自定义威胁智能平台都可以使用连接器将指示器发送到 Azure Sentinel (和其他 Microsoft 安全解决方案，如 Defender ATP) 。

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="威胁情报导入路径":::

请按照以下步骤操作，从集成式 TIP 或自定义威胁情报解决方案中将威胁指标导入到 Azure Sentinel：

1. 从 Azure Active Directory 获取应用程序 ID 和客户端机密

1. 将此信息输入到 TIP 解决方案或自定义应用程序中

1. 在 Azure Sentinel 中启用威胁情报平台数据连接器

下面详细介绍了每个步骤。

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>从 Azure Active Directory 获取应用程序 ID 和客户端机密

无论你使用的是 TIP 还是使用自定义解决方案，tiIndicators API 都需要一些基本信息来连接和发送威胁指标。 需要获取的三条信息是：
- 应用程序（客户端）ID
- 目录（租户）ID
- 客户端机密

此信息始终来自 Azure Active Directory 通过称为 **应用注册** 的过程，其中包括以下三个步骤：
- 向 Azure Active Directory 注册应用
- 指定应用程序连接到 Microsoft Graph tiIndicators API 所需的权限，并发送威胁指标
- 获取组织的许可，以将这些权限授予此应用程序。  

**向 Azure Active Directory 注册应用程序**

1. 打开 [Azure 门户](https://portal.azure.com/) ，导航到 **Azure Active Directory** 服务。

1. 从菜单中选择 " **应用注册** "，然后选择 " **新注册**"。

1. 选择应用程序注册的名称，选择 " **单租户** " 单选按钮，然后选择 " **注册**"。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="注册应用程序":::

1. 在随后出现的屏幕中，将 **应用程序 (客户端) id** 和 **目录 (租户) ID** 值。 这些是你稍后要配置提示或自定义解决方案以将威胁指标发送到 Azure Sentinel 所需的前两个信息片段。

**指定应用程序所需的权限**

1. 打开 [Azure 门户](https://portal.azure.com/) ，导航到 **Azure Active Directory** 服务。

1. 从菜单中选择 " **应用注册** "，并选择新注册的应用。

1. 从菜单中选择 " **API 权限** "，然后单击 " **添加权限** " 按钮。

1. 在 " **选择 API** " 页上，选择 " **Microsoft Graph** " 以从 Microsoft Graph 权限列表中进行选择。

1. 当系统询问 **您的应用程序所需的权限类型时，** 请选择 " **应用程序权限**"。 这是使用应用程序 ID 和应用密钥进行身份验证的应用程序所使用的权限类型 (API 密钥) 。

1. 选择 **ThreatIndicators. OwnedBy** ，然后选择 " **添加权限** "，将此权限添加到应用的权限列表。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="指定权限":::

**获得你的组织同意授予这些权限**

1. 若要授予许可，你需要一个 Azure Active Directory 全局管理员，以在应用的 API 权限页面上选择 " **授予租户管理员许可** " 按钮。 如果你的帐户没有 "全局管理员" 角色，此按钮将不可用，你将需要向组织的全局管理员请求执行此步骤。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="授予许可":::

1. 向你的应用程序授予同意后，" **状态**" 下应会出现绿色复选标记。
 
现在，你的应用已注册并已授予权限，你可以在列表中获取最后一件事-应用的客户端机密。

1. 打开 [Azure 门户](https://portal.azure.com/) ，导航到 **Azure Active Directory** 服务。

1. 从菜单中选择 " **应用注册** "，并选择新注册的应用。

1. 从菜单中选择 " **证书" & 机密** ，然后单击 " **新建客户端密码** " 按钮，为你的应用获取密钥 (API 密钥) 。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="获取客户端机密":::

1. 单击 " **添加** " 按钮，并 **确保复制客户端密码**，因为如果离开此页，则不能再次检索此机密。 配置提示或自定义解决方案时需要此值。

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>将此信息输入到 TIP 解决方案或自定义应用程序中

现在，你已获得了配置提示或自定义解决方案以将威胁指标发送到 Azure Sentinel 所需的全部三条信息。 
- 应用程序（客户端）ID
- 目录（租户）ID
- 客户端机密

在你的集成提示或自定义解决方案的配置中输入这些值（如果需要），并且将通过针对 Azure Sentinel 的 Microsoft Graph tiIndicators API 发送威胁指标。

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中启用威胁情报平台数据连接器

集成过程的最后一步是在 Azure Sentinel 中启用 **威胁情报平台** 数据连接器。 这是将从 TIP 或自定义 Microsoft Graph 解决方案发送的威胁指标从 tiIndicators API 导入到 Azure Sentinel 的步骤。 这些指标将可供组织的所有 Azure 标记工作区使用。 按照以下步骤为每个工作区启用威胁情报平台数据连接器：

1. 打开 [Azure 门户](https://portal.azure.com/) 并导航到 **Azure Sentinel** 服务。

1. 选择要将从 TIP 或自定义解决方案发送的威胁指标导入到的 **工作区** 。

1. 从菜单中选择 " **数据连接器** "，从 "连接器库" 中选择 " **威胁智能平台** "，然后单击 " **打开连接器页** " 按钮。

1. 由于已完成应用注册并已将提示或自定义解决方案配置为发送威胁指标，因此唯一的步骤是单击 " **连接** " 按钮。

几分钟后，威胁指标会开始流入此 Azure 标记工作区。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>通过威胁情报将威胁指标添加到 Azure Sentinel-TAXII 数据连接器

用于传输威胁智能的最广泛采用的行业标准是 [STIX 数据格式和 TAXII 协议的组合](https://oasis-open.github.io/cti-documentation/)。 如果你的组织从支持当前 STIX/TAXII 版本 (2.0 或 2.1) 的解决方案获得威胁指标，则可以使用 **威胁智能-TAXII** 数据连接器将威胁指标引入 Azure Sentinel。 利用威胁情报-TAXII 数据连接器，Azure Sentinel 中的内置 TAXII 客户端可以从 TAXII 2.x 服务器导入威胁情报。

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 导入路径":::
 
请按照以下步骤将 STIX 格式的威胁指标从 TAXII 服务器导入到 Azure Sentinel：

1. 获取 TAXII 服务器 API 根和集合 ID

1. 在 Azure Sentinel 中启用威胁情报-TAXII 数据连接器

现在，让我们详细了解一下其中的每个步骤。

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>获取 TAXII 服务器 API 根和集合 ID

TAXII 2.x 服务器播发 API 根，它是托管威胁情报集合的 Url。 最常见的情况是，可以通过托管 TAXII 服务器的威胁智能提供程序的 "文档" 页来获取 API 根目录。 但是，有时所公布的唯一信息是一个称为发现终结点的 URL。 如果是这种情况，可以使用发现终结点轻松找到 API 根。 如果你已知道要使用的 TAXII 服务器 API 根和集合 Id，请随时跳到下一节 **在 Azure Sentinel 中启用威胁情报-TAXII 数据连接器**。

让我们看一下如何使用简单的命令行实用程序，该实用程序在 Windows 和大多数 Linux 发行版中 [提供，用于](https://en.wikipedia.org/wiki/CURL)发现 API 根并从发现终结点开始浏览 TAXII 服务器的集合。 在此示例中，我们将使用 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 服务器的发现终结点。

1.  在浏览器中，导航到 [THREATSTREAM TAXII 2.0 服务器发现终结点](https://limo.anomali.com/taxii) 以检索 API 根。 用用户和密码进行身份验证 `guest` 。

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  使用上一步中的卷实用工具和 API 根 (https://limo.anomali.com/api/v1/taxii2/feeds/) 浏览 Api 根目录上托管的集合 id 的列表

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

现在，你已获得将 Azure Sentinel 连接到 Anomali Limo 提供的一个或多个 TAXII 服务器集合所需的所有信息。

| **API 根** (https://limo.anomali.com/api/v1/taxii2/feeds/) | 集合 ID |
| ------------------------------------------------------------ | ------------: |
| **网络钓鱼箱**                                               | 107           |
| **Abuse.ch 勒索软件 Ip**                                  | 135           |
| **Abuse.ch 勒索软件域**                              | 136           |
| **DShield 扫描 Ip**                                     | 150           |
| **恶意软件域列表-Hotlist**                            | 200           |
| **Blutmagie TOR 节点**                                      | 209           |
| **C 服务器&的新兴威胁**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **公约**                                               |  41           |
| **新兴威胁-已泄露**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中启用威胁情报-TAXII 数据连接器

若要将威胁指标从 TAXII 服务器导入到 Azure Sentinel 中，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/) 并导航到 **Azure Sentinel** 服务。

1. 选择要将威胁指标从 TAXII 服务器导入到的 **工作区** 。

1. 从菜单中选择 " **数据连接器** "，从连接器库中选择 " **威胁情报-TAXII** "，然后单击 " **打开连接器页面** " 按钮。

1. 键入此 TAXII 服务器集合的 **名称** 、 **API 根 URL**、 **集合 ID**、 **用户名** (如果需要) 和 **密码** (如有必要) ，然后单击 " **添加** " 按钮。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="配置 TAXII 服务器":::
 
你应该会收到确认，指出已成功建立与 TAXII 服务器的连接，并且可以根据需要重复多次执行步骤 (4) ，以连接到相同或不同 TAXII 服务器的多个集合。

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>查看 Azure Sentinel 中的威胁指标

现在，你已使用**威胁智能平台**和/或**威胁情报-TAXII**数据连接器将威胁指标成功导入到 Azure Sentinel，你可以在**日志**中的**ThreatIntelligenceIndicator**表中查看这些标记，其中存储了所有 Azure Sentinel 事件数据。 此表是其他 Azure Sentinel 功能（如分析和工作簿）执行的查询的基础。 下面介绍了如何在 **ThreatIntelligenceIndicator** 表中查找和查看威胁指标。

1. 打开 [Azure 门户](https://portal.azure.com/) 并导航到 **Azure Sentinel** 服务。

1. 使用威胁智能数据连接器选择已导入威胁指标的 **工作区** 。

1. 从 Azure Sentinel 菜单的 "**常规**" 部分中选择 "**日志**"。

1. **ThreatIntelligenceIndicator**表位于**Azure Sentinel**组下。

1. 选择 " **预览数据** " 图标 (表名称旁边的眼睛) ，然后选择 " **在查询编辑器中查看** " 按钮执行将显示此表中的记录的查询。

结果应类似于下面所示的示例威胁指标：

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="示例查询数据":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>在 Azure Sentinel 的新 "威胁智能" 区域中管理威胁指标

使用新的 **威胁情报** 领域（可从 Azure 标记菜单访问），你还可以查看、排序、筛选和搜索导入的威胁指标，甚至无需编写 **日志** 查询。 利用这一新的区域，你还可以直接在 Azure 标记接口中创建威胁指标，并执行常见的威胁智能管理任务，例如标记标记，以及创建与安全调查相关的新指示器。
让我们看看两个最常见的任务，创建新的威胁指标和标记指示器以便于分组和引用。

1. 打开 [Azure 门户](https://portal.azure.com/) 并导航到 **Azure Sentinel** 服务。

1. 使用威胁智能数据连接器选择已导入威胁指标的 **工作区** 。

1. 从 Azure Sentinel 菜单的 "威胁管理" 部分选择 " **威胁智能** "。

1. 从页面顶部菜单中选择 " **添加新** 按钮"。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="添加新威胁指标" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. 选择指示器类型，然后在 **新的指示器** 面板上完成标有红色星号 ( * ) 的必填字段。

1. 选择“应用”。  指示器将添加到指示器网格，并且也会发送到 **日志**中的 ThreatIntelligenceIndicator 表。

标记威胁指标是将它们组合在一起以使其更易于查找的一种简单方法。 通常，您可以将标记应用于与特定事件相关的指示器，或应用于表示来自特定已知执行组件或众所周知攻击市场活动的威胁的指示器。 您可以单独标记威胁指标，也可以选择多个指示器，并同时对它们进行标记。 下面显示了使用事件 ID 标记多个指示器的示例。 由于标记是自由格式的，建议的做法是为威胁指示符标记创建标准命名约定。 可以将多个标记应用于每个指示器。

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="将标记应用到威胁指标" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>分析可将威胁指标用于检测潜在的威胁

已将威胁指标送入 Azure Sentinel;你已了解如何查看和管理它们;现在，查看它们可以为您做些什么。 Azure Sentinel 等 SIEM 解决方案中威胁指标的最重要用例是使用 power analytics 规则。  这些基于指示器的规则将数据源中的原始事件与威胁指标进行比较，以检测组织中的安全威胁。 在 Azure Sentinel **analytics**中，你将创建按计划运行的分析规则并生成安全警报。 这些规则是由查询驱动的，以及用于确定应运行规则的频率的配置、生成安全警报的类型的查询结果，以及在生成警报时触发的任何自动响应。

尽管始终可以从头开始创建新的分析规则，但 Azure Sentinel 提供一套由 Microsoft 安全工程师创建的内置规则模板，可以按原样使用这些模板，也可以根据需要进行修改。 你可以轻松地识别使用威胁指标的规则模板，因为它们的标题以 "**TI map**..." 开头。 所有这些规则模板的运行方式都相同，唯一的区别在于 (域、电子邮件、文件哈希、IP 地址或 URL) 使用哪种类型的威胁指示器，以及要匹配的事件类型。 每个模板都列出了该规则正常运行所需的数据源，这样您就可以一目了然地了解 Azure Sentinel 中已经导入了必要的事件。

让我们看一下这些规则模板中的一个，并演练如何启用和配置规则，以便使用已导入到 Azure Sentinel 的威胁指标生成安全警报。 在此示例中，我们将使用名 **为 TI MAP IP 实体**的规则模板来 AzureActivity。 此规则将所有 IP 地址类型威胁指标与所有 Azure 活动事件匹配。 如果找到匹配项，则会生成 **警报** ，以及安全操作团队要调查的相应 **事件** 。 仅当已启用一个或两个 **威胁智能** 数据连接器 (导入威胁指标) 和 **azure 活动** 数据连接器 (导入 azure 订阅级别事件) 时，此分析规则才能成功运行。

1. 打开 [Azure 门户](https://portal.azure.com/) 并导航到 **Azure Sentinel** 服务。

1. 使用**Azure 活动**数据连接器，选择要**将威胁指标**导入到的**工作区**。

1. 从 Azure Sentinel 菜单的 "**配置**" 部分选择 "**分析**"。

1. 选择 " **规则模板** " 选项卡以查看可用分析规则模板的列表。

1. 导航到标题为 " **TI MAP IP 实体到 AzureActivity** " 的规则，并确保已连接所有所需的数据源，如下所示。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="必需的数据源":::

1. 选择此规则并选择 " **创建规则** " 按钮。 这会打开一个向导来配置规则。 完成此处的设置，然后选择 " **下一步：设置规则逻辑 >** " 按钮。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="创建分析规则":::

1. 向导的 "规则逻辑" 部分包含：
    - 将在规则中使用的查询。

    - 实体映射，告知 Azure Sentinel 如何识别实体（如帐户、IP 地址和 Url），以便 **事件** 和 **调查** 理解如何处理此规则生成的任何安全警报中的数据。

    - 运行此规则的计划。

    - 生成安全警报之前所需的查询结果数。

    模板中的默认设置为：
    - 每小时运行一次。

    - 将 **ThreatIntelligenceIndicator** 表中的任何 ip 地址威胁指标与从 **AzureActivity** 表中的最后一小时事件中找到的任何 ip 地址匹配。

    - 如果查询结果大于零，则生成安全警报，这意味着，如果找到任何匹配项。

你可以保留默认设置，也可以更改任何一个设置以满足你的要求。 完成后，选择 " **下一步：自动响应 >** " 按钮

1. 此向导步骤允许你配置在从此分析规则生成安全警报时要触发的任何自动化。 Azure Sentinel 中的自动化使用由 Azure 逻辑应用提供支持的 **行动手册**完成。 若要了解详细信息，请参阅本 [教程：在 Azure Sentinel 中设置自动威胁响应](./tutorial-respond-threats-playbook.md)。 对于本示例，只需选择 " **下一步：查看 >** " 按钮继续。

1. 最后一步验证规则中的设置。 准备好启用规则时，请选择 " **创建** " 按钮并完成操作。

启用分析规则后，可以在 Azure Sentinel 的 "**分析**" 部分的 "**活动规则**" 选项卡中找到已启用的规则。 可以在其中编辑、启用、禁用、复制或删除活动规则。 新规则会在激活后立即运行，然后在上一开始，将按定义的计划运行。

根据默认设置，在每次规则按计划运行时，找到的任何结果都将生成安全警报。 可以在 azure sentinel 的 "**日志**" 部分中的 **"azure sentinel" 组下**的**SecurityAlert**表中查看 azure sentinel 中的安全警报。

在 Azure Sentinel 中，通过分析规则生成的警报还会生成安全事件，这些事件可在 Azure Sentinel 菜单上的 "**威胁管理**" 下的**事件**中找到。 事件是安全操作团队将会审并调查以确定相应响应操作的内容。 可在本教程中查找详细信息 [：通过 Azure Sentinel 调查事件](./tutorial-investigate-cases.md)。

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>工作簿为你的威胁情报提供见解

最后，你可以使用 Azure Sentinel 工作簿直观显示 Azure 中的威胁情报的关键信息，并且可以根据业务需求轻松地自定义工作簿。
让我们演练如何查找 Azure Sentinel 中提供的威胁智能工作簿，并将演示如何对工作簿进行编辑，以便对其进行自定义。

1. 打开 [Azure 门户](https://portal.azure.com/) 并导航到 **Azure Sentinel** 服务。

1. 使用威胁智能数据连接器选择已导入威胁指标的 **工作区** 。

1. 从 Azure Sentinel 菜单的 "**威胁管理**" 部分中选择**工作簿**。

1. 导航到标题为 " **威胁智能** " 的工作簿，并验证 **ThreatIntelligenceIndicator** 表中是否有数据，如下所示。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="验证数据":::
 
1. 选择 " **保存** " 按钮，然后选择用于存储工作簿的 Azure 位置。 如果要以任何方式修改工作簿并保存更改，则需要执行此步骤。

1. 现在选择 " **查看保存的工作簿** " 按钮，打开工作簿以便查看和编辑。

1. 现在应会看到模板提供的默认图表。 现在，让我们对其中一个图表进行一些更改。 选择页面顶部的 " **编辑** " 按钮，进入工作簿的编辑模式。

1. 让我们添加一个新的威胁指标图表，按威胁类型。 滚动到页面底部，选择 "添加查询"。

1. 将以下文本添加到 " **Log Analytics 工作区日志查询** " 文本框中：
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. 在 " **可视化** " 下拉箭头中，选择 " **条形图**"。

1. 选择 " **完成编辑** " 按钮。 已为工作簿创建新的图表。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="条形图":::

工作簿提供了强大的交互仪表板，可让你深入了解 Azure Sentinel 的各个方面。 你可以对工作簿执行一项全面的操作，而提供的模板非常不错，你可能想要深入研究并自定义这些模板，或者创建新的仪表板来合并多个不同的数据源，以便能够以独特方式可视化数据。 由于 Azure Sentinel 工作簿基于 Azure Monitor 工作簿，因此已提供大量的文档，还有更多模板。 这篇文章介绍了如何 [使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/platform/workbooks-overview.md)。 

GitHub 上还提供丰富的 [Azure Monitor 工作簿](https://github.com/microsoft/Application-Insights-Workbooks) 社区，你可以在其中下载其他模板和提供你自己的模板。

## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure Sentinel 的威胁情报功能，以及新的威胁情报边栏选项卡。 有关使用 Azure Sentinel 的威胁情报功能的实用指导，请参阅以下文章：

- [将威胁智能数据连接](./connect-threat-intelligence.md) 到 Azure Sentinel。
- 在 Azure Sentinel 中创建 [内置](./tutorial-detect-threats-built-in.md) 或 [自定义](./tutorial-detect-threats-custom.md) 警报，并 [调查](./tutorial-investigate-cases.md) 事件。