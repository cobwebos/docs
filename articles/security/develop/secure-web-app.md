---
title: 开发安全的 web 应用程序 |Microsoft Docs
description: 这个简单的示例应用程序实现了在 Azure 上进行开发时改进应用程序和组织安全状况的最佳安全方案。
keywords: na
services: security
documentationcenter: na
author: isaiah-msft
manager: barbkess
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security
ms.subservice: security-develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: terrylan
ms.custom: has-adal-ref
ms.openlocfilehash: 690cb37df4a5d195bfce6ee792f7565a6f7f1768
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612769"
---
# <a name="develop-a-secure-web-app"></a>开发安全的 Web 应用

此示例是一个简单的 Python 应用程序，显示一个网页，其中包含用于在 Azure 上开发应用的安全资源的链接。 当你在 Azure 上开发应用程序时，该应用可实现安全最佳做法，有助于改进应用程序和组织的安全状况。

应按照本文中所述的步骤进行操作，以确保正确配置应用程序组件。 数据库、Azure App Service、Azure Key Vault 实例和 Azure 应用程序网关实例彼此依赖。

部署脚本设置基础结构。 运行部署脚本后，需要在 Azure 门户中进行一些手动配置，以将组件和服务链接在一起。

该示例应用面向初学者开发 Azure 上的应用程序，这些应用程序需要在其应用程序中实施安全措施。

在开发和部署此应用中，你将了解如何执行以下操作：

