---
title: 为 Azure Marketplace 的虚拟机产品/服务创建技术资产
description: 介绍如何为 Azure 市场中的虚拟机套餐创建技术资产。
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/20/2018
ms.author: pabutler
ms.openlocfilehash: c1ef00f846dfad76629b0603ab79fba17249417c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224530"
---
# <a name="create-technical-assets-for-a-virtual-machine-offer"></a>为虚拟机套餐创建技术资产

本部分引导你为 Azure 市场虚拟机 (VM) 套餐创建并配置技术资产。  VM 包含两个组件：解决方案虚拟硬盘 (VHD)，以及可选的关联数据磁盘。  

- 虚拟硬盘 (VHD) 包含要在 Azure 市场套餐中部署的操作系统和解决方案。 准备 VHD 的过程根据 VM 是基于 Linux、基于 Windows 还是自定义 VM 而有所不同。
- 数据磁盘代表虚拟机的专用持久性存储。 请不要使用解决方案 VHD（例如 `C:` 驱动器）来存储持久性信息。

一个 VM 映像包含一个操作系统磁盘以及零个或更多数据磁盘。 每个磁盘都需要一个 VHD。 即使空白数据磁盘也需要创建 VHD。
必须配置 VM OS、VM 大小、要打开的端口，以及最多 15 个附加的数据磁盘。

> [!TIP] 
> 无论使用何种操作系统，都仅添加 SKU 所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。 

> [!IMPORTANT]
> *请勿在新映像版本中更改磁盘计数。* 如果必须在映像中重新配置数据磁盘，请定义新 SKU。 发布不同磁盘计数的新映像版本可能会在通过 Azure 资源管理器模板和其他方案自动扩展和部署时，中断基于新映像版本的新部署。

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="fundamental-technical-knowledge"></a>基础技术知识

设计、构建和测试这些资产需要一定的时间，并需要 Azure 平台和用于构建产品/服务的技术的技术知识。 除解决方案域外，工程团队还应了解以下 Microsoft 技术： 
-   基本了解 [Azure 服务](https://azure.microsoft.com/services/) 
-   如何[设计和架构 Azure 应用程序](https://azure.microsoft.com/solutions/architecture/)
-   [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking)的实践知识
-   [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)的实践知识
-   [JSON](https://www.json.org/) 的实践知识


## <a name="suggested-tools"></a>建议的工具 

选择以下一种或两种脚本环境来帮助管理 VHD 和 VM：
-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

此外，我们建议将以下工具添加到开发环境： 

-   [Azure 存储资源管理器](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   扩展：[Azure 资源管理器工具](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   扩展：[Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   扩展：[Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

我们还建议在 [Azure 开发人员工具](https://azure.microsoft.com/tools/)页中查看可用的工具；如果使用的是 Visual Studio，请在 [Visual Studio 市场](https://marketplace.visualstudio.com/)中查看。


## <a name="next-steps"></a>后续步骤

本部分中的后续文章可引导你完成创建和注册这些 VM 资产的步骤：

1. [创建 Azure 兼容的虚拟硬盘](./cpp-create-vhd.md)介绍了如何创建与 Azure 兼容的基于 Linux 或 Windows 的 VHD。  其中包含最佳做法，例如，调整大小、修补和准备要上传的 VM。

2. [连接到虚拟机](./cpp-connect-vm.md)介绍了如何远程连接并登录到新建的 VM。  此文还介绍了如何停止 VM，以节省使用费。

3. [配置虚拟机](./cpp-configure-vm.md)介绍了如何选择正确的 VHD 大小、通用化映像、应用最新的更新（修补程序），以及计划自定义配置。

4. [从虚拟硬盘部署虚拟机](./cpp-deploy-vm-vhd.md)介绍了如何从 Azure 部署的 VHD 注册 VM。  其中列出了所需的工具，以及如何使用这些工具创建用户 VM 映像，然后使用 [Microsoft Azure 门户](https://ms.portal.azure.com/)或 PowerShell 脚本将其部署到 Azure。 

5. [认证虚拟机映像](./cpp-certify-vm.md)介绍了如何测试并提交 VM 映像，供 Azure 市场认证。 其中还介绍了可在何处获取“Azure 认证”测试工具，以及如何使用此工具来认证 VM 映像。 

6. [获取 SAS URI](./cpp-get-sas-uri.md) 介绍了如何获取 VM 映像的共享访问签名 (SAS) URI。
 
作为一篇支持文章，[常见共享访问签名 URL 问题](./cpp-common-sas-url-issues.md)列出了在使用 SAS URI 和可能的相应解决方案时可能会遇到的一些常见问题。

完成所有这些步骤后，可将 [VM 套餐发布](./cpp-publish-offer.md)到 Azure 市场。
