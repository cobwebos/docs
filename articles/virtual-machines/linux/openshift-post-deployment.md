---
title: "Azure 上的 OpenShift 部署后任务 | Microsoft Docs"
description: "OpenShift 部署后任务"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldw
manager: najoshi
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 
ms.author: haroldw
ms.openlocfilehash: 12e6785358f5f412326418b0c64eeaeabdaa3b5f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2017
---
# <a name="post-deployment-tasks"></a>部署后任务

部署 OpenShift 群集后，可配置一些其他项。 本文介绍以下内容：

- 使用 Azure Active Directory (AAD) 配置单一登录
- 配置 OMS 以监视 OpenShift
- 配置指标和日志记录

## <a name="single-sign-on-using-aad"></a>使用 AAD 的单一登录

要使用 AAD 进行身份验证，必须先创建 Azure AD 应用注册。 此过程包括 2 个步骤 - 创建应用注册，然后配置权限。

### <a name="create-app-registration"></a>创建应用注册

使用 Azure CLI 创建应用注册，然后使用 GUI（门户）设置权限。 要创建应用注册，需提供 5 项信息。

- 显示名称：应用注册名称（示例：OCPAzureAD）
- 主页：OpenShift 控制台 URL（示例：https://masterdns343khhde.westus.cloudapp.azure.com:8443/console）
- 标识符 URI：OpenShift 控制台 URL（示例：https://masterdns343khhde.westus.cloudapp.azure.com:8443/console）
- 回复 URL：主机公共 URL 和应用注册名称（示例：https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/OCPAzureAD）
- 密码：安全密码（使用强密码）

下面的示例使用上述信息创建应用注册。

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com:8443/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com:8443/console --password {Strong Password}
```

如果命令成功，将得到类似下面的 JSON 输出：

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

1.  选择“Azure Active Directory” --> “应用注册”
2.  搜索应用注册（示例：OCPAzureAD）
3.  在结果中，单击“应用注册”
4.  在“设置”边栏选项卡中，选择“所需的权限”
5.  在“所需的权限”边栏选项卡中，单击“添加”

  ![应用注册](media/openshift-post-deployment/app-registration.png)

6.  单击步骤 1：选择 API，单击“Microsoft Azure Active Directory (Microsoft.Azure.ActiveDirectory)”，然后单击底部的“选择”

  ![应用注册选择 API](media/openshift-post-deployment/app-registration-select-api.png)

7.  在步骤 2 中：选择“权限”，在“委派的权限”下选择“登录并读取用户配置文件”，然后单击“选择”

  ![应用注册访问权限](media/openshift-post-deployment/app-registration-access.png)

8.  单击“完成” 

### <a name="configure-openshift-for-azure-ad-authentication"></a>为 Azure AD 身份验证配置 OpenShift

要配置 OpenShift 以将 Azure AD 用作验证提供程序，必须在所有主节点上编辑 /etc/origin/master/master-config.yaml 文件。

可使用以下 CLI 命令找到租户 ID：

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

可使用以下 CLI 命令找到租户 ID：```az account show```

在所有主节点上重启 OpenShift 主机服务

**OpenShift Origin**

```bash
sudo systemctl restart origin-master-api
sudo systemctl restart origin-master-controllers
```

**具有多主机的 OpenShift 容器平台**

```bash
sudo systemctl restart atomic-openshift-master-api
sudo systemctl restart atomic-openshift-master-controllers
```

**具有单个主机的 OpenShift 容器平台**

```bash
sudo systemctl restart atomic-openshift-master
```

在 OpenShift 控制台中，现可看到两个身份验证选项 - htpasswd_auth 和 [应用注册]。

## <a name="monitor-openshift-with-oms"></a>使用 OMS 监视 OpenShift

使用两个选项之一可实现利用 OMS 监视 OpenShift - 在 VM 主机或 OMS 容器上安装 OMS 代理。 本文提供如何部署 OMS 容器的说明。

## <a name="create-an-openshift-project-for-oms-and-set-user-access"></a>为 OMS 创建 OpenShift 项目并设置用户访问权限

```bash
oadm new-project omslogging --node-selector='zone=default'
oc project omslogging
oc create serviceaccount omsagent
oadm policy add-cluster-role-to-user cluster-reader system:serviceaccount:omslogging:omsagent
oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent
```

## <a name="create-daemon-set-yaml-file"></a>创建守护程序集 yaml 文件

创建名为 ocp-omsagent.yml 的文件。

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

## <a name="create-secret-yaml-file"></a>创建机密 yaml 文件

要创建机密 yaml 文件，需提供 2 项信息 - OMS 工作区 ID 和 OMS 工作区共享密钥。 

示例 ocp-secret.yml 文件 

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: omsagent-secret
data:
  WSID: wsid_data
  KEY: key_data
```

用 Base64 编码 OMS 工作区 ID 替换 wsid_data，用 Base64 编码 OMS 工作区共享密钥替换 key_data。

```bash
wsid_data='11111111-abcd-1111-abcd-111111111111'
key_data='My Strong Password'
echo $wsid_data | base64 | tr -d '\n'
echo $key_data | base64 | tr -d '\n'
```

## <a name="create-secret-and-daemon-set"></a>创建机密和守护程序集

部署机密文件

```bash
oc create -f ocp-secret.yml
```

部署 OMS 代理守护程序集

```bash
oc create -f ocp-omsagent.yml
```

## <a name="configure-metrics-and-logging"></a>配置指标和日志记录

OpenShift 容器平台 (OCP) 资源管理器模板提供输入参数以启用指标和日志记录。 OpenShift 容器平台 Marketplace 产品/服务和 OpenShift Origin 资源管理器模板无此功能。

如果使用 OCP 资源管理器模板并且未在安装时启用指标和日志记录或使用 OCP Marketplace 产品/服务，事后也可轻松启用这些功能。 如果使用 OpenShift Origin 资源管理器模板，则需要一些准备工作。

### <a name="openshift-origin-template-pre-work"></a>OpenShift Origin 模板的准备工作

使用端口 2200 通过 SSH 连接到第一个主节点

示例

```bash
ssh -p 2200 clusteradmin@masterdnsixpdkehd3h.eastus.cloudapp.azure.com 
```

编辑 /etc/ansible/hosts file 并将以下行添加到标识提供者部分后 (# Enable HTPasswdPasswordIdentityProvider)

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

将 $ROUTING 替换为用于相同 /etc/ansible/hosts 文件中 openshift_master_default_subdomain 选项的字符串。

### <a name="azure-cloud-provider-in-use"></a>使用中的 Azure 云提供程序

在第一个主节点 (Origin) 或堡垒节点 (OCP) 上，SSH 使用部署期间提供的凭据。 发出以下命令：

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True \
-e openshift_hosted_logging_storage_kind=dynamic
```

### <a name="azure-cloud-provider-not-in-use"></a>未在使用中的 Azure 提供程序

在第一个主节点 (Origin) 或堡垒节点 (OCP) 上，SSH 使用部署期间提供的凭据。 发出以下命令：

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-metrics.yml \
-e openshift_metrics_install_metrics=True 

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/byo/openshift-cluster/openshift-logging.yml \
-e openshift_logging_install_logging=True 
```

## <a name="next-steps"></a>后续步骤

- [OpenShift 容器平台入门](https://docs.openshift.com/container-platform/3.6/getting_started/index.html)
- [OpenShift Origin 入门](https://docs.openshift.org/latest/getting_started/index.html)