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
ms.openlocfilehash: 1fb7716ff1b5ce661dff55c3a8dac90a062fad53
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630776"
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>将 Kubernetes 群集部署到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Azure 容器服务 (ACS) Kubernetes 目前为个人预览版。 Azure Stack 操作员需请求 Kubernetes 市场项的访问权限，该项是根据本文中的说明进行操作所必需的。
<!-- Should "Azure Container Services" be replaced globally with Azure Kubernetes Services? It seems the Azure container page is now redirectin to Azure Kubernetes https://azure.microsoft.com/en-us/services/container-service -->

下面的文章着眼于使用 Azure 资源管理器解决方案模板通过单个协调的操作为 Kubernetes 部署和预配资源。 需收集有关 Azure Stack 安装的必需信息，生成模板，然后再部署到云。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

可以使用 Azure 资源管理器模板来安装 Kubernetes，该模板由 Azure Stack 上的 Azure 容器服务 (ACS) 引擎生成。 [Kubernetes](https://kubernetes.io) 是一个开源系统，可以自动部署、缩放和管理容器中的应用程序。 [容器](https://www.docker.com/what-container)受限于映像，与 VM 类似。 与 VM 不同的是，容器映像只包含运行应用程序所需的资源，例如代码、执行代码所需的运行时、特定库以及设置。

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

    此群集不能部署到 Azure Stack **管理员**订阅。 必须使用用户**订阅。 

## <a name="create-a-service-principal-in-azure-ad"></a>在 Azure AD 中创建服务主体

1. 登录到全球 [Azure 门户](http://portal.azure.com)。
1. 检查是否已使用与 Azure Stack 实例关联的 Azure AD 租户登录。
1. 创建 Azure AD 应用程序。

    a. 选择“Azure Active Directory” > “+ 应用注册” > “新建应用程序注册”。

    b. 输入应用程序的“名称”。

    c. 选择“Web 应用/API”。

    d. 输入 `http://localhost` 作为“登录 URL”。

    c. 单击“创建”。

1. 记下“应用程序 ID”。 在创建群集时需要此 ID。 此 ID 称为“服务主体客户端 ID”。

1. 选择“设置” > “密钥”。

    a. 输入“说明”。

    b. 对于“过期”这一项，请选择“永不过期”。

    c. 选择“保存”。 记下密钥字符串。 在创建群集时需要此密钥字符串。 此密钥称为“服务主体客户端机密”。



## <a name="give-the-service-principal-access"></a>为服务主体提供访问权限

为服务主体提供对订阅的访问权限，使该主体能够创建资源。

1.  登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“更多服务” > “用户订阅” > “+ 添加”。

1. 选择已创建的订阅。

1. 选择“访问控制(IAM)”，然后选择“+ 添加”。

1. 选择“所有者”角色。

1. 选择为服务主体创建的应用程序名称。 可能需要在搜索框中键入名称。

1. 单击“ **保存**”。

## <a name="deploy-a-kubernetes-cluster"></a>部署 Kubernetes 群集

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

1. 选择“+新建” > “计算” > “Kubernetes 群集”。 单击“创建”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

1. 在“创建 Kubernetes 群集”中选择“基本设置”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 输入 **Linux VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的**主配置文件 DNS 前缀**。 这必须是特定于区域的名称，例如 `k8s-12345`。 最佳做法是尝试选择与资源组名称相同的名称。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 输入**代理池配置文件计数**。 此计数包含群集中的代理数。 其范围为 1 到 4。

1. 输入**服务主体 ClientId**，供 Kubernetes Azure 云提供程序使用。

1. 输入在创建服务主体应用程序时创建的**服务主体客户端机密**。

1. 输入 **Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

1. 选择**订阅** ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。 这是为 Azure Stack 安装选择的区域。

### <a name="specify-the-azure-stack-settings"></a>指定 Azure Stack 设置

1. 选择“Azure Stack 戳设置”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings.png)

1. 输入**租户 Arm 终结点**。 这是在连接后即可为 Kubernetes 群集创建资源组的 Azure 资源管理器终结点。 需从集成系统的 Azure Stack 操作员处获取此终结点。 对于 Azure Stack 开发工具包 (ASDK)，可以使用 `https://management.local.azurestack.external`。

1. 输入租户的**租户 ID**。 如果不知道如何查找此值，请参阅[获取租户 ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)。 

## <a name="connect-to-your-cluster"></a>连接到你的群集

现在可以连接到群集了。 master 可以在群集资源组中找到，其名称为 `k8s-master-<sequence-of-numbers>`。 使用 SSH 客户端连接到 master。 在 master 上，可以使用 **kubectl**（Kubernetes 命令行客户端）来管理群集。 有关说明，请参阅 [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview)。

你可能还会发现，**Helm** 包管理器适用于将应用安装和部署到群集中。 若要了解如何通过群集来安装和使用 Helm，请参阅 [helm.sh](https://helm.sh/)。

## <a name="next-steps"></a>后续步骤


  [向市场添加 Kubernetes 群集（适用于 Azure Stack 操作员）](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
