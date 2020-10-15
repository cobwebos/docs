---
title: DoD 影响级别 5 蓝图示例
description: DoD 影响级别 5 蓝图示例的部署步骤，包括蓝图项目参数详细信息。
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: deb6ffa0f886cc6a4a9bea10a38d7cd82e7df8f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90978223"
---
# <a name="deploy-the-dod-impact-level-5-blueprint-sample"></a>部署 DoD 影响级别 5 蓝图示例

若要部署 Azure 蓝图国防部影响级别 5 (DoD IL5) 蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布”
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。

1. 在“其他示例”下找到“DoD 影响级别 5”蓝图示例，然后选择“使用此示例”。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：为你的 DoD 影响级别 5 蓝图示例副本提供一个名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。 可根据环境和需求自定义蓝图示例的副本，但这种修改可能不符合 DoD 影响级别 5 控制要求。

1. 在左侧窗格中，选择“所有服务”。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧的新窗格中，提供蓝图示例副本的**版本**。 以后做出修改时，此属性非常有用。 提供“更改注释”，例如“基于 DoD 影响级别 5 蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。

## <a name="assign-the-sample-copy"></a>分配示例副本

成功**发布**蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”。

1. 在左侧选择“蓝图定义”页。 使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。

1. 提供蓝图分配的参数值：

   - 基础

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - 位置：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的**已发布**版本。

   - 锁分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../../concepts/resource-locking.md)。

   - 托管标识

     保留默认的系统分配的托管标识选项。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。 随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是**免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|DoD 影响级别 5|策略分配|必须包含在 Windows VM 管理员组中的用户的列表|以分号分隔的应包括在管理员本地组中的用户列表；示例：管理员；myUser1；myUser2|
