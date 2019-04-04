---
title: Azure 上的 OpenShift 部署后任务 | Microsoft Docs
description: 部署 OpenShift 群集之后的附加任务。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/02/2019
ms.author: haroldw
ms.openlocfilehash: cf3a3ca1f751ce9eed5ee5c5397c1d9c864a1dd6
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58903669"
---
# <a name="post-deployment-tasks"></a>部署后任务

部署 OpenShift 群集后，可以配置附加的项。 本文介绍：

- 了解如何使用 Azure Active Directory (Azure AD) 配置单一登录
- 如何配置 Azure Monitor 日志以监视 OpenShift
- 如何配置指标和日志记录
- 如何安装 Open Service Broker for Azure (OSBA)

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>使用 Azure Active Directory 配置单一登录

若要使用 Azure Active Directory 进行身份验证，需要先创建 Azure AD 应用注册。 此过程包括两个步骤：创建应用注册、配置权限。

### <a name="create-an-app-registration"></a>创建应用注册

这些步骤使用 Azure CLI 创建应用注册，然后使用 GUI（门户）设置权限。 若要创建应用注册，需要提供以下五项信息：

- 显示名称：应用注册名称 (例如 OCPAzureAD)
- 主页上：OpenShift 控制台 URL （例如， https://masterdns343khhde.westus.cloudapp.azure.com/console)
- 标识符 URI:OpenShift 控制台 URL （例如， https://masterdns343khhde.westus.cloudapp.azure.com/console)
- 回复 URL:主机公共 URL 和应用注册名称 （例如， https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD)
- 密码：安全密码 （使用强密码）

以下示例使用上述信息创建应用注册：

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

如果命令成功，将显示类似于下面的 JSON 输出：

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

记下该命令返回的 appId 属性供稍后步骤使用。

在 Azure 门户中：

1. 选择“Azure Active Directory” > “应用注册”。
2. 搜索应用注册（例如 OCPAzureAD）。
3. 在结果中，单击“应用注册”。
4. 在“设置”下，选择“所需的权限”。
5. 在“所需的权限”下，选择“添加”。

   ![应用注册](media/openshift-post-deployment/app-registration.png)

6. 单击步骤 1:选择 API，并单击**Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)**。 单击底部的“选择”。

   ![应用注册选择 API](media/openshift-post-deployment/app-registration-select-api.png)

7. 在步骤 2:选择权限中，选择**登录并读取用户个人资料**下**委托的权限**，然后单击**选择**。

   ![应用注册访问权限](media/openshift-post-deployment/app-registration-access.png)

8. 选择“完成”。

### <a name="configure-openshift-for-azure-ad-authentication"></a>为 Azure AD 身份验证配置 OpenShift

若要配置 OpenShift 以将 Azure AD 用作验证提供程序，必须在所有主节点上编辑 /etc/origin/master/master-config.yaml 文件。

使用以下 CLI 命令查找租户 ID：

```azurecli
az account show
```

在 yaml 文件中，找到以下行：

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

紧接在上述行的后面插入以下行：

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

确保 identityProviders 下的文本正确对齐。 使用以下 CLI 命令查找租户 ID： ```az account show```

在所有主节点上重启 OpenShift 主机服务：

**具有多主机的 OpenShift 容器平台 (OCP)**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**具有单个主机的 OpenShift 容器平台**

```bash
sudo systemctl restart atomic-openshift-master
```

**具有多主机 OKD**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**具有单个主机 OKD**

```bash
sudo systemctl restart origin-master
```

在 OpenShift 控制台中，现在可以看到两个身份验证选项：“htpasswd_auth”和“[应用注册]”。

## <a name="monitor-openshift-with-azure-monitor-logs"></a>使用 Azure Monitor 日志监视 OpenShift

可通过三种方法将 Log Analytics 代理添加到 OpenShift。
- 在每个 OpenShift 节点上直接安装适用于 Linux 的 Log Analytics 代理
- 每个 OpenShift 节点上启用 Azure 监视器 VM 扩展
- 安装 Log Analytics 代理作为 OpenShift daemon-set

https://docs.microsoft.com/azure/log-analytics/log-analytics-containers#configure-a-log-analytics-agent-for-red-hat-openshift 中提供了完整说明。

## <a name="configure-metrics-and-logging"></a>配置指标和日志记录

