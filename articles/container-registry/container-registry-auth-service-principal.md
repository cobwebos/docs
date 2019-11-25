---
title: Authenticate with service principal
description: 使用 Azure Active Directory 服务主体允许访问专用容器注册表中的映像。
ms.topic: article
ms.date: 10/04/2019
ms.openlocfilehash: 37da784c8e95a5f5b924532e4a019552924a1a3f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455409"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>使用服务主体的 Azure 容器注册表身份验证

可以使用 Azure Active Directory (Azure AD) 服务主体提供对容器注册表的容器映像 `docker push` 和 `pull` 访问权限。 通过使用服务主体，可以提供对“无外设”服务和应用程序的访问权限。

## <a name="what-is-a-service-principal"></a>什么是服务主体？

Azure AD“服务主体”提供对订阅中的 Azure 资源的访问权限。 You can think of a service principal as a user identity for a service, where "service" is any application, service, or platform that needs to access the resources. 可以为服务主体配置作用域仅限于你指定的那些资源的访问权限。 然后，将应用程序或服务配置为使用服务主体的凭据来访问这些资源。

在 Azure 容器注册表的上下文中，你可以创建对 Azure 中的专用注册表具有拉取、推送和拉取或其他权限的 Azure AD 服务主体。 有关完整列表，请参阅 [Azure 容器注册表的角色和权限](container-registry-roles.md)。

## <a name="why-use-a-service-principal"></a>为何使用服务主体？

通过使用 Azure AD 服务主体，可以针对专用容器注册表提供具有作用域的访问权限。 Create different service principals for each of your applications or services, each with tailored access rights to your registry. 而且，因为你可以避免在各个服务和应用程序之间共享凭据，因此可以仅针对你选择的服务主体（和涉及的应用程序）滚动更新凭据或撤销访问权限。

For example, configure your web application to use a service principal that provides it with image `pull` access only, while your build system uses a service principal that provides it with both `push` and `pull` access. If development of your application changes hands, you can rotate its service principal credentials without affecting the build system.

## <a name="when-to-use-a-service-principal"></a>何时使用服务主体

在**无外设方案**中，应当使用服务主体来提供注册表访问。 即，任何必须以自动或其他无人参与方式来推送或拉取容器映像的应用程序、服务或脚本。 例如：

  * *Pull*: Deploy containers from a registry to orchestration systems including Kubernetes, DC/OS, and Docker Swarm. You can also pull from container registries to related Azure services such as [Azure Kubernetes Service (AKS)](../aks/cluster-container-registry-integration.md), [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/), and others.

  * *Push*: Build container images and push them to a registry using continuous integration and deployment solutions like Azure Pipelines or Jenkins.

For individual access to a registry, such as when you manually pull a container image to your development workstation, we recommend using your own [Azure AD identity](container-registry-authentication.md#individual-login-with-azure-ad) instead for registry access (for example, with [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>示例脚本

You can find the preceding sample scripts for Azure CLI on GitHub, as well as versions for Azure PowerShell:

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Authenticate with the service principal

Once you have a service principal that you've granted access to your container registry, you can configure its credentials for access to "headless" services and applications, or enter them using the `docker login` command. 使用以下值：

* **User name** - service principal application ID (also called *client ID*)
* **Password** - service principal password (also called *client secret*)

Each value is a GUID of the form `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> 可通过运行 [az ad sp reset-credentials](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) 命令重新生成服务主体的密码。
>

### <a name="use-credentials-with-azure-services"></a>Use credentials with Azure services

You can use service principal credentials from any Azure service that authenticates with an Azure container registry.  Use service principal credentials in place of the registry's admin credentials for a variety of scenarios.

For example, use the credentials to pull an image from an Azure container registry to [Azure Container Instances](container-registry-auth-aci.md).

### <a name="use-with-docker-login"></a>Use with docker login

You can run `docker login` using a service principal. In the following example, the service principal application ID is passed in the environment variable `$SP_APP_ID`, and the password in the variable `$SP_PASSWD`. For best practices to manage Docker credentials, see the [docker login](https://docs.docker.com/engine/reference/commandline/login/) command reference.

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Once logged in, Docker caches the credentials.

### <a name="use-with-certificate"></a>Use with certificate

If you've added a certificate to your service principal, you can sign into the Azure CLI with certificate-based authentication, and then use the [az acr login][az-acr-login] command to access a registry. Using a certificate as a secret instead of a password provides additional security when you use the CLI. 

A self-signed certificate can be created when you [create a service principal](/cli/azure/create-an-azure-service-principal-azure-cli). Or, add one or more certificates to an existing service principal. For example, if you use one of the scripts in this article to create or update a service principal with rights to pull or push images from a registry, add a certificate using the [az ad sp credential reset][az-ad-sp-credential-reset] command.

To use the service principal with certificate to [sign into the Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal), the certificate must be in PEM format and include the private key. If your certificate isn't in the required format, use a tool such as `openssl` to convert it. When you run [az login][az-login] to sign into the CLI using the service principal, also provide the service principal's application ID and the Active Directory tenant ID. The following example shows these values as environment variables:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Then, run [az acr login][az-acr-login] to authenticate with the registry:

```azurecli
az acr login --name myregistry
```

The CLI uses the token created when you ran `az login` to authenticate your session with the registry.

## <a name="next-steps"></a>后续步骤

* See the [authentication overview](container-registry-authentication.md) for other scenarios to authenticate with an Azure container registry.

* For an example of using an Azure key vault to store and retrieve service principal credentials for a container registry, see the tutorial to [build and deploy a container image using ACR Tasks](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-login]: /cli/azure/reference-index#az-login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
