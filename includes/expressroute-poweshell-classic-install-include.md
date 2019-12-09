---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926225"
---
安装最新版本的 Azure 服务管理 (SM) PowerShell 模块和 ExpressRoute 模块。 不能使用 Azure CloudShell 环境来运行 SM 模块。

1. 按照安装[服务管理模块](/powershell/azure/servicemanagement/install-azure-ps)一文中的说明安装 Azure 服务管理模块。 如果已安装 Az 或 RM 模块，请确保使用 "-AllowClobber"。
2. 导入已安装的模块。 使用以下示例时，请调整路径，以反映已安装的 PowerShell 模块的位置和版本。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. 若要登录到 Azure 帐户，请打开具有提升权限的 PowerShell 控制台，并连接到你的帐户。 使用以下示例来帮助你使用服务管理模块进行连接：

   ```powershell
   Add-AzureAccount
   ```