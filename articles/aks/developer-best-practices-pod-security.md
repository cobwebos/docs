---
title: 开发人员最佳做法 - 保护 Azure Kubernetes 服务 (AKS) 中的 Pod
description: 了解开发人员保护 Azure Kubernetes 服务 (AKS) 中的 Pod 的最佳做法
services: container-service
ms.topic: conceptual
ms.date: 07/28/2020
ms.author: zarhoads
ms.openlocfilehash: bd6891ff4d15dc326c846efbaa37aea997ef2e17
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320674"
---
# <a name="best-practices-for-pod-security-in-azure-kubernetes-service-aks"></a>保护 Azure Kubernetes 服务 (AKS) 中的 Pod 的最佳做法

在 Azure Kubernetes 服务 (AKS) 中开发和运行应用程序时，Pod 的安全性是需要考虑的一个重要方面。 设计应用程序时，应以所需权限最少为原则。 客户最看重的是私人数据的安全。 你不会希望将数据库连接字符串、密钥或机密和证书等凭据暴露给外部世界，因为攻击者可能利用这些机密进行恶意攻击。 请勿将这些凭据添加到代码中，或将其嵌入容器映像中。 这样会有暴露的风险，并且会限制凭证的轮换能力，因为轮换时需要重建容器映像。

这篇有关最佳做法的文章重点介绍如何保护 AKS 中的 Pod。 学习如何：

> [!div class="checklist"]
> * 使用 Pod 安全性上下文来限制对进程和服务，或权限提升的访问权限
> * 使用 Pod 托管的标识通过其他 Azure 资源进行身份验证
> * 从数字保管库（如 Azure Key Vault）请求和检索凭据

还可阅读有关[群集安全性][best-practices-cluster-security]和[容器映像管理][best-practices-container-image-management]的最佳做法。

## <a name="secure-pod-access-to-resources"></a>保护 Pod 对资源的访问权限

**最佳做法指南** - 要作为其他用户或组运行，并限制对基础节点进程和服务的访问权限，请定义 Pod 安全性上下文设置。 请分配所需的最少权限。

为使应用程序正常运行，Pod 应作为已定义的用户或组运行，而不是根用户或组。 通过 Pod 或容器的 `securityContext`，可定义 runAsUser 或 fsGroup 等设置，以承担相应权限 。 仅分配所需的用户或组权限，不要使用安全性上下文来承担其他权限。 runAsUser、特权提升和其他 Linux 功能设置仅在 Linux 节点和 Pod 上可用。

作为非根用户运行时，容器无法绑定到 1024 下的特权端口。 此时可使用 Kubernetes 服务掩盖应用程序正在特定端口上运行这一事实。

Pod 安全性上下文还可定义用于访问进程和服务的其他功能或权限。 可设置以下常见安全性上下文定义：

* allowPrivilegeEscalation 定义 Pod 是否可承担根特权。 设计应用程序，将此设置始终设为 false。
* Linux 功能可使 Pod 访问基础节点进程。 请小心分配这些功能。 请分配所需的最少权限。 有关详细信息，请参阅 [Linux 功能][linux-capabilities]。
* SELinux 标签是一个 Linux 内核安全模块，允许你定义服务、进程和文件系统访问权限的访问策略。 同样，请分配所需的最少权限。 有关详细信息，请参阅 [Kubernetes 中的 SELinux 选项][selinux-labels]

以下示例 Pod YAML 清单设置了安全性上下文设置，给出了以下定义：

* Pod 以 ID 为 1000 的用户身份和 ID 为 2000 的部分组运行 
* 无法提升特权，无法使用 `root`
* 允许 Linux 功能访问网络接口和主机的实时（硬件）时钟

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    fsGroup: 2000
  containers:
    - name: security-context-demo
      image: nginx:1.15.5
      securityContext:
        runAsUser: 1000
        allowPrivilegeEscalation: false
        capabilities:
          add: ["NET_ADMIN", "SYS_TIME"]