根据分支，适用于 OpenShift 容器平台和 OKD 的 Azure 资源管理器模板可以提供输入参数，用于在安装过程中启用指标和日志记录。

OpenShift 容器平台市场套餐还提供一个选项用于在安装群集期间启用指标和日志记录。

如果在安装群集期间未启用指标/日志记录，事后可以轻松启用。

### <a name="ansible-inventory-pre-work"></a>Ansible 库存准备工作

验证 Ansible 库存文件 (/etc/ansible/hosts) 是否包含指标/日志记录的相应变量。 可以根据所用的模板，在不同的主机上找到库存文件。

对于 OpenShift 容器模板和市场套餐，库存文件位于守护主机上。 对于 OKD 模板，库存文件位于 master-0 主机或守护主机上，具体取决于所用的分支。

1. 编辑 /etc/ansible/hosts file 并将以下行添加到标识提供者节的后面 (# Enable HTPasswdPasswordIdentityProvider)。 如果这些行已存在，请不要再次添加。

   OpenShift/OKD 3.9 和更低版本

   ```yaml
   # Setup metrics
   openshift_hosted_metrics_deploy=false
   openshift_metrics_cassandra_storage_type=dynamic
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"type":"infra"}
   openshift_metrics_cassandra_nodeselector={"type":"infra"}
   openshift_metrics_heapster_nodeselector={"type":"infra"}
   openshift_hosted_metrics_public_url=https://metrics.$ROUTING/hawkular/metrics

   # Setup logging
   openshift_hosted_logging_deploy=false
   openshift_hosted_logging_storage_kind=dynamic
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"type":"infra"}
   openshift_logging_kibana_nodeselector={"type":"infra"}
   openshift_logging_curator_nodeselector={"type":"infra"}
   openshift_master_logging_public_url=https://kibana.$ROUTING
   ```

   OpenShift/OKD 3.10 和更高版本

   ```yaml
   # Setup metrics
   openshift_metrics_install_metrics=false
   openshift_metrics_start_cluster=true
   openshift_metrics_hawkular_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_cassandra_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_metrics_heapster_nodeselector={"node-role.kubernetes.io/infra":"true"}

   # Setup logging
   openshift_logging_install_logging=false
   openshift_logging_fluentd_nodeselector={"logging":"true"}
   openshift_logging_es_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_kibana_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_curator_nodeselector={"node-role.kubernetes.io/infra":"true"}
   openshift_logging_master_public_url=https://kibana.$ROUTING
   ```

3. 将 $ROUTING 替换为同一 /etc/ansible/hosts 文件中的 openshift_master_default_subdomain 选项所用的字符串。

### <a name="azure-cloud-provider-in-use"></a>使用中的 Azure 云提供程序

使用部署期间提供的凭据，通过 SSH 连接到守护节点或第一个主节点（取决于所用的模板和分支）。 发出以下命令：

**OpenShift 容器平台 3.7 及更早版本**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OpenShift 容器平台 3.9 和更高版本**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

**OKD 3.7 及更早版本**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

**OKD 3.9 和更高版本**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>未在使用中的 Azure 提供程序

使用部署期间提供的凭据，通过 SSH 连接到守护节点或第一个主节点（取决于所用的模板和分支）。 发出以下命令：


**OpenShift 容器平台 3.7 及更早版本**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OpenShift 容器平台 3.9 和更高版本**

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.7 及更早版本**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True
```

**OKD 3.9 和更高版本**

```bash
ansible-playbook ~/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True
ansible-playbook ~/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>安装 Open Service Broker for Azure (OSBA)

打开 Service Broker for Azure (OSBA)，以便直接从 OpenShift 预配 Azure 云服务。 OSBA 是适用于 Azure 的 Open Service Broker API 实现。 Open Service Broker API 是为云提供程序定义通用语言的一种规范。云本机应用程序可以使用这些云提供程序来管理云服务，而不会发生锁定。

若要在 OpenShift 中安装 OSBA，请遵照 https://github.com/Azure/open-service-broker-azure#openshift-project-template 中的说明。 

## <a name="next-steps"></a>后续步骤

- [OpenShift 容器平台入门](https://docs.openshift.com/container-platform)
- [开始使用 OKD](https://docs.okd.io/latest)
