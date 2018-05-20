---
title: 管理 Azure Stack 中的 API 版本配置文件 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: e568ffd2c3adb97ed0b727b85e7888fb797db1f9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 可以使用 API 配置文件以不同的语言创建自定义客户端。 每个客户端使用的 API 配置文件来为 Azure 堆栈联系的正确的资源提供程序和 API 版本。

可以创建一个应用来与 Azure 资源提供程序配合运行，而无需明确区分与 Azure Stack 兼容的每个资源提供程序 API 的版本。 只需将对齐到配置文件; 应用程序SDK 将恢复为正确的 API 版本。

本主题帮助读者了解：

 - Azure Stack 的 API 配置文件。
 - 了解如何使用 API 配置文件来开发你的解决方案。
 - 请参阅在哪里可以找到特定于代码的指南。

## <a name="summary-of-api-profiles"></a>API 配置文件的摘要

- API 配置文件用于表示一组 Azure 资源提供程序及其 API 版本。
- API 配置文件已为开发人员创建，因此它们可以跨多个 Azure 云创建模板。 配置文件旨在满足对兼容和稳定接口的需要。
- 配置文件每年发布四次。
- 使用三个配置文件命名约定：
    - **latest**  
        包含在全球 Azure 中发布的最新的 API 版本。
    - **yyyy-mm-dd-hybrid**  
    发布节奏举行，此版本侧重于一致性和稳定性多个云。 此配置文件以目标最佳 Azure 堆栈兼容性。
    - **配置文件 yyyy mm dd**位于之间最佳的稳定性和最新功能。

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API 配置文件和 Azure 堆栈兼容性

最新的 Azure API 配置文件不是与 Azure 堆栈兼容的。 可以使用以下命名约定来标识要用于 Azure 堆栈解决方案的配置文件。

**最新**  
此配置文件已在全球 Azure，Azure 堆栈中将不起作用中找到的最新的 API 版本。 **最新**具有最大数量的重大更改。 稳定性和与其他云中的兼容性，该配置文件将保留。 如果你正在尝试使用最新的 API 版本，**最新**是应使用的配置文件。

**Yyyy mm dd 混合**  
此配置文件在年 3 月和 9 月每年发布。 此配置文件具有最佳的稳定性和与各类云的兼容性。 **Yyyy mm dd 混合**设计为针对全局 Azure 和 Azure 堆栈。 此配置文件中列出的 Azure API 版本将列出 Azure 堆栈的相同。 此配置文件可用于开发针对混合云解决方案的代码。

**yyyy-mm-dd-profile**  
此配置文件面向全球 Azure 发布于 6 月和年 12 月。 此配置文件将不起作用根据 Azure 堆栈;通常情况下，将有许多重大更改。 尽管它位于之间最佳的稳定性和最新功能，之间的差异**最新**，并且此配置文件是**最新**将始终包含的最新的 API 版本，不管何时的 API已释放。 例如，如果新的 API 版本明天创建为计算 API，该 API 版本将被列入**最新**，但未显示在**yyyy mm-dd 配置文件**因为此配置文件已存在。  **配置文件 yyyy mm dd**涵盖发行之前年 6 月或年 12 月之前的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure 堆栈不使用在全球 Azure 中找到的 API 版本的最新版本。 当创建解决方案时，你需要查找每个 Azure 资源提供程序与 Azure 堆栈兼容的 API 版本。

而是比研究每个资源提供程序和支持的 Azure 堆栈的特定版本，你可以使用 API 配置文件。 配置文件会指定一组资源提供程序和 API 版本。 SDK 或者使用 SDK 生成的工具将还原到配置文件中指定的目标 api-version。 使用 API 配置文件可以指定应用到整个模板的配置文件版本，在运行时，Azure 资源管理器会选择正确的资源版本。

API 配置文件可与使用 Azure 资源管理器的工具（例如 PowerShell、Azure CLI、SDK 中提供的代码，以及 Microsoft Visual Studio）配合运行。 工具和 SDK 可以使用配置文件来读取生成应用程序时要包含的模块和库的版本。

**使用配置文件的开发方案**  
假设你要使用 PowerShell 创建：

* 使用的存储帐户**Microsoft.Storage**资源提供程序，支持 api 版本 2016年-03-30。
* 使用的 VM， **Microsoft.Compute**资源提供程序，支持 api 版本 2015年-12-01。

而不是查找和安装支持的 api 版本存储和计算所需的 PowerShell 模块，你可以使用配置文件。 使用 cmdlet * * 安装配置文件 * profilename * * *，和 PowerShell 加载的模块的正确版本。

同样，如果你要使用 Python SDK 构建 Python 基于应用程序，你可以使用配置文件。 SDK 加载的脚本中指定的资源提供程序的正确的模块。

开发人员可以专注于编写解决方案。 你可以使用一个配置文件，了解你的代码将在支持的配置文件的所有云之间工作。

## <a name="api-profile-code-samples"></a>API 配置文件代码示例

可以借助代码示例，通过配置文件将采用偏好语言的解决方案与 Azure Stack 相集成。 目前可以找到以下语言的指导和示例：

- **PowerShell**  
可以使用通过 PowerShell 库提供的 **AzureRM.Bootstrapper** 模块来获取使用 API 版本配置文件所需的 PowerShell cmdlet。 有关信息，请参阅[使用适用于 PowerShell 的 API 版本配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI 2.0**  
可将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 有关信息，请参阅[使用适用于 Azure CLI 2.0 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。
- **GO**  
在 GO SDK 中，配置文件结合了不同服务的不同版本的不同资源类型。 配置文件在 profiles/ 路径下提供，其版本采用 **YYYY-MM-DD** 格式。 有关信息，请参阅[使用 API 版本转到配置文件](azure-stack-version-profiles-go.md)。
- **Ruby**  
Ruby SDK Azure 堆栈资源管理器提供了工具来帮助你构建和管理你的基础结构。 SDK 中的资源提供程序包括计算、 虚拟网络和存储与 Ruby 语言使用。 有关信息，请参阅[使用 API 版本配置文件与 Ruby](azure-stack-version-profiles-ruby.md)

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [查看配置文件支持的资源提供程序 API 版本的详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
