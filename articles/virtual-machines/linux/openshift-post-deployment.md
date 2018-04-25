---
title: Azure 上的 OpenShift 部署后任务 | Microsoft Docs
description: 部署 OpenShift 群集之后的附加任务。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: ''
ms.author: haroldw
ms.openlocfilehash: bdfd075b9438ee12e940f3ec4fddebf467c93ca8
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
---
# <a name="post-deployment-tasks"></a>部署后任务

部署 OpenShift 群集后，可以配置附加的项。 本文介绍以下内容：

- 了解如何使用 Azure Active Directory (Azure AD) 配置单一登录
- 如何配置 Log Analytics 来监视 OpenShift
- 如何配置指标和日志记录

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>使用 Azure Active Directory 配置单一登录

若要使用 Azure Active Directory 进行身份验证，需要先创建 Azure AD 应用注册。 此过程包括两个步骤：创建应用注册、配置权限。

### <a name="create-an-app-registration"></a>创建应用注册

这些步骤使用 Azure CLI 创建应用注册，然后使用 GUI（门户）设置权限。 若要创建应用注册，需要提供以下五项信息：

- 显示名称：应用注册名称（例如 OCPAzureAD）
- 主页：OpenShift 控制台 URL（例如 https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- 标识符 URI：OpenShift 控制台 URL（例如 https://masterdns343khhde.westus.cloudapp.azure.com:8443/console)
- 答复 URL：主公用 URL 和应用注册名称（例如 https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD)
- 密码：安全密码（使用强密码）

以下示例使用上述信息创建应用注册：

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

如果命令成功，将显示类似于下面的 JSON 输出：

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD"
  ]
}
```

记下该命令返回的 appId 属性供稍后步骤使用。

在 Azure 门户中：

1.  选择“Azure Active Directory” > “应用注册”。
2.  搜索应用注册（例如 OCPAzureAD）。
3.  在结果中，单击“应用注册”。
4.  在“设置”下，选择“所需的权限”。
5.  在“所需的权限”下，选择“添加”。

  ![应用注册](media/openshift-post-deployment/app-registration.png)

6.  依次单击“步骤 1: 选择 API”、“Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)”。 单击底部的“选择”。

  ![应用注册选择 API](media/openshift-post-deployment/app-registration-select-api.png)

7.  在“步骤 2: 选择权限”中，选择“委托的权限”下的“登录并读取用户配置文件”，并单击“选择”。

  ![应用注册访问权限](media/openshift-post-deployment/app-registration-access.png)

8.  选择“完成”。

### <a name="configure-openshift-for-azure-ad-authentication"></a>为 Azure AD 身份验证配置 OpenShift

若要配置 OpenShift 以将 Azure AD 用作验证提供程序，必须在所有主节点上编辑 /etc/origin/master/master-config.yaml 文件。

使用以下 CLI 命令查找租户 ID：

```azurecli
az account show
```

在 yaml 文件中，找到以下行：

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com:8443/console/
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

使用以下 CLI 命令查找租户 ID：```az account show```

在所有主节点上重启 OpenShift 主机服务：

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**包含多个主节点的 OpenShift 容器平台 (OCP)**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**包含单个主节点的 OpenShift 容器平台**

```bash
sudo systemctl restart atomic-openshift-master
```

在 OpenShift 控制台中，现在可以看到两个身份验证选项：“htpasswd_auth”和“[应用注册]”。

## <a name="monitor-openshift-with-log-analytics"></a>使用 Log Analytics 监视 OpenShift

若要使用 Log Analytics 监视 OpenShift，可以使用以下两个选项之一：VM 主机上安装的 OMS 代理，或 OMS 容器。 本文提供有关部署 OMS 容器的说明。

## <a name="create-an-openshift-project-for-log-analytics-and-set-user-access"></a>为 Log Analytics 创建 OpenShift 项目并设置用户访问权限

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-a-daemon-set-yaml-file"></a>创建守护程序集 yaml 文件

创建名为 ocp-omsagent.yml 的文件：

```yaml
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: oms
spec:
  selector:
    matchLabels:
      name: omsagent
  template:
    metadata:
      labels:
        name: omsagent
        agentVersion: 1.4.0-45
        dockerProviderVersion: 10.0.0-25
    spec:
      nodeSelector:
        zone: default
      serviceAccount: omsagent
      containers:
      - image: "microsoft/oms"
        imagePullPolicy: Always
        name: omsagent
        securityContext:
          privileged: true
        ports:
        - containerPort: 25225
          protocol: TCP
        - containerPort: 25224
          protocol: UDP
        volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        livenessProbe:
          exec:
            command:
              - /bin/bash
              - -c
              - ps -ef | grep omsagent | grep -v "grep"
          initialDelaySeconds: 60
          periodSeconds: 60
      volumes:
      - name: docker-sock
        hostPath:
          path: /var/run/docker.sock
      - name: omsagent-secret
        secret:
         secretName: omsagent-secret
````

## <a name="create-a-secret-yaml-file"></a>创建机密 yaml 文件

若要创建机密 yaml 文件，需要提供两项信息：Log Analytics 工作区 ID 和 Log Analytics 工作区共享密钥。 

示例 ocp-secret.yml 文件如下： 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

请将 wsid_data 替换为 Base64 编码的 Log Analytics 工作区 ID。 然后将 key_data 替换为 Base64 编码的 Log Analytics 工作区共享密钥。

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-the-secret-and-daemon-set"></a>创建机密和守护程序集

部署机密文件：

```bash
oc create -f ocp-secret.yml
```

部署 OMS 代理守护程序集：

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>配置指标和日志记录

OpenShift 容器平台的 Azure 资源管理器模板提供输入参数用于启用指标和日志记录。 OpenShift 容器平台 Marketplace 产品和 OpenShift Origin 资源管理器模板不提供这些参数。

如果使用了 OCP 资源管理器模板并且在安装时未启用指标和日志记录，或使用了 OCP Marketplace 产品，则事后也可以轻松启用这些功能。 如果使用 OpenShift Origin 资源管理器模板，则需要完成一些准备工作。

### <a name="openshift-origin-template-pre-work"></a>OpenShift Origin 模板的准备工作

1. 使用端口 2200 通过 SSH 连接到第一个主节点。

   示例：

   ```bash
   ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
   ```

2. 编辑 /etc/ansible/hosts file 并将以下行添加到标识提供者节的后面 (# Enable HTPasswdPasswordIdentityProvider)：

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

3. 将 $ROUTING 替换为同一 /etc/ansible/hosts 文件中的 openshift_master_default_subdomain 选项所用的字符串。

### <a name="azure-cloud-provider-in-use"></a>使用中的 Azure 云提供程序

在第一个主节点 (Origin) 或守护节点 (OCP) 上，使用部署期间提供的凭据通过 SSH 建立连接。 发出以下命令：

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>未在使用中的 Azure 提供程序

在第一个主节点 (Origin) 或守护节点 (OCP) 上，使用部署期间提供的凭据通过 SSH 建立连接。 发出以下命令：

```bash
ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook $HOME/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>后续步骤

- [OpenShift 容器平台入门](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [OpenShift Origin 入门](https://docs.openshift.org/latest/getting_started/index.html)
