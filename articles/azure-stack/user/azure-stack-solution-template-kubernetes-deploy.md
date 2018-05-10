---
title: 将实现 Kubernetes 群集部署到 Azure 的堆栈 |Microsoft 文档
description: 了解如何将 Kubernetes 群集部署到 Azure 堆栈。
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
ms.date: 05/08/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 71d920ea1c143ed65510b77e1804e9c5e38cc180
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="deploy-a-kubernetes-cluster-to-azure-stack"></a>将实现 Kubernetes 群集部署到 Azure 堆栈

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure 堆栈上的 Azure 容器服务 (ACS) Kubernetes 是处于特邀预览阶段。 Azure 堆栈运算符将需要请求对 Kubernetes 应用商店项执行本文中的说明所需的访问。

以下文章查看使用 Azure 资源管理器解决方案模板来部署和设置的资源为 Kubernetes 在单个协调的操作。 你将需要收集有关你的 Azure 堆栈安装所需的信息生成模板，并随后部署到云。

## <a name="kubernetes-and-containers"></a>Kubernetes 和容器

你可以在 Azure 堆栈上安装 Azure 容器服务 (ACS) Kubernetes。 [Kubernetes](https://kubernetes.io)是一种用于自动部署，开放源代码系统缩放和管理容器中的应用程序。 A[容器](https://www.docker.com/what-container)包含在映像中，类似于 VM。 与 VM 的容器映像只是示例包含它需要运行应用程序，如代码运行时执行代码、 特定的库和设置的资源。

你可以使用 Kubernetes 到：

- 开发可大规模缩放、 可升级，应用程序可以部署以秒为单位。 
- 简化你的应用程序的设计，并通过不同的舵应用程序来提高其可靠性。 [Helm](https://github.com/kubernetes/helm) 是一种开放源打包工具，有助于安装和管理 Kubernetes 应用程序的生命周期。
- 轻松监视和诊断应用程序与缩放的运行状况和升级功能。

## <a name="prerequisites"></a>必备组件 

若要开始，确保拥有正确权限，并且 Azure 堆栈已就绪。

1. 验证可以在 Azure Active Directory (Azure AD) 租户中创建应用程序。 Kubernetes 部署需要这些权限。

    检查你的权限的说明，请参阅[检查 Azure Active Directory 权限](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#check-azure-active-directory-permissions)。

2. 生成的 SSH 公钥和私钥密钥对以登录到 Linux VM Azure 堆栈上。 创建群集时，你将需要公共密钥。

    有关生成密钥的说明，请参阅[SSH 密钥生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

3. 检查在你 Azure 堆栈租户门户中，具有有效的订阅，并且你有足够的公共 IP 地址可用于添加新的应用程序。

## <a name="create-a-service-principal-in-azure-ad"></a>在 Azure AD 中创建服务主体

1. 登录到全局[Azure 门户](http://www.poartal.azure.com)。
2. 您已在使用与 Azure 堆栈相关联的 Azure AD 租户登录的检查。
3. 创建 Azure AD 应用程序。

    a. 选择**Azure Active Directory** > **+ 应用程序注册** > **新应用程序注册**。

    b. 输入**名称**的应用程序。

    c. 选择**Web 应用程序 / API**

    d. 输入`http://localhost`为**登录 URL**。

    c. 单击“创建” 

4. 记下**应用程序 ID**。 创建群集时，你需要具有 ID。 作为引用 ID**服务主体客户端 ID**。

5. 选择**设置** > **密钥**。

    a. 输入**说明**。

    b. 选择**永不过期**为**Expires**。

    c. 选择“保存”。 请记下密钥的字符串。 创建群集时，你将需要密钥的字符串。 该键与引用**服务主体客户端机密**。



## <a name="give-the-service-principal-access"></a>为提供的服务主体访问权限

为服务主体访问提供到你的订阅中，使主体可以创建资源。

1.  登录到[管理门户](https://adminportal.local.azurestack.external)。

2. 选择**更多的服务** > **用户订阅** > **+ 添加**。

3. 选择你创建的订阅。

4. 选择**访问控制 (IAM)** > 选择 **+ 添加**。

5. 选择**所有者**角色。

6. 选择创建你的服务主体的应用程序名称。 你可能需要在搜索框中键入的名称。

7. 单击“ **保存**”。

## <a name="deploy-a-kubernetes-cluster"></a>部署实现 Kubernetes 群集

1. 打开[Azure 堆栈门户](https://portal.local.azurestack.external)。

2. 选择 **+ 新** > **计算** > **Kubernetes 群集**。

    ![部署解决方案模板](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template.png)

3. 选择**参数**在部署解决方案模板。

    ![部署解决方案模板](../media/azure-stack-solution-template-kubernetes-cluster-add/azure-stack-kubernetes-cluster-solution-template-parameters.png)

2. 输入**Linux 管理用户名**。 Kubernetes 群集的一部分的 Linux 虚拟机并 DVM 的用户名称。

3. 输入**SSH 公钥**用于到 DVM Kubernetes 群集的一部分创建的所有 Linux 计算机的授权。

4. 输入**租户终结点**。 这是要连接到创建 Kubernetes 群集的资源组的 Azure 资源管理器终结点。 你将需要从 Azure 堆栈运算符集成的系统中获取终结点。 有关 Azure 堆栈开发工具包 (ASDK)，你可以使用`https://management.local.azurestack.external`。

5. 输入**租户 ID**租户。 如果你需要查找此值的帮助，请参阅[获取租户 ID](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#get-tenant-id)。 

6. 输入**主配置文件的 DNS 前缀**区域中唯一。 这必须是一个区域唯一的名称，如`k8s-12345`。 尝试选择相同作为资源组的名称，作为最佳做法。

    > [!note]  
    > 对于每个群集中，使用新的和独特的主配置文件的 DNS 前缀。

7. 在群集中输入的代理的数量。 此值称为**代理池配置文件计数**。 可以有从 1 到 32

8. 输入**服务主体的应用程序 ID** Kubernetes Azure 云提供程序使用此。

9. 输入**服务主体的客户端机密**时创建服务主体的应用程序创建。

10. 输入**Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序版本。 Azure 堆栈释放用于每个 Azure 堆栈版本的自定义 Kubernetes 版本。

12. 选择“确定”。

### <a name="specify-the-solution-values"></a>指定解决方案值

1. 选择**订阅**。

2. 输入新的资源组的名称或选择现有资源组。 资源名称必须是字母数字和小写。

3. 输入的位置的资源组中，如**本地**。

4. 选择**创建。**

## <a name="connect-to-your-cluster"></a>连接到你的群集

现在你就可以连接到你的群集。 你将需要**kubectl**，Kubernetes 命令行客户端。 你可以找到有关连接到和管理 Azure 容器服务文档中的群集的说明。   

有关说明，请参阅[连接到群集](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough#connect-to-the-cluster)。

## <a name="next-steps"></a>后续步骤

[将实现 Kubernetes 群集添加到应用商店 （适用于 Azure 堆栈运算符）](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)