|DoD 影响级别 5|策略分配|从 Windows VM 管理员组排除的用户的列表|以分号分隔的应从管理员本地组中排除的用户列表；示例：管理员；myUser1；myUser2|
|DoD 影响级别 5|策略分配|应启用诊断日志的资源类型列表||
|DoD 影响级别 5|策略分配|用于 VM 代理报告的 Log Analytics 工作区 ID|VM 代理应在其中报告的 Log Analytics 工作区的 ID (GUID)|
|DoD 影响级别 5|策略分配|应启用网络观察程序的区域的列表|若要查看区域的完整列表，请使用 Get-AzLocation，|
|DoD 影响级别 5|策略分配|Windows Web 服务器的最低 TLS 版本|应在 Windows Web 服务器上启用的最低 TLS 协议版本|
|DoD 影响级别 5|策略分配|最新 PHP 版本|应用服务支持的最新 PHP 版本|
|DoD 影响级别 5|策略分配|最新 Java 版本|应用服务支持的最新 Java 版本|
|DoD 影响级别 5|策略分配|最新 Windows Python 版本|应用服务支持的最新 Python 版本|
|DoD 影响级别 5|策略分配|最新 Linux Python 版本|应用服务支持的最新 Python 版本|
|DoD 影响级别 5|策略分配|可选：支持 Log Analytics 代理添加到审核范围的 Windows VM 映像的列表|映像的分号分隔列表；示例：/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD 影响级别 5|策略分配|可选：支持 Log Analytics 代理添加到审核范围的 Linux VM 映像的列表|映像的分号分隔列表；示例：/subscriptions/<subscriptionId>/resourceGroups/YourResourceGroup/providers/Microsoft.Compute/images/ContosoStdImage|
|DoD 影响级别 5|策略分配|策略的效果：应该为你的订阅分配了多个所有者|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机上启用磁盘加密|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应启用向订阅所有者发送高严重性警报的电子邮件通知|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应当为函数应用禁用远程调试|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：如果在函数应用中使用“.Net Framework”，请确保它是最新版|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 数据库上启用透明数据加密|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应对 SQL 托管实例启用漏洞评估|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作 API 应用一部分的“PHP 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该为 SQL 服务器预配 Azure Active Directory 管理员|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该启用只能通过安全方式连接到 Redis 缓存|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机规模集上安装 Endpoint Protection 解决方案|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：审核对存储帐户的不受限的网络访问|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：SQL 托管实例的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该修复虚拟机规模集上安全配置中的漏洞|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该启用安全传输到存储帐户|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机上启用自适应应用程序控制|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应为 Azure Database for PostgreSQL 启用异地冗余备份|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作 Web 应用一部分的“Java 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：只多只为订阅指定 3 个所有者|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应为订阅提供安全联系人电子邮件地址|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：CORS 不应允许所有资源访问 Web 应用程序|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应从订阅中删除具有写入权限的外部帐户|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应从订阅中删除拥有读取权限的外部帐户|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应从订阅中删除弃用的帐户|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该只能通过 HTTPS 访问函数应用|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作 Web 应用一部分的“Python 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作函数应用一部分的“Python 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作 WEB 应用一部分的“PHP 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作 API 应用一部分的“Python 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该通过漏洞评估解决方案修复漏洞|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应为 Azure Database for MySQL 启用异地冗余备份|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：如果在 Web 应用中使用“.Net Framework”，请确保它是最新版|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在计算机上安装系统更新|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作 API 应用一部分的“Java 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用于运行 Web 应用的“HTTP 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 API 应用中使用最新的 TLS 版本|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应对订阅中拥有写入权限的帐户启用 MFA|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：SQL 服务器的“高级数据安全性”设置应包含用于接收安全警报的电子邮件地址|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用于运行 API 应用的“HTTP 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 Windows 服务器上部署 Microsoft IaaSAntimalware 扩展|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作函数应用一部分的“Java 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该限制通过面向 Internet 的终结点进行访问|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应选择安全中心标准定价层|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：审核自定义 RBAC 规则的使用|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：只能通过 HTTPS 访问 Web 应用程序|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应启用 SQL 服务器上的审核|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机上安装 Log Analytics 代理|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应启用 DDoS 防护标准版|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在对订阅拥有所有者权限的帐户上启用 MFA|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用作函数应用一部分的“PHP 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 服务器上启用高级数据安全性|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 托管实例上启用高级数据安全性|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 托管实例高级数据安全设置中启用“向管理员和订阅所有者发送电子邮件通知”|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：监视 Azure 安全中心 Endpoint Protection 的缺失情况|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机上应用实时网络访问控制|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应为订阅提供安全联系人电话号码|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：Service Fabric 群集只应使用 Azure Active Directory 进行客户端身份验证|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：只能通过 HTTPS 访问 API 应用|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 托管实例的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应为存储帐户启用异地冗余存储|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：如果在 API 应用中使用“.Net Framework”，请确保它是最新版|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机规模集上安装系统更新|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 服务器高级数据安全设置中为管理员和订阅所有者启用电子邮件通知|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应当为 Web 应用程序禁用远程调试|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应为 Azure SQL 数据库启用长期异地冗余备份|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该修复计算机上安全配置中的漏洞|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：确保用于运行函数应用的“HTTP 版本”是最新的|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在对订阅拥有读取权限的帐户上启用 MFA|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 SQL 服务器的“高级数据安全性”设置中将“高级威胁保护类型”设置为“所有”|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该修复容器安全配置中的漏洞|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应当为 API 应用禁用远程调试|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应从订阅中删除拥有所有者权限的已弃用帐户|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应对 SQL Server 启用漏洞评估|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在虚拟机规模集上安装 Log Analytics 代理|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在 Web 应用中使用最新的 TLS 版本|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应从订阅中删除拥有所有者权限的外部帐户|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应在函数应用中使用最新的 TLS 版本|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：[预览]：Kubernetes 服务应升级到不易受攻击的 Kubernetes 版本|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|
|DoD 影响级别 5|策略分配|策略的效果：应该修复 SQL 数据库中的漏洞|此策略的 Azure Policy 效果；若要详细了解效果，请访问 https://aka.ms/policyeffects|

## <a name="next-steps"></a>后续步骤

了解 DoD 影响级别 5 蓝图示例的部署步骤后，请访问以下文章来了解蓝图和控制映射：

> [!div class="nextstepaction"]
> [DoD 影响级别 5 蓝图 - 概述](./index.md)
> [DoD 影响级别 5 蓝图 - 控制映射](./control-mapping.md)

有关蓝图及其使用方式的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。