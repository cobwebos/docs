---
title: '访问 Azure 门户 (预览版中的 Kubernetes 资源) '
description: 了解如何与 Kubernetes 资源交互，以从 Azure 门户管理 Azure Kubernetes Service (AKS) 群集。
services: container-service
author: laurenhughes
ms.topic: article
ms.date: 09/21/2020
ms.author: lahugh
ms.openlocfilehash: 6a9567669445cb5aa94c1108051c961a216fabad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335596"
---
# <a name="access-kubernetes-resources-from-the-azure-portal-preview"></a>访问 Azure 门户 (预览版中的 Kubernetes 资源) 

Azure 门户包括 Kubernetes 资源查看器 (预览版) ，以便轻松访问 Azure Kubernetes Service (AKS) 群集中的 Kubernetes 资源。 从 Azure 门户查看 Kubernetes 资源可减少 Azure 门户和命令行工具之间的上下文切换 `kubectl` ，从而简化了查看和编辑 Kubernetes 资源的体验。 资源查看器当前包括多个资源类型，例如部署、箱和副本集。

Azure 门户中的 Kubernetes 资源视图将替换为弃用而设置的 [AKS 仪表板加载项][kubernetes-dashboard]。

>[!NOTE]
>[专用 Azure Kubernetes 服务群集](https://docs.microsoft.com/azure/aks/private-clusters)目前不支持 capabilty。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>必备条件

若要查看 Azure 门户中的 Kubernetes 资源，需要一个 AKS 群集。 支持任何群集，但是，如果使用 Azure Active Directory (Azure AD) 集成，则群集必须使用 [AKS 托管的 Azure AD 集成][aks-managed-aad]。 如果群集使用旧版 Azure AD，可以在门户中或使用 [Azure CLI][cli-aad-upgrade]升级群集。

## <a name="view-kubernetes-resources"></a>查看 Kubernetes 资源

若要查看 Kubernetes 资源，请导航到 Azure 门户中的 AKS 群集。 使用左侧的导航窗格来访问资源。 资源包括：

- **命名空间** 显示群集的命名空间。 "命名空间" 列表顶部的筛选器提供了一种快速筛选和显示命名空间资源的方法。
- **工作负载** 显示有关部署到群集的部署、盒、副本集和守护程序集的信息。 下面的屏幕截图显示了一个示例 AKS 群集中的默认系统箱。
- **服务和恒温器** 显示群集的所有服务和入口资源。

:::image type="content" source="media/kubernetes-portal/workloads.png" alt-text="Azure 门户中显示的 Kubernetes pod 信息。" lightbox="media/kubernetes-portal/workloads.png":::

### <a name="deploy-an-application"></a>部署应用程序

在此示例中，我们将使用示例 AKS 群集从 [AKS 快速入门][portal-quickstart]部署 Azure 投票应用程序。

1. 从任何资源视图 (命名空间、工作负荷或服务和恒温器) 中选择 " **添加** "。
1. 从 [AKS 快速入门][portal-quickstart]中粘贴 Azure 投票应用程序的 YAML。
1. 选择 YAML 编辑器底部的 " **添加** "，以部署应用程序。 

添加 YAML 文件后，资源查看器将显示已创建的 Kubernetes 服务：内部服务 (azure-投票) ，外部服务 (azure 投票) 来访问 Azure 投票应用程序。 外部服务包含链接的外部 IP 地址，因此你可以轻松地在浏览器中查看该应用程序。

:::image type="content" source="media/kubernetes-portal/portal-services.png" alt-text="Azure 门户中显示的 Kubernetes pod 信息。" lightbox="media/kubernetes-portal/portal-services.png":::

### <a name="monitor-deployment-insights"></a>监视部署见解

已启用 [容器 Azure Monitor 的][enable-monitor] AKS 群集可快速查看部署见解。 在 Kubernetes 资源视图中，用户可以查看各个部署的实时状态，包括 CPU 和内存使用情况，以及转换为 Azure 监视器以获取更深入的信息。 下面是一个示例 AKS 群集的部署见解示例：

:::image type="content" source="media/kubernetes-portal/deployment-insights.png" alt-text="Azure 门户中显示的 Kubernetes pod 信息。" lightbox="media/kubernetes-portal/deployment-insights.png":::

## <a name="edit-yaml"></a>编辑 YAML

Kubernetes 资源视图还包括一个 YAML 编辑器。 内置的 YAML 编辑器意味着可以从门户中更新或创建服务和部署，并立即应用更改。

:::image type="content" source="media/kubernetes-portal/service-editor.png" alt-text="Azure 门户中显示的 Kubernetes pod 信息。" **保存**"，确认更改，然后重新保存来应用更改。

>[!WARNING]
> 不建议通过 UI 或 CLI 执行直接的生产更改， [ (CI) 和持续部署 (CD) 最佳实践](kubernetes-action.md)。 Azure 门户 Kubernetes 管理功能和 YAML 编辑器在开发和测试设置中用于学习和试验新部署。

## <a name="troubleshooting"></a>疑难解答

本部分介绍常见问题和故障排除步骤。

### <a name="unauthorized-access"></a>未经授权的访问

若要访问 Kubernetes 资源，你必须有权访问 AKS 群集、Kubernetes API 和 Kubernetes 对象。 确保你是群集管理员或具有访问 AKS 群集的适当权限的用户。 有关群集安全性的详细信息，请参阅 [AKS 的访问和标识选项][concepts-identity]。

>[!NOTE]
> Azure 门户中的 kubernetes 资源视图仅受 [托管的启用 aad 的群集](managed-aad.md) 或未启用 aad 的群集支持。 如果你使用的是已启用托管 AAD 的群集，则你的 AAD 用户或标识除了需要请求[用户 `kubeconfig` ](control-kubeconfig-access.md)的权限外，还需要具有各自的角色/角色绑定来访问 kubernetes API。

### <a name="enable-resource-view"></a>启用资源视图

对于现有群集，可能需要启用 Kubernetes 资源视图。 若要启用资源视图，请按照群集的门户中的提示进行操作。

:::image type="content" source="media/kubernetes-portal/enable-resource-view.png" alt-text="Azure 门户中显示的 Kubernetes pod 信息。" lightbox="media/kubernetes-portal/enable-resource-view.png":::

> [!TIP]
> 可以添加 [API 服务器已授权 IP 范围](api-server-authorized-ip-ranges.md) AKS 功能，以便限制 API 服务器仅访问防火墙的公共终结点。 此类群集的另一个选项是进行更新 `--api-server-authorized-ip-ranges` ，以包括从其浏览)  (的本地客户端计算机或 IP 地址范围的访问权限。 要允许此访问，需要计算机的公共 IPv4 地址。 可以通过以下命令找到此地址，或在 internet 浏览器中搜索 "我的 IP 地址是什么"。
```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

## <a name="next-steps"></a>后续步骤

本文介绍了如何访问 AKS 群集的 Kubernetes 资源。 请参阅 [部署和 YAML 清单][deployments] ，进一步了解群集资源以及使用 Kubernetes 资源查看器访问的 YAML 文件。

<!-- LINKS - internal -->
[kubernetes-dashboard]: kubernetes-dashboard.md
[concepts-identity]: concepts-identity.md
[portal-quickstart]: kubernetes-walkthrough-portal.md#run-the-application
[deployments]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[aks-managed-aad]: managed-aad.md
[cli-aad-upgrade]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
[enable-monitor]: ../azure-monitor/insights/container-insights-enable-existing-clusters.md
