---
title: 管理 Azure Stack 中的 API 版本配置文件 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: sethm
ms.reviewer: sijuman
ms.openlocfilehash: 1b63a75a2505ecbd82896932018ef4f0936b9b37
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182886"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本配置文件

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 可以使用 API 配置文件以不同的语言创建自定义客户端。 每个客户端都使用 API 配置文件来与 Azure Stack 的正确的资源提供程序和 API 版本通信。

可以创建一个应用来与 Azure 资源提供程序配合运行，而无需明确区分与 Azure Stack 兼容的每个资源提供程序 API 的版本。 只需将应用程序对应到某个配置文件，SDK 就能还原到正确的 API 版本。

本主题帮助读者了解：

 - Azure Stack 的 API 配置文件。
 - 了解如何使用 API 配置文件来开发解决方案。
 - 查看在何处查找代码特定的指导。

## <a name="summary-of-api-profiles"></a>API 配置文件的摘要

- API 配置文件用于表示一组 Azure 资源提供程序及其 API 版本。
- API 配置文件是为了让你创建跨多个 Azure 云的模板而创建的。 配置文件设计用来满足对兼容且稳定的接口的需求。
- 配置文件每年发布四次。
- 使用了以下三个配置文件命名约定：
    - **latest**  
        包含全球 Azure 中发布的最新 API 版本。
    - **yyyy-mm-dd-hybrid**  
    每两年发布一次，此版本注重于跨多个云的一致性和稳定性。 此配置文件以实现最佳 Azure Stack 兼容性为目标。
    - **yyyy-mm-dd-profile** 介于最佳稳定性与最新功能之间。

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API 配置文件和 Azure Stack 兼容性

最新 Azure API 配置文件与 Azure Stack 不兼容。 可以使用以下命名约定来标识要将哪些配置文件用于 Azure Stack 解决方案。

**最新**  
此配置文件包含可在全球 Azure 中找到的最新 API 版本，这些版本在 Azure Stack 中不起作用。 **Latest** 包含的重大更改最多。 此配置文件不考虑稳定性以及与其他云的兼容性。 如果尝试使用最新的 API 版本，则应当使用 **Latest** 配置文件。

**Yyyy-mm-dd-hybrid**  
此配置文件在每年的三月份和九月份发布。 此配置文件具有最佳的稳定性以及与各种云的兼容性。 **Yyyy-mm-dd-hybrid** 是以全球 Azure 和 Azure Stack 作为应用目标而设计的。 此配置文件中列出的 Azure API 版本将与 Azure Stack 上列出的相同。 可以使用此配置文件来为混合云解决方案开发代码。

**yyyy-mm-dd-profile**  
此配置文件在六月份和九月份针对全球 Azure 发布。 此配置文件对 Azure Stack 不起作用；通常，其中包含许多重大更改。 虽然它介于最佳稳定性与最新功能之间，但 **Latest** 与此配置文件之间的区别是 **Latest** 将始终包含最新的 API 版本，无论 API 是何时发布的。 例如，如果明天为“计算 API”创建一个新的 API 版本，则该 API 版本将列在 **Latest** 中，但不会列在 **yyyy-mm-dd-profile** 中，因为此配置文件已经存在。  **yyyy-mm-dd-profile** 涵盖了六月份或九月份之前发布的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure Stack 不使用全球 Azure 中提供的最新版 API。 在创建解决方案时，需要为每个 Azure 资源提供程序查找与 Azure Stack 兼容的 API 版本。

你可以使用 API 配置文件，而无需研究 Azure Stack 支持的每个资源提供程序和特定版本。 配置文件会指定一组资源提供程序和 API 版本。 SDK 或者使用 SDK 生成的工具将还原到配置文件中指定的目标 api-version。 使用 API 配置文件可以指定应用到整个模板的配置文件版本，在运行时，Azure 资源管理器会选择正确的资源版本。

API 配置文件可与使用 Azure 资源管理器的工具（例如 PowerShell、Azure CLI、SDK 中提供的代码，以及 Microsoft Visual Studio）配合运行。 工具和 SDK 可以使用配置文件来读取生成应用程序时要包含的模块和库的版本。

例如，如果你使用 PowerShell 创建存储帐户使用**Microsoft.Storage**支持 api 版本 2016年-03-30 和使用 api 版本的 Microsoft.Compute 资源提供程序的 VM 的资源提供程序2015-12-01，需要查找 PowerShell 模块支持 2016年-03-30 为存储和计算哪个模块支持 2015年-02-01，并安装它们。 可以改用配置文件。 使用 cmdlet **Install-Profile *profilename***，然后 PowerShell 会加载正确的模块版本。

同样，在使用 Python SDK 生成基于 Python 的应用程序时，可以指定配置文件。 SDK 将为脚本中指定的资源提供程序加载正确的模块。

开发人员可以专注于编写解决方案。 无需研究哪个 api-version、资源提供程序和云可一起运行，而可以使用配置文件，确定代码可跨所有支持该配置文件的云运行。

## <a name="api-profile-code-samples"></a>API 配置文件代码示例

可以借助代码示例，通过配置文件将采用偏好语言的解决方案与 Azure Stack 相集成。 目前可以找到以下语言的指导和示例：

- **.NET**可以使用.NET API 配置文件来获取资源提供程序包中的每种资源类型的最新、 最稳定版本。 有关详细信息，请参阅[使用.NET 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-net.md)。
- **PowerShell**  
可以使用通过 PowerShell 库提供的 **AzureRM.Bootstrapper** 模块来获取使用 API 版本配置文件所需的 PowerShell cmdlet。 有关信息，请参阅[使用适用于 PowerShell 的 API 版本配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI**  
可将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 有关信息，请参阅[使用 API 版本配置文件的 Azure CLI](azure-stack-version-profiles-azurecli2.md)。
- **GO**  
在 GO SDK 中，配置文件结合了不同服务的不同版本的不同资源类型。 配置文件在 profiles/ 路径下提供，其版本采用 **YYYY-MM-DD** 格式。 有关信息，请参阅[为 GO 使用 API 版本配置文件](azure-stack-version-profiles-go.md)。
- **Ruby**  
用于 Azure Stack 资源管理器的 Ruby SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了采用 Ruby 语言的计算、虚拟网络和存储提供程序。 有关信息，请参阅[将 API 版本配置文件与 Ruby 配合使用](azure-stack-version-profiles-ruby.md)
- **Python**  
Python SDK 支持 API 版本配置文件将不同的云平台（例如 Azure Stack 和 Azure 公有云）用作目标。 可以使用 API 配置文件为混合云创建解决方案。 有关信息，请参阅[将 API 版本配置文件与 Python 配合使用](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [查看配置文件支持的资源提供程序 API 版本的详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
