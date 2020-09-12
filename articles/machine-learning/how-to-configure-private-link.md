---
title: " (预览配置专用终结点) "
titleSuffix: Azure Machine Learning
description: 使用 Azure 专用链接从虚拟网络安全地访问 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 09/03/2020
ms.openlocfilehash: b9b256a3d3c1636cac55bcb1790182240d2199c0
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661882"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a> (预览版为 Azure 机器学习工作区配置 Azure 专用链接) 

本文档介绍如何将 Azure 专用链接与 Azure 机器学习工作区配合使用。 有关为 Azure 机器学习设置虚拟网络的信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md)

> [!IMPORTANT]
> 将 Azure Private Link 与 Azure 机器学习工作区结合使用目前为公共预览版。 此功能仅在 **美国东部**、 **美国中南部** 和 **美国西部 2** 区域提供。 此预览版在提供时没有服务级别协议，不建议用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure 专用链接，可以通过专用终结点连接到工作区。 专用终结点是虚拟网络中的一组专用 IP 地址。 然后，你可以限制工作区访问权限，只允许通过专用 IP 地址访问你的工作区。 专用链接有助于降低数据外泄风险。 若要详细了解专用终结点，请参阅 [Azure 专用链接](/azure/private-link/private-link-overview)一文。

> [!IMPORTANT]
> Azure 专用链接不影响 Azure 控制平面（管理操作），例如删除工作区或管理计算资源。 例如，创建、更新或删除计算目标。 这些操作像往常一样通过公共 Internet 执行。 数据平面操作（如使用 Azure 机器学习 studio、Api (包括) 的已发布管道）或 SDK 使用专用终结点。
>
> 如果使用的是 Mozilla Firefox，则在尝试访问工作区的专用终结点时可能会遇到问题。 此问题可能与 Mozilla 中的 HTTPS 上的 DNS 有关。 建议使用 Google Chrome 的 Microsoft Edge 作为解决方法。

> [!TIP]
> Azure 机器学习计算实例可以与工作区和专用终结点一起使用。 此功能目前在 **美国东部**、 **美国中南部** 和 **美国西部 2** 区域公开预览版。

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>创建使用专用终结点的工作区

> [!IMPORTANT]
> 目前，我们仅支持在创建新的 Azure 机器学习工作区时启用专用终结点。

中的模板 [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) 可用于创建具有专用终结点的工作区。

有关使用此模板（包括专用终结点）的信息，请参阅 [使用 Azure 资源管理器模板创建 Azure 机器学习的工作区](how-to-create-workspace-template.md)。

## <a name="using-a-workspace-over-a-private-endpoint"></a>通过专用终结点使用工作区

由于仅允许从虚拟网络到工作区的通信，因此，使用该工作区的任何开发环境都必须是虚拟网络的成员。 例如，虚拟网络中的虚拟机。

> [!IMPORTANT]
> 为了避免暂时中断连接，Microsoft 建议你在启用专用链接后在连接到工作区的计算机上刷新 DNS 缓存。 

有关 Azure 虚拟机的信息，请参阅[虚拟机文档](/azure/virtual-machines/)。


## <a name="next-steps"></a>后续步骤

有关保护 Azure 机器学习工作区的详细信息，请参阅 [虚拟网络隔离和隐私概述](how-to-network-security-overview.md) 一文。