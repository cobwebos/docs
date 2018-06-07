---
title: 将 Kubernetes 群集部署到 Azure Stack | Microsoft Docs
description: 了解如何将 Kubernetes 群集部署到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 43c0b7c87f9ee1cd33da3d617747c11dc120e51a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823616"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>将 Kubernetes 群集部署到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Azure 容器服务 (ACS) Kubernetes 目前为个人预览版。 Azure Stack 操作员需请求 Kubernetes Marketplace 项的访问权限，该项是根据本文中的说明进行操作所必需的。

下面的文章着眼于使用 Azure 资源管理器解决方案模板通过单个协调的操作为 Kubernetes 部署和预配资源。 需收集有关 Azure Stack 安装的必需信息，生成模板，然后再部署到云。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

你可以安装 Kubernetes 使用由 Azure 堆栈上的 Azure 容器服务 (ACS) 引擎生成的 Azure 资源管理器模板。 [Kubernetes](https://kubernetes.io) 是一个开源系统，可以自动部署、缩放和管理容器中的应用程序。 [容器](https://www.docker.com/what-container)受限于映像，与 VM 类似。 与 VM，不同的容器映像仅包含它需要运行应用程序，如代码运行时执行代码、 特定的库和设置的资源。

可以使用 Kubernetes 执行以下操作：

- 开发可以大规模伸缩、升级并可快速部署的应用程序。 
- 通过不同的 Helm 应用程序简化应用程序的设计并改进其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松监视和诊断具有缩放和升级功能的应用程序的运行状况。

## <a name="prerequisites"></a>必备组件 

开始之前，请确保你有适当的权限且 Azure Stack 已就绪。

1. 验证是否可以在 Azure Active Directory (Azure AD) 租户中创建应用程序。 需要这些权限才能进行 Kubernetes 部署。

    有关如何检查权限的说明，请参阅[检查 Azure Active Directory 权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)。

2. 生成一个 SSH 公钥和私钥对，用于登录到 Azure Stack 上的 Linux VM。 在创建群集时需要此公钥。

    有关如何生成密钥的说明，请参阅 [SSH 密钥生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

3. 检查你在 Azure Stack 租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址来添加新的应用程序。

    无法将群集部署到 Azure 堆栈**管理员**订阅。 你必须使用用户 * * 订阅。 

## <a name="create-a-service-principal-in-azure-ad"></a>在 Azure AD 中创建服务主体

1. 登录到全球 [Azure 门户](http://portal.azure.com)。
2. 登录使用 Azure AD 租户与 Azure 堆栈实例关联的检查。
3. 创建 Azure AD 应用程序。

    a. 选择“Azure Active Directory” > “+ 应用注册” > “新建应用程序注册”。

    b. 输入应用程序的“名称”。

    c. 选择**Web 应用程序 / API**。

    d. 输入 `http://localhost` 作为“登录 URL”。

    c. 单击“创建”。

4. 记下“应用程序 ID”。 在创建群集时需要此 ID。 此 ID 称为“服务主体客户端 ID”。

5. 选择“设置” > “密钥”。

    a. 输入“说明”。

    b. 对于“过期”这一项，请选择“永不过期”。

    c. 选择“保存”。 记下密钥字符串。 在创建群集时需要此密钥字符串。 密钥引用了作为**服务主体客户端机密**。



## <a name="give-the-service-principal-access"></a>为服务主体提供访问权限

为服务主体提供对订阅的访问权限，使该主体能够创建资源。

1.  登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 选择“更多服务” > “用户订阅” > “+ 添加”。

3. 选择已创建的订阅。

4. 选择“访问控制(IAM)”，然后选择“+ 添加”。

5. 选择“所有者”角色。

6. 选择为服务主体创建的应用程序名称。 可能需要在搜索框中键入名称。

7. 单击“ **保存**”。

## <a name="deploy-a-kubernetes-cluster"></a>部署 Kubernetes 群集

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

2. 选择“+新建” > “计算” > “Kubernetes 群集”。 单击“创建”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

3. 选择**基础知识**中创建 Kubernetes 群集。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

2. 输入**Linux VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

3. 输入**SSH 公钥**用于到 DVM Kubernetes 群集的一部分创建的所有 Linux 计算机的授权。

4. 输入**母版配置文件 DNS 前缀**区域中唯一。 这必须是特定于区域的名称，例如 `k8s-12345`。 最佳做法是尝试选择与资源组名称相同的名称。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

5. 输入**代理池配置文件计数**。 计数包含群集中的代理数。 可以有从 1 到 4。

6. 输入**服务主体 ClientId** Kubernetes Azure 云提供程序使用此。

7. 输入**服务主体客户端机密**时创建服务主体的应用程序创建。

8. 输入 **Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

9. 选择你**订阅**id。

10. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

11. 选择**位置**的资源组。 这是你的 Azure 堆栈安装你选择的区域。

### <a name="specify-the-azure-stack-settings"></a>指定 Azure 堆栈设置

1. 选择**Azure 堆栈戳设置**。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

2. 输入**租户 Arm 终结点**。 这是在连接后即可为 Kubernetes 群集创建资源组的 Azure 资源管理器终结点。 需从集成系统的 Azure Stack 操作员处获取此终结点。 对于 Azure Stack 开发工具包 (ASDK)，可以使用 `https://management.local.azurestack.external`。

3. 输入**租户 ID**租户。 如果不知道如何查找此值，请参阅[获取租户 ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)。 

## <a name="connect-to-your-cluster"></a>连接到你的群集

现在可以连接到群集了。 Master 可以在群集资源组中，找到和名为`k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到主服务器。 你可使用了主机， **kubectl**，Kubernetes 命令行客户端来管理你的群集。 有关说明，请参阅[Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你可能会发现**舵**程序包管理器用于安装和将应用部署到你的群集。 有关安装和使用你的群集舵的说明，请参阅[helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤

[向 Marketplace 添加 Kubernetes 群集（适用于 Azure Stack 操作员）](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