```

与群集操作员共同确定所需安全性上下文设置。 尝试设计应用程序，以尽量减少其他权限并访问 Pod 要求。 群集操作员还可实施其他安全功能来限制使用 AppArmor 和 seccomp（安全计算）进行的访问。 有关详细信息，请参阅[保护容器对资源的访问][apparmor-seccomp]。

## <a name="limit-credential-exposure"></a>避免凭据暴露

**最佳操作指南** - 不要在应用程序代码中定义凭据。 使用 Azure 资源的托管标识，让 Pod 请求访问其他资源。 还应使用数字保管库（如 Azure Key Vault）来存储和检索数字密钥和凭据。 Pod 托管标识仅适用于 Linux Pod 和容器映像。

要避免凭据在应用程序代码中暴露，请勿使用固定或共享凭据。 不应直接在代码中包含凭证或密钥。 如果凭据暴露，则需更新并重新部署应用程序。 更好的做法是，为 Pod 提供自己的标识，让它自行进行身份验证，或自动从数字保管库中检索凭据。

### <a name="use-azure-container-compute-upstream-projects"></a>使用 Azure 容器计算上游项目

> [!IMPORTANT]
> Azure 技术支持不支持相关 AKS 开源项目。 它们是为用户提供的，可自行安装到群集中并从我们的社区收集反馈。

通过以下[相关 AKS 开源项目][aks-associated-projects]，你可以自动对 Pod 进行身份验证或从数字保管库请求凭据和密钥。 这些项目由 Azure 容器计算上游团队维护，并且是[范围更广的可用项目列表](https://github.com/Azure/container-compute-upstream/blob/master/README.md#support)的一部分。

 * [Azure Active Directory Pod 标识][aad-pod-identity]
 * [适用于 Secrets Store CSI 驱动程序的 Azure 密钥保管库提供程序](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)

#### <a name="use-pod-managed-identities"></a>使用 Pod 托管标识

Azure 资源的托管标识允许 Pod 向支持它的 Azure 服务（如存储或 SQL）验证自身的身份。 已向该 Pod 分配 Azure 标识，允许 Pod 对 Azure Active Directory 进行身份验证并接收数字令牌。 可向其他 Azure 服务展示此数字令牌，以检查该 Pod 是否有权访问该服务并执行所需操作。 采用此方法时，对于数据库连接字符串等，无需使用机密。 下图显示了简化后的 Pod 托管标识工作流：

![Azure 中简化后的 Pod 托管标识工作流](media/developer-best-practices-pod-security/basic-pod-identity.png)

使用托管标识，应用程序代码无需包含凭据即可访问 Azure 存储等服务。 由于每个 Pod 都使用自己的标识进行身份验证，因此可审核并评价访问权限。 如果应用程序与其他 Azure 服务连接，请使用托管标识来限制凭据重用，避免凭据暴露。

有关 Pod 标识的详细信息，请参阅[配置 AKS 群集以在应用程序中使用 Pod 托管标识][aad-pod-identity]

#### <a name="use-azure-key-vault-with-secrets-store-csi-driver"></a>将 Azure 密钥保管库与 Secrets Store CSI 驱动程序结合使用

使用 Pod 标识项目可向提供支持的 Azure 服务进行身份验证。 若自己的服务或应用程序没有 Azure 资源托管标识，仍可使用凭据或密钥进行身份验证。 可使用数字保管库来存储这些机密内容。

当应用程序需要凭据时，它们会与数字保管库通信，检索最新的机密内容，然后连接到所需的服务。 此数字保管库可以是 Azure Key Vault。 下图显示了使用 Pod 托管标识从 Azure Key Vault 检索凭据的简化工作流：

![使用 Pod 托管标识从 Key Vault 检索凭据的简化工作流](media/developer-best-practices-pod-security/basic-key-vault.png)

使用 Key Vault，可存储并定期轮换凭据、存储帐户密钥或证书等机密。 可使用[适用于 Secrets Store CSI 驱动程序的 Azure 密钥保管库提供程序](https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage)将 Azure 密钥保管库与 AKS 群集集成。 Secrets Store CSI 驱动程序允许 AKS 群集以本机方式检索密钥保管库中的机密内容，并仅将其安全地提供给发出请求的 Pod。 与群集操作员一起将 Secrets Store CSI 驱动程序部署到 AKS 工作器节点上。 可使用 Pod 托管标识来请求访问密钥保管库，并通过 Secrets Store CSI 驱动程序检索所需的机密内容。

带有 Secrets Store CSI 驱动程序的 Azure 密钥保管库可用于要求使用 Kubernetes 版本 1.16 或更高版本的 Linux 节点和 Pod。 Windows 节点和 Pod 要求使用 Kubernetes 版本 1.18 或更高版本。

## <a name="next-steps"></a>后续步骤

本文重点介绍了如何保护 Pod。 若要实施其中某些做法，请参阅以下文章：

* [将 Azure 资源托管标识与 AKS 配合使用][aad-pod-identity]
* [将 Azure 密钥保管库与 AKS 集成][aks-keyvault-csi-driver]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity#demo
[aks-keyvault-csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure#usage
[linux-capabilities]: http://man7.org/linux/man-pages/man7/capabilities.7.html
[selinux-labels]: https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.18/#selinuxoptions-v1-core
[aks-associated-projects]: https://github.com/Azure/AKS/blob/master/previews.md#associated-projects

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[aks-pod-identities]: operator-best-practices-identity.md#use-pod-identities
[apparmor-seccomp]: operator-best-practices-cluster-security.md#secure-container-access-to-resources
