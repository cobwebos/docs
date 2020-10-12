---
title: 在 Azure 门户中创建 Azure Arc 数据控制器
description: 在 Azure 门户中创建 Azure Arc 数据控制器
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 297efa83fb1563e3a360f652a6ac1bc2b1679998
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934637"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>在 Azure 门户中创建 Azure Arc 数据控制器

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>简介

可以使用 Azure 门户创建 Azure Arc 数据控制器。

虽然要创建或管理的资源在 Azure 基础结构之外，但许多 Azure Arc 创建体验都是在 Azure 门户中开始的。 用户体验模式在这种情况下，尤其是在 Azure 和你的环境之间没有直接连接时，请使用 Azure 门户生成脚本，然后在环境中下载并执行该脚本，以建立返回到 Azure 的安全连接。 例如，启用了 Azure Arc 的服务器遵循此模式来 [创建启用 Arc 的服务器](../servers/onboard-portal.md)。

目前，假设预览版仅支持启用了 Azure Arc 的数据服务的间接连接模式，则可以使用 Azure 门户为你生成笔记本，然后可以在 Azure Data Studio 中下载并运行 Kubernetes 群集。 将来，当直接连接模式可用时，你将能够直接从 Azure 门户预配数据控制器。 你可以阅读有关 [连接模式](connectivity.md)的详细信息。

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>使用 Azure 门户创建 Azure Arc 数据控制器

按照以下步骤使用 Azure 门户和 Azure Data Studio 创建 Azure Arc 数据控制器。

1. 首先，请登录到 [Azure 门户 marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller)。  将筛选 marketplace 搜索结果以显示 "Azure Arc 数据控制器"。
2. 如果第一步没有输入搜索条件，则为。 请输入搜索结果，然后单击 "Azure Arc 数据控制器"。
3. 从 marketplace 中选择 "Azure 数据控制器" 磁贴。
4. 单击“创建”按钮。
5. 查看创建 Azure Arc 数据控制器的要求，并安装任何缺少的必备软件，如 Azure Data Studio 和 kubectl。
6. 单击 " **数据控制器详细信息** " 按钮。
7. 选择订阅、资源组和 Azure 位置，就像对在 Azure 门户中创建的任何其他资源一样。 在这种情况下，你选择的 Azure 位置将是存储资源元数据的位置。  将在所选的任何基础结构上创建资源本身。 不需要在 Azure 基础结构上。
8. 输入数据控制器的名称。
9. 目前，预览版仅支持间接连接模式。
10. 选择部署配置文件。
11. 单击 " **在 Azure Studio 中打开** " 按钮。
12. 在下一个屏幕上，你将看到所选内容的摘要和生成的笔记本。  可以单击 " **下载预配笔记本** " 按钮下载笔记本。
13. 在 Azure Data Studio 中打开笔记本，并单击顶部的 " **全部运行** " 按钮。
14. 按照笔记本中的提示和说明完成数据控制器创建。

## <a name="monitoring-the-creation-status"></a>监视创建状态

创建控制器需要几分钟才能完成。 可以通过以下命令在另一个终端窗口中监视进度：

> [!NOTE]
>  下面的示例命令假设你创建了一个名为 "arc" 的数据控制器和 Kubernetes 命名空间。  如果使用了不同的命名空间/数据控制器名称，则可以将 "arc" 替换为您的名称。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

还可以通过运行如下命令来检查任何特定 pod 的创建状态。  这对于解决任何问题特别有用。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>创建问题疑难解答

如果在创建 troubles 的过程中遇到任何问题，请参阅 [故障排除指南](troubleshoot-guide.md)。
