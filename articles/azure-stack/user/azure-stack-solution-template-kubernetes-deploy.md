---
title: 将 Kubernetes 部署到 Azure Stack |Microsoft Docs
description: 了解如何将 Kubernetes 部署到 Azure Stack。
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
ms.date: 09/25/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 89c72e21733b01a3e42c0e58d65cb7877e47d374
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47163488"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>将 Kubernetes 部署到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 处于预览状态。 Azure Stack 操作员需要请求访问所需执行本文中说明的 Kubernetes 群集 Marketplace 项。

下面的文章着眼于使用 Azure 资源管理器解决方案模板通过单个协调的操作为 Kubernetes 部署和预配资源。 需收集有关 Azure Stack 安装的必需信息，生成模板，然后再部署到云。 请注意该模板不提供全球 Azure 中的相同托管的 AKS 服务。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

你可以安装 Kubernetes 中通过 Azure 资源管理器模板在 Azure Stack 上的 ACS 引擎生成的。 [Kubernetes](https://kubernetes.io) 是一个开源系统，可以自动部署、缩放和管理容器中的应用程序。 [容器](https://www.docker.com/what-container)受限于映像，与 VM 类似。 与 VM 不同的是，容器映像只包含运行应用程序所需的资源，例如代码、执行代码所需的运行时、特定库以及设置。

可以使用 Kubernetes 执行以下操作：

- 开发可以大规模伸缩、升级并可快速部署的应用程序。 
- 通过不同的 Helm 应用程序简化应用程序的设计并改进其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松监视和诊断具有缩放和升级功能的应用程序的运行状况。

## <a name="prerequisites"></a>必备组件 

开始之前，请确保你有适当的权限且 Azure Stack 已就绪。

1. 验证是否可以在 Azure Active Directory (Azure AD) 租户中创建应用程序。 需要这些权限才能进行 Kubernetes 部署。

    有关如何检查权限的说明，请参阅[检查 Azure Active Directory 权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)。

1. 生成一个 SSH 公钥和私钥对，用于登录到 Azure Stack 上的 Linux VM。 在创建群集时需要此公钥。

    有关如何生成密钥的说明，请参阅 [SSH 密钥生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

1. 检查你在 Azure Stack 租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址来添加新的应用程序。

    此群集不能部署到 Azure Stack **管理员**订阅。 必须使用**用户**订阅。 

## <a name="create-a-service-principal-in-azure-ad"></a>在 Azure AD 中创建服务主体

1. 登录到全球 [Azure 门户](http://portal.azure.com)。

1. 检查是否已使用与 Azure Stack 实例关联的 Azure AD 租户登录。 可以通过单击 Azure 工具栏中的筛选器图标切换登录。

    ![选择 AD 租户](media/azure-stack-solution-template-kubernetes-deploy/tenantselector.png)

1. 创建 Azure AD 应用程序。

    a. 选择“Azure Active Directory” > “+ 应用注册” > “新建应用程序注册”。

    b. 输入应用程序的“名称”。

    c. 选择“Web 应用/API”。

    d. 输入 `http://localhost` 作为“登录 URL”。

    c. 单击“创建”。

1. 请记下**应用程序 ID**。 在创建群集时需要此 ID。 此 ID 称为“服务主体客户端 ID”。

1. 选择“设置” > “密钥”。

    a. 输入“说明”。

    b. 对于“过期”这一项，请选择“永不过期”。

    c. 选择“保存”。 记下密钥字符串。 在创建群集时需要此密钥字符串。 此密钥称为“服务主体客户端机密”。


## <a name="give-the-service-principal-access"></a>为服务主体提供访问权限

为服务主体提供对订阅的访问权限，使该主体能够创建资源。

1.  登录到[Azure Stack 门户](https://portal.local.azurestack.external/)。

1. 选择**所有服务** > **订阅**。

1. 选择为使用 Kubernetes 群集创建您的运营商的订阅。

1. 选择“访问控制(IAM)”，然后选择“+ 添加”。

1. 选择**参与者**角色。

1. 选择为服务主体创建的应用程序名称。 可能需要在搜索框中键入名称。

1. 单击“ **保存**”。

## <a name="deploy-a-kubernetes"></a>部署 Kubernetes

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

1. 选择 **+ 创建资源** > **计算** > **Kubernetes 群集**。 单击“创建”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基础

1. 选择**基础知识**中创建 Kubernetes 群集。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择**订阅** ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。 这是为 Azure Stack 安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 群集设置

1. 选择**Kubernetes 群集设置**中创建 Kubernetes 群集。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. 输入 **Linux VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的**主配置文件 DNS 前缀**。 这必须是特定于区域的名称，例如 `k8s-12345`。 最佳做法是尝试选择与资源组名称相同的名称。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择**Kubernetes 主池配置文件计数**。 计数包含主池中的节点数。 可以有从 1 到 7。 此值应为奇数。

1. 选择**Kubernetes 主 Vm 的 VMSize**。

1. 选择**Kubernetes 节点池配置文件计数**。 此计数包含群集中的代理数。 

1. 选择**存储配置文件**。 你可以选择**Blob 磁盘**或**托管磁盘**。 这将指定的 VM 大小的 Kubernetes 节点 Vm。 

1. 输入**服务主体 ClientId**，供 Kubernetes Azure 云提供程序使用。 客户端 ID 标识为应用程序 ID 时您创建服务主体。

1. 输入**服务主体客户端机密**创建服务主体时创建。

1. 输入 **Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

### <a name="3-summary"></a>3.摘要

1. 选择摘要。 在边栏选项卡显示的验证消息的 Kubernetes 群集配置设置。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 查看你的设置。

3. 选择**确定**以部署群集。

## <a name="connect-to-your-cluster"></a>连接到你的群集

现在可以连接到群集了。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到 master。 在 master 上，可以使用 **kubectl**（Kubernetes 命令行客户端）来管理群集。 有关说明，请参阅 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你可能还会发现，**Helm** 包管理器适用于将应用安装和部署到群集中。 若要了解如何通过群集来安装和使用 Helm，请参阅 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤

[将 Kubernetes （适用于 Azure Stack 操作员） 添加到 Marketplace](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
