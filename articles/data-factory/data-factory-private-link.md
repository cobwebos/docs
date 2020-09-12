---
title: Azure 数据工厂的 azure 专用链接
description: 了解 azure 数据工厂中的 Azure 专用链接工作。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: 164a1005d9382711874b644e14b23d2154d613a0
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596016"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure 数据工厂的 azure 专用链接

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

使用专用链接可以通过专用终结点连接到 Azure 中的各种 PaaS 服务。 有关支持私有链接功能的 PaaS 服务的列表，请参阅 " [专用链接" 文档页](https://docs.microsoft.com/azure/private-link/)。 专用终结点是特定虚拟网络和子网中的专用 IP 地址。

## <a name="secure-communication-between-customer-network-and-azure-data-factory-service"></a>保护客户网络与 Azure 数据工厂服务之间的通信
若要保护 Azure 资源免受公用网络的攻击，或让它们安全地相互通信，可以在云中将 Azure 虚拟网络设置为网络的逻辑表示形式。 还可以通过设置 IPSec VPN (站点到站点) 或 ExpressRoute (专用对等互连) ，将本地网络连接到虚拟网络。 可以在虚拟网络中的本地计算机或虚拟机上安装自承载的 Integration Runtime，以在云数据存储和专用网络中的数据存储之间运行复制活动，或者根据本地网络或 Azure 虚拟网络中的计算资源调度转换活动。 

数据工厂与客户虚拟网络之间需要几个通信通道。


| **Domain** | **端口** | **说明** |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | 控制平面。 数据工厂创作和监视所必需的。 |
| `*.{region}.datafactory.azure.net` | 443 | 自承载 Integration Runtime 要求连接到数据工厂服务。 |
| `*.servicebus.windows.net` | 443 | 用于交互式创作的自承载 Integration Runtime 必需的。 |
| `download.microsoft.com` | 443 | 自承载 Integration Runtime 需要下载更新。 |


通过 azure 数据工厂的 Azure 专用链接支持，你可以在虚拟网络中创建 (PE) 的专用终结点，并启用到特定 Azure 数据工厂的专用连接。 与 Azure 数据工厂服务的通信将通过 Azure 专用链接，该链接提供了安全的专用连接性。 你不需要在虚拟网络或公司防火墙中配置以上的域和端口，这提供了一种更安全的方式来保护你的资源。  

![Azure 数据工厂专用链接体系结构](./media/data-factory-private-link/private-link-architecture.png)

以下是为上述每个通信通道启用专用链接服务的优点：
-  (支持) 你可以在虚拟网络中创作和监视 Azure 数据工厂，即使你会阻止所有出站通信。
-  (支持的) 自承载 Integration Runtime 与 Azure 数据工厂服务之间的命令通信可以在专用网络环境中安全地执行。 自承载 Integration Runtime 与 Azure 数据工厂服务之间的流量通过专用链接进行。 
-  (当前不支持使用自承载 Integration Runtime) 交互创作，请转到 "测试连接"、"浏览文件夹列表和表列表"、"获取架构" 和 "预览数据"。
- 当前不支持 () 如果启用自动更新，则可以从下载中心自动下载新版本的自承载 Integration Runtime。

> [!NOTE]
> 对于当前不支持的功能，仍需要在虚拟网络或企业防火墙中配置上述域和端口。 

> [!WARNING]
> 创建链接服务时，请确保凭据存储在 Azure Key Vault 中。 否则，在 Azure 数据工厂中启用 Private Link Service 时，此方法不起作用。

## <a name="how-to-set-up-private-link-for-azure-data-factory"></a>如何为 Azure 数据工厂设置专用链接
可以使用 Azure 门户、PowerShell 或 Azure CLI 创建专用终结点：

[Portal](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)


你还可以在 Azure 门户中导航到 Azure 数据工厂，并 (PE 创建专用终结点) ：

![创建专用终结点](./media/data-factory-private-link/create-private-endpoint.png)


如果要阻止公共访问此 Azure 数据工厂，并且仅允许通过专用链接访问，则可以在 Azure 门户中禁用 Azure 数据工厂的网络访问：

![创建专用终结点](./media/data-factory-private-link/disable-network-access.png)

> [!NOTE]
> 禁用公共网络访问仅适用于自承载 Integration Runtime，而不适用于 Azure Integration Runtime 和 SSIS Integration Runtime。

> [!NOTE]
> 用户仍可在禁用公共网络访问后通过公共网络访问 Azure 数据工厂门户。

## <a name="next-steps"></a>后续步骤

- [使用 Azure 数据工厂 UI 创建数据工厂](quickstart-create-data-factory-portal.md)

- [Azure 数据工厂简介](introduction.md)

- [Azure 数据工厂中的视觉对象创作](author-visually.md)