- 创建一个 Azure Key Vault 实例，并从其存储和检索机密。
- 部署 Azure Database for PostgreSQL、设置安全密码并授予对其的访问权限。
- 在适用于 Linux 的 Azure Web 应用上运行 Alpine Linux 容器，并为 Azure 资源启用托管标识。
- 使用[OWASP Top 10 规则集](https://coreruleset.org/)的防火墙创建和配置 Azure 应用程序网关实例。
- 使用 Azure 服务对传输中的数据和静态数据启用加密。

开发和部署此应用后，你将设置以下示例 web 应用以及所述的配置和安全措施。

![示例 web 应用](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>体系结构

应用是具有三个级别的典型 n 层应用程序。 集成的前端、后端和数据库层以及集成的管理组件如下所示：

![应用体系结构](./media/secure-web-app/architecture.png)

该体系结构包括以下组件：

- [Azure 应用程序网关](../../application-gateway/index.yml)。 为应用程序体系结构提供网关和防火墙。
- [Linux 上的 Azure Web 应用](../../app-service/containers/app-service-linux-intro.md)。 提供容器运行时，用于在 Linux 环境中运行 Python 应用。
- [Azure Key Vault](../../key-vault/index.yml)。 存储并加密应用的机密，并管理其周围的访问策略创建。
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql/)。 安全地存储应用的数据。
- [Azure 安全中心](../../security-center/index.yml)和[Azure 应用程序见解](../../azure-monitor/app/app-insights-overview.md)。 提供对应用程序操作的监视和警报。

## <a name="threat-model"></a>威胁模型

威胁建模是识别业务和应用程序的潜在安全威胁的过程，然后确保适当的缓解计划已准备就绪。

此示例使用[Microsoft Threat Modeling Tool](threat-modeling-tool.md)实现安全示例应用的威胁建模。 通过绘制组件和数据流的关系图，你可以在开发过程的早期确定问题和威胁。 这会在以后节省时间和资金。

这是示例应用的威胁模型：

![威胁模型](./media/secure-web-app/threat-model.png)

下面的屏幕截图显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了公开的攻击面，并提示开发人员考虑如何缓解这些问题。

![威胁模型输出](./media/secure-web-app/threat-model-output.png)

例如，通过净化用户输入以及在 Azure Database for PostgreSQL 中使用存储函数，可减轻前面的威胁模型输出中的 SQL 注入。 此缓解措施可防止在数据读取和写入过程中执行查询。

开发人员通过缓解威胁模型输出中的每个威胁，提高系统的整体安全性。

## <a name="deployment"></a>部署

以下选项可让你在 Azure App Service 上运行 Linux：

- 从 Azure 上的预建 Microsoft 容器列表中选择一个容器，这些容器已通过支持技术（Python、Ruby、PHP、Java、node.js、.NET Core）创建。
- 使用自定义生成的容器。 选择自己的容器注册表作为映像源，并构建在支持 HTTP 的许多可用技术之上。

在此示例中，你将运行部署脚本，该脚本将 webapp 部署到应用服务并创建资源。

应用可以使用如下所示的不同部署模型：

![部署数据流关系图](./media/secure-web-app/deployment.png)

有多种方法可在 Azure 上部署应用，包括：

- Azure 资源管理器模板
- PowerShell
- Azure CLI
- Azure 门户
- Azure DevOps

使用此应用程序：

- 用于创建和构建容器映像的[Docker](https://docs.docker.com/) 。
- 用于部署的[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 。
- 作为容器注册表的[Docker 中心](https://hub.docker.com/)。

## <a name="security-considerations"></a>安全注意事项

### <a name="network"></a>网络

示例应用使用端对端 TLS/SSL 加密来传输进出网络的传输中数据。 使用自签名证书配置网关。
> [!IMPORTANT]
> 此演示中使用了自签名证书。 在生产环境中，应从已验证的证书颁发机构（CA）获取证书。

当在网络流量中检测到恶意流量时，应用程序防火墙还会检查传入的流量并向管理员发出警报。
应用程序网关降低了威胁模型中发现的 DDoS 和 SQL 注入威胁的可能性。

### <a name="identity"></a>标识

若要登录到门户，示例应用使用多重身份验证，为分配了资源访问权限的 Azure Active Directory （Azure AD）管理员提供多重身份验证。
该示例应用使用托管标识获取从 Azure Key Vault 读取和检索机密的权限，确保应用不需要硬编码凭据和令牌来读取机密。 Azure AD 会自动创建服务主体，应用程序在使用托管标识时需要读取和修改机密。

Azure 资源和 MFA 的托管标识使攻击者更难获取权限，并在系统中提升其权限。 威胁模型中指出了这种威胁。
应用使用 OAuth，这允许在 OAuth 应用程序中注册的用户登录到应用。

### <a name="storage"></a>存储

Azure Database for PostgreSQL 会自动将 PostgreSQL 数据库中的数据加密。 数据库将授权应用服务 IP 地址，以便只有已部署的应用服务 web 应用可以使用正确的身份验证凭据来访问数据库资源。

### <a name="logging-and-auditing"></a>日志记录和审核

该应用通过使用 Application Insights 跟踪发生的指标、日志和异常来实现日志记录。 此日志记录提供了足够的应用元数据，通知开发人员和运营团队成员应用的状态。 它还提供足够的数据，以便在发生安全事件时进行回溯。

## <a name="cost-considerations"></a>成本注意事项

如果还没有 Azure 帐户，可以创建一个免费帐户。 转到[免费帐户页](https://azure.microsoft.com/free/)开始使用免费的 Azure 帐户，并了解12个月免费购买哪些产品。

若要在包含安全功能的示例应用程序中部署资源，需要为一些高级功能付费。 随着应用规模和 Azure 提供的免费层和试用版的升级，你的成本可能会增加。 使用 Azure[定价计算器](https://azure.microsoft.com/pricing/calculator/)估算成本。

## <a name="deploy-the-solution"></a>部署解决方案

### <a name="prerequisites"></a>先决条件

若要启动并运行应用程序，需要安装以下工具：

- 用于修改和查看应用程序代码的代码编辑器。[Visual Studio Code](https://code.visualstudio.com/)为开源选项。
- [Azure CLI](/cli/azure/install-azure-cli)开发计算机上。
- 系统中的[Git](https://git-scm.com/) 。 Git 用于本地克隆源代码。
- [jq](https://stedolan.github.io/jq/)，一种以用户友好的方式查询 JSON 的 UNIX 工具。

需要一个 Azure 订阅来部署示例应用的资源。 如果没有 Azure 订阅，可以[创建一个免费帐户](https://azure.microsoft.com/free/)来测试示例应用。

安装这些工具后，便可以在 Azure 上部署应用了。

### <a name="environment-setup"></a>环境设置

运行部署脚本以设置环境和订阅：

1. 若要克隆源代码存储库，请使用以下 Git 命令：

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. 若要移动到目录中，请使用此命令：

   ```shell
   cd tutorial-project/scripts
   ```

3. 脚本文件夹中有一些特定于你所使用的平台（Windows 或 Linux）的文件。 由于已安装 Azure CLI，请通过运行以下 Azure CLI 命令在命令提示符处登录到 Azure 帐户：

   ```azurecli-interactive
   az login
   ```

浏览器将打开，并以您的凭据登录。 登录后，你可以从命令提示符开始部署资源。

部署脚本`deploy-powershell.ps1`和`deploy-bash.sh`包含用于部署整个应用程序的代码。
部署解决方案：

1. 如果使用的是 PowerShell，请`deploy-powershell.ps1`通过键入`./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME`将区域和资源组名称替换为合适的 Azure 区域，并使用资源组的名称来运行该文件。
2. 如果在 Linux 上运行，则`deploy-bash.sh`通过键入`/deploy-bash.sh REGION RESOURCE_GROUP_NAME`来运行该文件，可能需要通过键入`chmod +x deploy-bash.sh`

下面的示例展示了关键组件的代码段。 您可以通过运行部署文件，单独或通过其他组件部署这些示例。

### <a name="implementation-guidance"></a>实施指南

部署脚本是一个可以分解为四个阶段的脚本。 每个阶段部署并配置[体系结构关系图](#architecture)中的 Azure 资源。

这四个阶段为：

- 部署 Azure Key Vault。
- 部署 Azure Database for PostgreSQL。
- 在 Linux 上部署 Azure Web 应用。
- 部署具有 web 应用程序防火墙的应用程序网关。

每个阶段都通过使用以前部署的资源中的配置来构建。

若要完成实施步骤，请确保已安装 "[先决条件](#prerequisites)" 下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure Key Vault

在本部分中，将创建和部署用于存储机密和证书的 Azure Key Vault 实例。

完成部署后，会在 Azure 上部署一个 Azure Key Vault 实例。

使用 Azure CLI 部署 Azure Key Vault：

1. 为 Azure Key Vault 声明变量。
2. 注册 Azure Key Vault 提供程序。
3. 为实例创建资源组。
4. 在步骤3中创建的资源组中创建 Azure Key Vault 实例。

   ```powershell-interactive

    function Get-Hash() {
        return (New-Guid).Guid.Split('-')[4]
    }

   az provider register -n Microsoft.KeyVault

   # Create the Azure Key Vault instance
   Write-Host "Creating Azure Key Vault instance: $($kvName)"
   az keyvault create --name $kvName `
       --resource-group $ResourceGroup `
       --location $Location `
       --verbose

   # Generate usernames and passwords using system functions and environment variables
   Write-Host "Generating PostgreSQL username and password"
   $pgUsername = "$($env:Username)$(Get-Hash)"
   $pgPassword = (New-Guid).Guid

   # Set the username secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL username in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGUSERNAME `
       --value $pgUsername `
       --verbose

   # Set the password secret in the Azure Key Vault instance
   Write-Host "Setting PostgreSQL password in KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGPASSWORD `
       --value $pgPassword `
       --verbose

   ```

最佳做法是在使用 Key Vault 的应用程序中使用 Azure 资源的托管标识来访问资源。 如果 Key Vault 的访问密钥未存储在代码或配置中，则安全状况会提高。

#### <a name="deploy-azure-database-for-postgresql"></a>部署 Azure Database for PostgreSQL

Azure Database for PostgreSQL 的工作方式如下所示，首先创建数据库服务器，然后创建用于存储架构和数据的数据库。

完成部署后，你将拥有一个在 Azure 上运行的 PostgreSQL 服务器和数据库。

使用 Azure CLI 部署 Azure Database for PostgreSQL：

1. 使用 Azure CLI 和 Azure 订阅设置打开终端。
2. 生成用于访问数据库的安全用户名和密码组合。 （这些应存储在使用这些应用程序 Azure Key Vault 中。）
3. 创建 PostgreSQL 服务器实例。
4. 在步骤3中创建的服务器实例上创建数据库。
5. 在 PostgreSQL 实例上运行 PostgreSQL 脚本。

以下代码依赖于 Azure KeyVault 中存储的 PGUSERNAME 和 PGPASSWORD 机密，请从上面的部署 KeyVault 步骤开始。

   ```powershell-interactive
   $pgUsername = $(az keyvault secret show --name PGUSERNAME --vault-name $kvName --query value) -replace '"',''
   $pgPassword = $(az keyvault secret show --name PGPASSWORD --vault-name $kvName --query value) -replace '"',''

   # Create an Azure Database for PostgreSQL server
   Write-Host "Creating the PostreSQL server: $($dbServer)"
   az  postgres server create -l $Location `
       --resource-group $ResourceGroup `
       --name $dbServer `
       --admin-user $pgUsername `
       --admin-password $pgPassword `
       --sku-name B_Gen5_1 `
       --verbose

   # Create a database in the server instance created above
   Write-Host "Creating the PostgreSQL database: $($dbName)"
   az postgres db create --resource-group $ResourceGroup `
       --server-name $dbServer `
       --name $dbName `
       --verbose

   # Retrieve the database information above
   $db = (az postgres server show --resource-group $ResourceGroup `
           --name $dbServer)

   $db = $db | ConvertFrom-Json

   # Get the Fully Qualified Domain Name (FQDN) of the database to use in
   # the database connection strings to be stored in Azure Key Vault
   Write-Host "Generating the PostgreSQL connection string"
   $PGCONNECTIONSTRING = "postgresql+psycopg2://${pgUsername}@$($db.fullyQualifiedDomainName):${pgPassword}@$($db.fullyQualifiedDomainName):$dbPort/$($dbName)?sslmode=$($dbSSLMode)"
   $PGCONNECTIONSTRING = $PGCONNECTIONSTRING + '"&"' + "sslrootcert=$($dbRootCertPath)"

   # Set the database connection string above into Azure Key Vault
   Write-Host "Setting the PostgreSQL connection string into KeyVault"
   az keyvault secret set --vault-name $kvName `
       --name PGCONNECTIONSTRING `
       --value $PGCONNECTIONSTRING `
       --verbose
   ```

部署数据库后，需要将其凭据和连接字符串存储在 Azure Key Vault 中。
在 "脚本" 文件夹中，有`functions.sql`一个文件包含用于在运行时创建存储函数的 PL/pgSQL 代码。 运行此文件参数化输入以限制 SQL 注入。

PostgreSQL 与一个用于连接到数据库`psql`的名为的工具捆绑在一起。 若要`functions.sql`运行，需要从本地计算机连接到 Azure Database for PostgreSQL 实例，并从该处运行它。 Psql 工具的安装包括在每个操作系统上的 PostgreSQL 的默认安装中。
有关详细信息，请参阅[Psql 文档](https://www.postgresql.org/docs/9.3/app-psql.html)。

Azure Cloud Shell 还包括此`psql`工具。 您可以通过选择 "Cloud Shell" 图标直接从 Azure 门户使用 Cloud Shell。

若要启用对 PostgreSQL 实例的远程访问，需要在 PostgreSQL 中授权该 IP 地址。
转到 "**连接安全**" 选项卡，选择 "**添加客户端 IP**"，并保存新设置，即可启用此访问。

![授权客户端 IP](./media/secure-web-app/add-client-ip-postgres.png)

如果使用 Cloud Shell 而不是本地 psql 工具，请选择 "**允许访问 Azure 服务**" 并将其值更改为 **"打开"** ，以允许 Cloud Shell 访问。

然后，通过在 Azure 门户上的 PostgreSQL 实例的 "**连接字符串**" 选项卡中运行以下带有连接字符串参数的 psql 命令，连接到该实例。
将空大括号替换为数据库的连接字符串边栏选项卡中的参数，将密码替换为 Azure Key Vault 中的密码。

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

确保已连接到数据库后，运行以下 PL/pgSQL 脚本。 此脚本创建用于将数据插入到数据库中的存储函数。

```shell
CREATE OR REPLACE FUNCTION insert_visitor(country VARCHAR(40), browser VARCHAR(40), operating_system VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO visitor(
        country,
        browser,
        operating_system,
        date_visited)
    VALUES (
        country,
        browser,
        operating_system,
        NOW()
    );
END;
$$ LANGUAGE PLPGSQL;

CREATE OR REPLACE FUNCTION insert_azure_document(title VARCHAR(40), url VARCHAR(100), category VARCHAR(40)) RETURNS void AS $$
BEGIN
    INSERT INTO azure_document(
        title,
        url,
        category)
    VALUES (
        title,
        url,
        category
    );
END;
$$ LANGUAGE PLPGSQL;
```

有关如何为 PostgreSQL 设置 TLS 和证书颁发机构（CA）验证的详细信息，请参阅[在 Azure Database for PostgreSQL 中配置 tls 连接](/azure/postgresql/concepts-ssl-connection-security)。

根证书包含在容器中。 获取证书所采取的步骤如下：

1. 从[证书颁发机构](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下载证书文件。
2. [在计算机上下载并安装 OpenSSL](/azure/postgresql/concepts-ssl-connection-security)。
3. 解码证书文件：

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

若要深入了解如何在 PostgreSQL 中配置 TLS 安全性，请参阅[配置 Tls 连接安全性](/azure/postgresql/concepts-ssl-connection-security)。

#### <a name="deploy-azure-web-apps-on-linux"></a>在 Linux 上部署 Azure Web 应用

你可以在 Azure App Service 上轻松构建 Linux 服务，因为 Azure 为 Python、Ruby、c # 和 Java 等广泛使用的语言提供了一组预构建的容器和映像。 Azure 还支持自定义容器，它几乎可以在 Azure App Service 平台上运行所有编程语言。

正在部署的应用程序是一个简单的 Python 应用程序，可在最新的 Ubuntu Linux 分发上运行。 它将分别连接到在上一部分中为凭据管理和数据存储创建的 Azure Key Vault 和 PostgreSQL 实例。

在应用程序的根文件夹中提供了以下 Docker 文件：

```dockerfile
# Docker file for the basic web app
# Using the latest Alpine Linux

FROM alpine:latest

# Copy requirements to the container
COPY requirements.txt /tmp/requirements.txt

# Install Python and PostgreSQL dependencies
RUN apk update && \
    apk add --update bash gcc libffi-dev musl-dev  postgresql-dev python3 python3-dev && \
    rm -r /usr/lib/python*/ensurepip && \
    pip3 install --upgrade pip setuptools && \
    if [ ! -e /usr/bin/pip ]; then ln -s pip3 /usr/bin/pip ; fi && \
    if [[ ! -e /usr/bin/python ]]; then ln -sf /usr/bin/python3 /usr/bin/python; fi && \
    pip3 install --no-cache-dir -r /tmp/requirements.txt && \
    rm -rf /.wh /root/.cache /var/cache /tmp/requirements.txt

# Change the working directory inside the container to /hello
WORKDIR /hello

# Copy the application code into the container
COPY . /hello

# Set the FLASK_APP environment variable used by flask migrate
ENV FLASK_APP=app.py

# Copy the init script to the container
COPY init.sh /usr/local/bin/

# Make the init script executable
RUN  chmod u+x /usr/local/bin/init.sh

# Expose the container web service endpoint
EXPOSE 8000

# Run the app with a non root user
RUN addgroup -g 1000 -S appgroup && \
    adduser  -u 1000 -S appuser -G appgroup

# Allow the non root user to access the folder
RUN chown -R appuser:appgroup /hello

# Switch to the non root user
USER appuser

# Set the init script as the file to be run during container startups
ENTRYPOINT ["/usr/local/bin/init.sh"]
```

上面的 Dockerfile 用于生成在 Azure 容器注册表上承载的容器`mcr.microsoft.com/samples/basic-linux-app`。

下面的代码：

1. 声明应用服务实例的变量和名称。
2. 创建应用服务计划的资源组。
3. 预配 Linux 容器实例上的 Azure Web 应用。
4. 为 web 应用容器启用日志记录。
5. 在容器的应用设置中设置一些应用配置。

   ```powershell-interactive
   Write-Host "Retrieving the Azure Key Vault URL"
   $kvURI = $(az keyvault show --name $kvName --query properties.vaultUri)

   # Create the App Service plan, using --linux for running containers on Web Apps on Linux
   Write-Host "Creating App Service Plan: $($appName)"
   az appservice plan create --name $appServicePlanName `
       --resource-group $ResourceGroup `
       --location $Location `
       --number-of-workers 1 `
       --sku B1 `
       --is-linux `
       --verbose

   # Create the web app
   Write-Host "Creating Azure Web App for Linux: $($appName)"
   az webapp create --name $appName `
       --resource-group $ResourceGroup `
       --plan $appServicePlanName `
       --deployment-container-image-name $containerName `
       --verbose

   # Assign a system-assigned identity
   # This creates a service principal to be used for managed identities for Azure resources allowing access to Key Vault Secrets without using auth keys/tokens
   Write-Host "Assigning Service Principal Identity to webapp: $($appName)"
   az webapp identity assign --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Configure logging for the Docker container on App Service
   Write-Host "Configuring logging for the web app: $($appName)"
   az webapp log config --name $appName `
       --resource-group $ResourceGroup `
       --application-logging true `
       --detailed-error-messages true `
       --docker-container-logging filesystem `
       --verbose

   # Set app configuration settings to be set in the container's environment variables
   Write-Host "Setting app settings for our web app: $($appName)"
   az webapp config appsettings set --name $appName `
       --resource-group $ResourceGroup `
       --settings WEBSITE_TIME_ZONE=$timezone KEY_VAULT_URI=$kvURI `
       --verbose

   # Stop the web app to enable us to first allow the created managed identities for Azure resources service principals access to the Key Vault instance
   az webapp stop --name $appName `
       --resource-group $ResourceGroup `
       --verbose

   # Get all the outbound IPs the App Service instance might use
   Write-Host "Adding outbound Azure App Service IP's to the PostgreSQL database firewall."
   $outboundIps = (az webapp show --resource-group $ResourceGroup `
       --name $appName `
       --query outboundIpAddresses `
       --output tsv)

   # Loop over all the outbound IP addresses and authorize them in the PostgreSQL firewall
   $outboundIps = $outboundIps.Split(',')
   for($i=0; $i -lt $outboundIps.length; $i++) {
       Write-Output "Adding IP Rule $($outboundIps[$i]) on PostgreSQL for App Service"

       az postgres server firewall-rule create --name "OUTBOUND_IP_RULE$i" `
           --resource-group $ResourceGroup `
           --server-name $dbServer `
           --start-ip-address $outboundIps[$i] `
           --end-ip-address $outboundIps[$i] `
           --verbose
   }
   ```

此脚本将为应用服务实例创建一个已分配的标识，该标识可与 MSI 一起使用以与 Azure Key Vault 进行交互，而不会在代码或配置中对密码进行硬编码。

请访问门户中的 Azure Key Vault 实例，在 "访问策略" 选项卡上授权分配的标识。选择 "**添加新访问策略**"。 在 "**选择主体**" 下，搜索与创建的应用服务实例的名称相似的应用程序名称。
附加到应用程序的服务主体应可见。 选择它并单击 "保存访问策略" 页，如以下屏幕截图所示。

由于应用程序只需检索密钥，因此请在 "机密" 选项中选择 "**获取**" 权限，允许访问，同时减少所授予的特权。

![密钥保管库访问策略](./media/secure-web-app/kv-access-policy.png)

*创建 Key Vault 访问策略*

保存访问策略，然后在 "**访问策略**" 选项卡上保存新更改，以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>部署启用了 web 应用程序防火墙的应用程序网关

在 web 应用中，不建议直接向 internet 上的外部公开服务。
负载均衡和防火墙规则提供了更高的安全性并控制传入流量，并可帮助你对其进行管理。

部署应用程序网关实例：

1. 创建资源组以容纳应用程序网关。
2. 预配要附加到网关的虚拟网络。
3. 为虚拟网络中的网关创建子网。
4. 预配公共 IP 地址。
5. 预配应用程序网关。
6. 在网关上启用 web 应用程序防火墙。

   ```powershell-interactive
   az keyvault certificate create --vault-name $kvName `
       --name $certName `
       --policy `@policy.json `
       --verbose

   az keyvault secret download --file $filePath `
       --encoding base64 `
       --name $certName `
       --vault-name $kvName `
       --verbose

   $pfxFile = Get-PfxData -FilePath $filePath

   $certPassword = Get-Random

   az keyvault secret set --vault-name $kvName `
       --name CERTPASSWORD `
       --value $certPassword `
       --verbose

   $signPassword = ConvertTo-SecureString $certPassword -Force -AsPlainText
   Export-PfxCertificate -PFXData $pfxFile -FilePath $certPath -Password $signPassword
   ```

上述脚本：

1. 在 Azure 上创建新的自签名证书。
2. 下载 base64 编码文件形式的自签名证书。
3. 生成自签名证书的密码。
4. 将证书导出为使用密码进行签名的 PFX 文件。
5. 将证书的密码存储在 Azure Key Vault 中。

本部分部署应用程序网关：

```powershell-interactive
# Create a virtual network required by the gateway
Write-Host "Creating the Azure Virtual Network: $($vnetName)"
az network vnet create --name $vnetName `
    --resource-group $ResourceGroup `
    --location $Location `
    --address-prefix $vnetAddressPrefix `
    --verbose

# Add a subnet to the virtual network
Write-Host "Creating the Subnet: $($gwSubnet)"
az network vnet subnet create --name $gwSubnet `
    --resource-group $ResourceGroup `
    --vnet-name $vnetName `
    --address-prefix $gatewayAddressPrefix `
    --verbose

# Create a public IP address that will be used by clients to access the application gateway
Write-Host "Creating the Public IP Address: $($publicIpName)"
az network public-ip create --resource-group $ResourceGroup `
    --name $publicIpName `
    --verbose

# Create the application gateway
Write-Host "Creating the Application Gateway: $($gwName)"
az network application-gateway create `
    --name $gwName `
    --resource-group $ResourceGroup `
    --location $Location `
    --vnet-name $vnetName `
    --subnet $gwSubnet `
    --public-ip-address $publicIpName `
    --http-settings-cookie-based-affinity Disabled `
    --frontend-port 443 `
    --http-settings-protocol Https `
    --http-settings-port 443 `
    --capacity 2 `
    --sku WAF_Medium `
    --cert-file $certPath `
    --cert-password $certPassword `
    --verbose

# Enable the firewall with OWASP Ruleset 3.0 on the application gateway
Write-Host "Creating the Application Gateway WAF Configuration"
az network application-gateway waf-config set `
    --enabled true `
    --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --firewall-mode Detection `
    --rule-set-version 3.0 `
    --verbose

# Retrieve the name of the HTTP settings that will be updated below
$gwHTTPSettings = $(az network application-gateway http-settings list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwHTTPSettings = $gwHTTPSettings | ConvertFrom-Json
$gwHTTPSettingsName = $gwHTTPSettings.name

# Retrieve the name of the backend address pool that will be updated below
$gwAddressPool = $(az network application-gateway address-pool list --resource-group $ResourceGroup `
    --gateway-name $gwName)

$gwAddressPool = $gwAddressPool | ConvertFrom-Json
$gwAddressPoolName = $gwAddressPool.name

# Update the backend pool with the App Service host name
Write-Host "Updating the Azure Application Gateway backend pool host name: $($appHostName)"
az network application-gateway address-pool update --name $gwAddressPoolName `
    --resource-group $ResourceGroup `
    --gateway-name $gwName `
    --servers $appHostName `
    --verbose

# Create a probe that will check the backend pool's availability
Write-Host "Updating the Azure Application Gateway Probe: $($gwProbe)"
az network application-gateway probe create --gateway-name $gwName `
    --name $gwProbe `
    --resource-group $ResourceGroup `
    --protocol Https `
    --path $gwProbePath `
    --host-name-from-http-settings true `
    --verbose

# Update the app to user https and to pick the host name from the backend settings
Write-Host "Deploying the updated application gateway"
az network application-gateway http-settings update --gateway-name $gwName `
    --resource-group $ResourceGroup `
    --name $gwHTTPSettingsName `
    --connection-draining-timeout 0 `
    --enable-probe true `
    --host-name-from-backend-pool true `
    --probe $gwProbe `
    --protocol Https `
    --port 443 `
    --verbose
```

完成部署后，你将拥有一个启用了 web 应用程序防火墙的应用程序网关。

网关实例对 HTTPS 公开端口443。 此配置可确保只能通过 HTTPS 在端口443上访问我们的应用程序。

最佳安全做法是，阻止未使用的端口并限制攻击面暴露。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>将网络安全组添加到应用服务实例

应用服务实例可以与虚拟网络集成。 此集成允许将它们配置为管理应用的传入和传出流量的网络安全组策略。

1. 若要启用此功能，请在 "Azure 应用服务实例" 边栏选项卡上的 "**设置**" 下选择 "**网络**"。 在右窗格中的 " **VNet 集成**" 下，选择 **"单击此处进行配置**"。

   ![新的虚拟网络集成](./media/secure-web-app/app-vnet-menu.png)

    *新的应用服务虚拟网络集成*

1. 在下一页上，选择 "**添加 VNET （预览版）**"。

1. 在下一菜单中，选择在以开头的部署中创建的虚拟`hello-vnet`网络。 你可以创建一个新的子网，也可以选择一个现有子网。
   在这种情况下，请创建新的子网。 将**地址范围**设置为**10.0.3.0/24** ，并将子网命名**为子网。**

   ![应用服务虚拟网络配置](./media/secure-web-app/app-vnet-config.png)

    *应用服务的虚拟网络配置*

现在，已启用虚拟网络集成，可以将网络安全组添加到应用。

1. 使用 "搜索" 框，搜索 "**网络安全组**"。 在结果中选择 "**网络安全组**"。

    ![搜索网络安全组](./media/secure-web-app/nsg-search-menu.png)

    *搜索网络安全组*

2. 在下一菜单中，选择 "**添加**"。 输入 NSG 的**名称**及其所在的**资源组**。 此 NSG 将应用到应用程序网关的子网。

    ![创建 NSG](./media/secure-web-app/nsg-create-new.png)

    *创建 NSG*

3. 创建 NSG 后，将其选中。 在其边栏选项卡中的 "**设置**" 下，选择 "**入站安全规则**"。 将这些设置配置为允许通过端口443进入应用程序网关的连接。

   ![配置 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *配置 NSG*

4. 在网关 NSG 的出站规则中，添加一个规则，以允许通过创建以服务标记`AppService`为目标的规则对应用服务实例建立出站连接：

   ![为 NSG 添加出站规则](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *为 NSG 添加出站规则*

    添加另一个出站规则，以允许网关将出站规则发送到虚拟网络。

   ![添加另一个出站规则](./media/secure-web-app/nsg-outbound-vnet.png)

    *添加另一个出站规则*

5. 在 NSG 的 "子网" 边栏选项卡中，选择 "**关联**"，选择在部署中创建的虚拟网络，然后选择名为 " **gw-子**网" 的网关子网。 NSG 将应用于子网。

6. 如前面的步骤所示，创建另一个 NSG，这是应用服务实例的时间。 为其命名。 添加端口443的入站规则，就像对应用程序网关 NSG 执行的操作一样。

   如果在应用服务环境实例上部署了应用服务实例（这对于此应用不是这种情况），则可以通过在应用服务 NSG 的入站安全组中打开端口454-455 来添加入站规则，以允许 Azure 服务运行状况探测。 下面是配置：

   ![为 Azure 服务运行状况探测添加规则](./media/secure-web-app/nsg-create-healthprobes.png)

    *为 Azure 服务运行状况探测添加规则（仅应用服务环境）*

7. 在 "出站安全规则" 中，创建新的出站安全规则，以允许应用服务实例与 PostgreSQL 数据库通信。 如下所示进行配置：

   ![允许出站 PostgreSQL 连接的规则](./media/secure-web-app/nsg-outbound-postgresql.png)

   *添加允许出站 PostgreSQL 连接的规则*

若要限制攻击面，请修改应用服务网络设置，仅允许应用程序网关访问应用程序。
为此，请转到 "应用服务网络" 选项卡，选择 " **IP 限制**" 选项卡，并创建允许仅允许应用程序网关 IP 直接访问服务的允许规则。

可以从其 "概述" 页检索网关的 IP 地址。 在 " **Ip 地址 CIDR** " 选项卡上，按以下格式输入 IP `<GATEWAY_IP_ADDRESS>/32`地址：。

![仅允许网关](./media/secure-web-app/app-allow-gw-only.png)

*仅允许网关 IP 访问应用服务*

#### <a name="implement-azure-active-directory-oauth"></a>实现 Azure Active Directory OAuth

在示例 web 应用页面上分发的 Azure 文档是应用中可能需要保护的资源。 你可以使用 Azure Active Directory （Azure AD）通过不同的身份验证流来实现 web、桌面和移动应用的身份验证。
应用使用**与 Microsoft 的登录名**，这允许应用读取已添加到单租户 Azure AD 用户列表的用户的配置文件。

在 Azure 门户中，将应用程序配置为使用所需的凭据：

1. 选择**Azure Active Directory**，或使用搜索框搜索。

2. 选择**新注册**：

   ![创建注册](./media/secure-web-app/ad-auth-create.png)

   *创建 Azure AD 应用注册*

3. 在下一页上，输入应用名称。 在 "**支持的帐户类型**" 下，选择 "**仅限此组织目录中的帐户**"。
    在 "**重定向 URI**" 下，输入应用程序将在其上运行的基本域，以及一个具有令牌终结点的应用程序。 例如*GATEWAY_HASH*： cloudapp.net/token。

   ![配置 Azure AD 应用注册](./media/secure-web-app/ad-auth-type.png)

   *配置 Azure AD 应用注册*

4. 系统会显示一个屏幕，其中显示了已注册的应用及其信息。 需要将此信息添加到 Azure Key Vault 实例中。
   1. 复制应用程序（客户端） ID，并将其作为`CLIENTID`Key Vault 保存。
   2. 复制你在上一步中输入的重定向 URI，并将`REDIRECTURI`其另存为。
   3. 复制 Azure AD 默认目录名称 *，格式为* `TENANT`microsoftonline.com，并将其保存到 Key Vault 中。
   4. 请参阅之前创建的 Azure AD 应用的 "**证书 & 机密**" 选项卡，然后选择 "**新建客户端密钥**"，如以下屏幕截图所示。 设置过期日期，然后复制生成的值，并将其保存到 Key Vault 中`CLIENTSECRET`。

      ![Azure AD 授权机密](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD 授权机密*

   5. 使用任意命令行/联机工具生成安全的随机密钥。 将其保存到 Key Vault `FLASKSECRETKEY`中。 应用程序框架使用此密钥来创建会话。
        若要了解如何生成机密密钥，请参阅[Flask 会话](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)。

5. 配置登录后，需要将用户添加到 Azure AD 链接，以允许他们登录到资源。 若要添加它们，请在 Azure AD 中，选择 "**用户**" 选项卡，选择 "**所有用户**"，然后选择 "**新建用户**" 或 "**新来宾用户**"。 对于测试，可以添加来宾用户并邀请用户进入目录。 或者，如果应用程序正在其上运行的域已经过验证，则可以添加新用户。 在此示例中，只能注册 Azure AD 租户中注册的用户进行访问。 有关多租户登录访问的信息，请参阅文档。

   ![将用户添加到默认域](./media/secure-web-app/ad-auth-add-user.png)

   *将用户添加到默认 Azure Active Directory 域*

将 Azure AD 配置和机密添加到 Key Vault 后，可以使用 Azure OAuth 身份验证在应用中对用户进行身份验证。
在应用程序代码中，这由 Azure Active Directory 身份验证库（ADAL）处理。

在 Key Vault 了机密并且应用程序有权访问机密和数据库后，可通过网关的应用程序 URL （https：\/GATEWAY_HASH/p p.）访问应用程序服务，可以从其边栏选项卡获取该 URL。

如果登录到 Azure AD 时，会收到一条错误消息，指出 "用户未在尝试登录的目录中注册" 中，需要添加该用户。 若要添加用户，请进入 Azure AD 的 "**用户**" 选项卡，通过输入其详细信息，手动添加用户，或通过输入其电子邮件地址作为来宾用户登录到**邀请来宾**边栏选项卡中 Azure AD 来邀请用户。

#### <a name="deploy-application-insights"></a>部署 Application Insights
现在，应用已部署并正常运行，你需要处理应用中发生的错误以及日志记录和跟踪数据收集。
日志记录和跟踪数据收集可以查看应用中发生的审核事件。

Application Insights 是一种服务，可收集用户或系统可能生成的日志。

创建 Application Insights 实例：

1. 使用 Azure 门户中的 "搜索" 框搜索**Application Insights** 。
2. 选择“Application Insights”****。 提供此处所示的详细信息以创建实例。

   ![创建 Application Insights 实例](./media/secure-web-app/app-insights-data.png)

部署完成后，你将拥有一个 Application Insights 实例。

创建 Application Insights 实例后，需要使应用知道检测密钥，使其能够将日志发送到云。 为此，可检索 Application Insights 的密钥，并在 Azure 为 Application Insights 提供的应用程序库中使用该密钥。 最佳做法是将密钥和机密存储在 Azure Key Vault 中，以确保它们的安全。

对于基本示例应用程序，在创建 Application Insights 实例之后，需要使应用知道检测密钥，使其能够将日志发送到云。
在 Key Vault 中，设置`APPINSIGHTSKEY`一个密码，并将其值设置为检测密钥。 这样，应用程序便可以将日志和指标发送到 Application Insights。

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>为 Azure Active Directory 实施多重身份验证

管理员需要确保门户中的订阅帐户受到保护。 由于该订阅管理创建的资源，因此它容易受到攻击。 若要保护订阅，请在订阅的 " **Azure Active Directory** " 选项卡上启用多重身份验证。

Azure AD 根据应用于符合特定条件的用户或用户组的策略进行操作。
Azure 会创建默认策略，指定管理员需要双重身份验证才能登录到门户。
启用此策略后，系统可能会提示你注销并重新登录到 Azure 门户。

若要为管理员登录启用 MFA：

1. 中转到 Azure 门户中的 " **Azure Active Directory** " 选项卡
2. 在 "安全" 类别下，选择 "条件访问"。 将看到以下屏幕：

   ![条件性访问-策略](./media/secure-web-app/ad-mfa-conditional-add.png)

如果无法创建新策略：

1. 中转到 " **MFA** " 选项卡。
2. 选择 "Azure AD Premium**免费试用版**" 链接，订阅免费试用版。

   ![Azure AD Premium 免费试用版](./media/secure-web-app/ad-trial-premium.png)

返回到条件访问屏幕。

1. 选择 "新建策略" 选项卡。
2. 输入策略名称。
3. 选择要为其启用 MFA 的用户或组。
4. 在 "**访问控制**" 下，选择 "**授权**" 选项卡，然后选择 "**需要多重身份验证**" （以及其他设置，如果需要）。

   ![需要进行 MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

您可以通过选择屏幕顶部的复选框来启用该策略，也可以在 "**条件访问**" 选项卡上执行此操作。启用策略后，用户需要通过 MFA 登录到门户。

需要为所有 Azure 管理员提供 MFA 的基准策略。 可以在门户中立即启用它。 启用此策略可能会使当前会话失效，并强制你再次登录。

如果未启用基线策略：

1. 选择 "**需要对管理员进行 MFA**"。
2. 选择 "**立即使用策略**"。

   ![立即选择使用策略](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure Sentinel 监视应用和资源

随着应用程序的增长，很难聚合从资源接收的所有安全信号和指标，并使它们在面向操作的方法中有用。

Azure Sentinel 旨在收集数据、检测可能的威胁类型，并提供安全事件的可见性。
在等待手动干预的同时，Azure Sentinel 可以依赖预编写的行动手册来启动警报和事件管理流程。

该示例应用由 Azure Sentinel 可监视的多个资源组成。
若要设置 Azure Sentinel，首先需要创建一个 Log Analytics 的工作区，用于存储从各种资源收集的所有数据。

若要创建此工作区：

1. 在 Azure 门户的 "搜索" 框中，搜索**Log Analytics**。 选择“Log Analytics 工作区”****。

   ![搜索 Log Analytics 工作区](./media/secure-web-app/sentinel-log-analytics.png)

    *搜索 Log Analytics 工作区*

2. 在下一页上，选择 "**添加**"，然后提供工作区的名称、资源组和位置。
   ![创建 Log Analytics 工作区](./media/secure-web-app/sentinel-log-analytics-create.png)

   *创建 Log Analytics 工作区*

3. 使用 "搜索" 框搜索**Azure Sentinel**。

   ![搜索 Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *搜索 Azure Sentinel*

4. 选择 "**添加**"，然后选择之前创建的 "Log Analytics" 工作区。

   ![添加 Log Analytics 工作区](./media/secure-web-app/sentinel-workspace-add.png)

    *添加 Log Analytics 工作区*

5. 在 " **Azure Sentinel-数据连接器**" 页的 "**配置**" 下，选择 "**数据连接器**"。 你将看到 Azure 服务的数组，你可以链接到 Log Analytics 存储实例，以便在 Azure Sentinel 中进行分析。

   ![Log Analytics 数据连接器](./media/secure-web-app/sentinel-connectors.png)

    *将数据连接器添加到 Azure Sentinel*

   例如，若要连接应用程序网关，请执行以下步骤：

   1. 打开 "Azure 应用程序网关实例" 边栏选项卡。
   2. 在“监视”下，选择“诊断设置”********。
   3. 选择“添加诊断设置”。****

      ![添加应用程序网关诊断](./media/secure-web-app/sentinel-gateway-connector.png)

      *添加应用程序网关诊断*

   4. 在 "**诊断设置**" 页上，选择你创建的 "Log Analytics" 工作区，然后选择要收集并发送到 Azure Sentinel 的所有指标。 选择“保存”  。

        ![Azure Sentinel 连接器设置](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure Sentinel 连接器设置*

  来自资源的指标位于 Azure Sentinel 中，你可以在其中查询和调查这些指标。

   在诊断设置中为 Azure Key Vault、公共 IP 地址、Azure Database for PostgreSQL 和支持帐户中诊断日志的任何服务添加相同的指标。

设置指标之后，Azure Sentinel 包含要分析的数据。

## <a name="evaluate-and-verify"></a>评估和验证

开发和部署体系结构后，需要确保代码和部署的服务符合安全标准。 以下是可以用来验证软件的一些步骤：

- 静态代码分析
- 漏洞扫描
- 查找并修复应用程序依赖项中的漏洞

这些是用于安全开发的最佳实践的基本构建基块。

### <a name="static-code-analysis"></a>静态代码分析

对于示例应用程序，使用静态分析工具进行验证涉及到使用破坏检查和数据流分析等技术查找应用程序代码中的漏洞。 Python 静态分析工具可让你更有把握地确保你的应用程序安全。

**Linting**

PyFlakes 是 Python linting 库，可帮助你从应用中删除死代码和未使用的函数，如下所示：

![PyFlakes](./media/secure-web-app/pyflakes.png)

Linting 提供了提示和可能的更改，从而使代码更简洁，并在运行时容易出错。

**PyLint**

PyLint 为此项目提供了最大的值。 它执行代码标准检查、错误检查和重构提示，以确保在服务器上运行的代码是安全的。 通过使用 PyLint 更新你的代码，你可以消除 bug 并改善 PyLint 级别，如下图所示。

![PyLint 之前](./media/secure-web-app/before-pylint.png)

*PyLint 之前*

修复了 linting 工具发现的一些代码错误之后，就可以更有把握地确保代码不容易出错。 修复这些错误可显著减少将代码部署到生产环境时可能出现的安全风险。

![Pylint 后](./media/secure-web-app/after-pylint.png)

*PyLint 后*

### <a name="vulnerability-scanning"></a>漏洞扫描

[OWASP 的 ZAP](https://www.zaproxy.org/)工具是一种开放源代码 web 应用程序漏洞扫描程序，可用于检查示例应用程序的漏洞。 在示例应用上运行该工具会显示一些可能的错误和攻击媒介。

![ZAP 工具](./media/secure-web-app/zap-tool.png)

*ZAP 工具*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>查找并修复应用程序依赖项中的漏洞

若要查找并修复应用程序依赖项，可以使用[OWASP 的依赖项检查](https://owasp.org/www-project-dependency-check/)。

安全是检查依赖关系的类似应用程序。 可在[GitHub](https://github.com/pyupio/safety)上找到它。 安全扫描在已知漏洞数据库中发现的漏洞。

![安全](./media/secure-web-app/pysafety.png)

*保护*

## <a name="next-steps"></a>后续步骤

以下文章可帮助你设计、开发和部署安全的应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
