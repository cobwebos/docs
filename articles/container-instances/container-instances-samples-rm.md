---
title: Azure 资源管理器模板示例 - Azure 容器实例
description: 适用于 Azure 容器实例的 Azure 资源管理器模板示例
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 05/17/2018
ms.author: marsma
ms.openlocfilehash: fcc2e6c52e773d95bcdfe43d881fce036fae6513
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259494"
---
# <a name="azure-resource-manager-templates-for-azure-container-instances"></a>适用于 Azure 容器实例的 Azure 资源管理器模板

下面的示例模板在各种配置中部署容器实例。

有关部署选项，请参阅[部署](#deployment)部分。 如果想要创建自己的模板，Azure 容器实例[资源管理器模板参考][ref]详细介绍了模板格式和可用属性。

## <a name="sample-templates"></a>示例模板

| | |
|-|-|
| **应用程序** ||
| [Wordpress][app-wp] | 在容器实例中创建 WordPress 网站及其 MySQL 数据库。 WordPress 站点内容和 MySQL 数据库会保存到 Azure 文件共享中。 |
| [带有 SQL Server 和 IIS 的 MS NAV][app-nav] | 使用全功能自包含 Dynamics NAV/Dynamics 365 Business Central 环境部署单个 Windows 容器。 |
| **卷** ||
| [emptyDir][vol-emptydir] | 部署两个共享 emptyDir 卷的 Linux 容器。 |
| [gitRepo][vol-gitrepo] | 部署一个克隆 GitHub 存储库的 Linux 容器并将其作为卷装入。 |
| [secret][vol-secret] | 部署装有 PFX 证书的 Linux 容器作为机密卷。 |
| **网络** ||
| [公开 UDP 的容器][net-udp] | 部署公开 UDP 端口的 Windows 或 Linux 容器。 |
| [具有公共 IP 的 Linux 容器][net-publicip] | 部署可通过公共 IP 访问的单个 Linux 容器。 |
| **Azure 资源** ||
| [创建 Azure 存储帐户和文件共享][az-files] | 在容器实例中使用 Azure CLI 创建存储帐户和 Azure 文件共享。

## <a name="deployment"></a>部署

有几个选项可用于使用资源管理器模板部署资源：

[Azure CLI][deploy-cli]

[Azure PowerShell][deploy-powershell]

[Azure 门户][deploy-portal]

[REST API][deploy-rest]

<!-- LINKS - External -->
[app-nav]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-dynamicsnav
[app-wp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress
[az-files]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-storage-file-share
[net-publicip]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip
[net-udp]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-udp
[repo]: https://github.com/Azure/azure-quickstart-templates
[vol-emptydir]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-emptydir
[vol-gitrepo]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-gitrepo
[vol-secret]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-linuxcontainer-volume-secret

<!-- LINKS - Internal -->
[deploy-cli]: ../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-portal]: ../azure-resource-manager/resource-group-template-deploy-portal.md
[deploy-powershell]: ../azure-resource-manager/resource-group-template-deploy.md
[deploy-rest]: ../azure-resource-manager/resource-group-template-deploy-rest.md
[ref]: /azure/templates/microsoft.containerinstance/containergroups