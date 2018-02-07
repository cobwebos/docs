## <a name="create-a-service-principal"></a>创建服务主体

若要创建可以访问容器注册表的服务主体，可以使用以下脚本。 使用容器注册表的名称更新 `ACR_NAME` 变量，并可选择性地更新 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令中的 `--role` 值以授予不同权限。 必须已安装 [Azure CLI](/cli/azure/install-azure-cli) 才能使用此脚本。

运行脚本后，请记下服务主体的 **ID** 和**密码**。 获得其凭据后，可以配置应用程序和服务使其作为服务主体对容器注册表进行身份验证。

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_NAME can be any unique name within your
# subscription (you can use the default below).
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate some values required for subsequent command args
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create the service principal with rights scoped to the registry.
# Default permissions are for both docker push and pull access. Modify the
# '--role' argument value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
SP_PASSWD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME --scopes $ACR_REGISTRY_ID --role reader --query password --output tsv)
SP_APP_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output the service principal's credentials; use these in your services and
# applications to authenticate to the container registry.
echo "Service principal ID: $SP_APP_ID"
echo "Service principal password: $SP_PASSWD"
```

## <a name="use-an-existing-service-principal"></a>使用现有的服务主体

若要向现有服务主体授予注册表访问权限，必须为服务主体分配新角色。 与创建新的服务主体一样，可以授予“拉取”、“推送和拉取”以及“所有者”访问权限。

以下脚本使用 [az role assignment create][az-role-assignment-create] 命令向 `SERVICE_PRINCIPAL_ID` 变量中指定的服务主体授予“拉取”权限。 如果要授予不同的访问级别，请调整 `--role` 值。

```bash
#!/bin/bash

# Modify for your environment. The ACR_NAME is the name of your Azure Container
# Registry, and the SERVICE_PRINCIPAL_ID is the service principal's 'appId' or
# one of its 'servicePrincipalNames' values.
ACR_NAME=myregistryname
SERVICE_PRINCIPAL_ID=<service-principal-ID>

# Populate value required for subsequent command args
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Assign the desired role to the service principal. Modify the '--role' argument
# value as desired:
# reader:      pull only
# contributor: push and pull
# owner:       push, pull, and assign roles
az role assignment create --assignee $SERVICE_PRINCIPAL_ID --scope $ACR_REGISTRY_ID --role reader
```

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create