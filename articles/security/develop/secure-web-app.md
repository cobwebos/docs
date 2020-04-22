---
title: 开发安全的 Web 应用程序 |微软文档
description: 此简单示例应用实现了安全最佳实践，在 Azure 上开发时可改进应用程序和组织的安全状态。
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
ms.openlocfilehash: 730e478622da8cd90af1c559e4d0c6fd04151cca
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686811"
---
# <a name="develop-a-secure-web-app"></a>开发安全的 Web 应用

此示例是一个简单的 Python 应用，它显示一个网页，其中包含指向在 Azure 上开发应用的安全资源的链接。 该应用程序实现了安全最佳实践，这些最佳实践可帮助改进应用程序和组织在 Azure 上开发应用时的安全状况。

您应该按顺序按照本文中描述的步骤操作，以确保正确配置应用程序组件。 数据库、Azure 应用服务、Azure 密钥保管库实例和 Azure 应用程序网关实例彼此依赖。

部署脚本设置基础结构。 运行部署脚本后，需要在 Azure 门户中执行一些手动配置，以将组件和服务链接在一起。

示例应用面向在 Azure 上开发应用程序，希望在其应用程序中实施安全措施的初学者。

在开发和部署此应用程序时，您将学习如何：

- 创建 Azure 密钥保管库实例，存储和检索其机密。
- 为 PostgreSQL 部署 Azure 数据库，设置安全密码并授权访问它。
- 在 Azure Web 应用上为 Linux 运行阿尔卑斯 Linux 容器，并为 Azure 资源启用托管标识。
- 使用使用[OWASP 前 10 个规则集](https://coreruleset.org/)的防火墙创建和配置 Azure 应用程序网关实例。
- 使用 Azure 服务对传输中和静态的数据进行加密。

开发和部署此应用后，您将设置以下示例 Web 应用以及描述的配置和安全措施。

![示例 Web 应用](./media/secure-web-app/demo-app.png)

## <a name="architecture"></a>体系结构

该应用程序是一个典型的n层应用程序，具有三层。 此处显示了集成了监视和密钥管理组件的前端、后端和数据库层：

![应用体系结构](./media/secure-web-app/architecture.png)

该体系结构由以下组件组成：

- [Azure 应用程序网关](../../application-gateway/index.yml)。 为我们的应用程序体系结构提供网关和防火墙。
- [Linux 上的 Azure Web 应用程序](../../app-service/containers/app-service-linux-intro.md)。 提供在 Linux 环境中运行 Python 应用的容器运行时。
- [Azure 密钥保管库](../../key-vault/index.yml)。 存储和加密应用的机密，并管理围绕其创建访问策略。
- [用于 PostgreSQL](https://azure.microsoft.com/services/postgresql/)的 Azure 数据库 。 安全地存储我们应用的数据。
- [Azure 安全中心和](../../security-center/index.yml) [Azure 应用程序见解](../../azure-monitor/app/app-insights-overview.md)。 提供有关应用程序操作的监视和警报。

## <a name="threat-model"></a>威胁模型

威胁建模是识别业务和应用程序的潜在安全威胁，然后确保制定适当的缓解计划的过程。

此示例使用[Microsoft 威胁建模工具](threat-modeling-tool.md)为安全示例应用实现威胁建模。 通过绘制组件和数据流图，可以在开发过程的早期识别问题和威胁。 这样可以节省时间和金钱。

这是示例应用的威胁模型：

![威胁模型](./media/secure-web-app/threat-model.png)

以下屏幕截图中显示了威胁建模工具生成的一些示例威胁和潜在漏洞。 威胁模型概述了暴露的攻击面，并提示开发人员考虑如何缓解问题。

![威胁模型输出](./media/secure-web-app/threat-model-output.png)

例如，通过清理用户输入和使用 Azure 数据库中的存储函数进行 PostgreSQL 来缓解上述威胁模型输出中的 SQL 注入。 此缓解措施可防止在数据读取和写入期间任意执行查询。

开发人员通过缓解威胁模型输出中的每个威胁来提高系统的整体安全性。

## <a name="deployment"></a>部署

以下选项允许您在 Azure 应用服务上运行 Linux：

- 从 Azure 上已构建的预构建 Microsoft 容器列表中选择一个容器，该容器已使用支持技术（Python、Ruby、PHP、Java、Node.js、.NET Core）创建。
- 使用自定义容器。 选择您自己的容器注册表作为映像的源，并基于支持 HTTP 的许多可用技术进行构建。

在此示例中，您将运行部署脚本，该脚本将 Webapp 部署到应用服务并创建资源。

该应用程序可以使用如下所示的不同部署模型：

![部署数据流图](./media/secure-web-app/deployment.png)

在 Azure 上部署应用的方法有很多种，包括：

- Azure 资源管理器模板
- PowerShell
- Azure CLI
- Azure 门户
- Azure DevOps

此应用程序使用：

- [Docker](https://docs.docker.com/)创建和生成容器映像。
- 用于部署的[Azure CLI。](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Docker 集线器](https://hub.docker.com/)作为容器注册表。

## <a name="security-considerations"></a>安全注意事项

### <a name="network"></a>网络

示例应用对流入和流出网络的传输中数据使用端到端 TLS/SSL 加密。 网关配置了自签名证书。
> [!IMPORTANT]
> 此演示使用自签名证书。 在生产环境中，应从经过验证的证书颁发机构 （CA） 获取证书。

当网络流量检测到恶意流量时，应用程序防火墙还会检查传入流量并提醒管理员。
应用程序网关可降低威胁模型中发现的 DDoS 和 SQL 注入威胁的可能性。

### <a name="identity"></a>标识

要登录到门户，示例应用对已分配对资源的访问权限的 Azure 活动目录 （Azure AD） 管理员使用多重身份验证。
示例应用使用托管标识从 Azure 密钥保管库获取读取和检索机密的权限，确保应用不需要硬编码凭据和令牌即可读取机密。 Azure AD 会自动创建应用在使用托管标识时需要读取和修改机密的服务主体。

Azure 资源和 MFA 的托管标识使对手更难获得特权并升级其在系统中的权限。 威胁模型指出了这种威胁。
该应用程序使用 OAuth，它允许用户在 OAuth 应用程序中注册登录到应用程序。

### <a name="storage"></a>存储

PostgreSQL 数据库中的数据由 PostgreSQL 的 Azure 数据库自动加密静态数据。 数据库授权应用服务 IP 地址，以便只有已部署的应用服务 Web 应用才能使用正确的身份验证凭据访问数据库资源。

### <a name="logging-and-auditing"></a>日志记录和审核

应用通过使用应用程序见解来跟踪发生的指标、日志和异常，从而实现日志记录。 此日志记录提供了足够的应用元数据，可通知开发人员和操作团队成员有关应用的状态。 它还提供了足够的数据，以便在发生安全事件时回溯。

## <a name="cost-considerations"></a>成本注意事项

如果还没有 Azure 帐户，则可以创建一个免费帐户。 转到[免费帐户页面](https://azure.microsoft.com/free/)即可开始，了解可以使用免费 Azure 帐户做什么，并了解哪些产品在 12 个月内是免费的。

要使用安全功能部署示例应用中的资源，您需要为某些高级功能付费。 随着应用的扩展以及 Azure 提供的免费层和试用版需要升级以满足应用程序要求，您的成本可能会增加。 使用 Azure[定价计算器](https://azure.microsoft.com/pricing/calculator/)估计成本。

## <a name="deploy-the-solution"></a>部署解决方案

### <a name="prerequisites"></a>先决条件

要启动和运行应用程序，您需要安装这些工具：

- 用于修改和查看应用程序代码的代码编辑器。[可视化工作室代码](https://code.visualstudio.com/)是一个开源选项。
- [开发计算机上的 Azure CLI。](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)在你的系统上。 Git 用于在本地克隆源代码。
- [jq，](https://stedolan.github.io/jq/)一种以用户友好方式查询JSON的UNIX工具。

您需要 Azure 订阅来部署示例应用的资源。 如果没有 Azure 订阅，则可以[创建一个免费帐户](https://azure.microsoft.com/free/)来测试示例应用。

安装这些工具后，即可在 Azure 上部署应用。

### <a name="environment-setup"></a>环境设置

运行部署脚本以设置环境和订阅：

1. 要克隆源代码存储库，请使用此 Git 命令：

   ```shell
   git clone https://github.com/Azure-Samples/sample-linux-python-app tutorial-project
   ```

2. 要进入目录，请使用以下命令：

   ```shell
   cd tutorial-project/scripts
   ```

3. 脚本文件夹中有特定于您正在使用的平台（Windows 或 Linux）的文件。 安装 Azure CLI 后，请通过运行此 Azure CLI 命令在命令提示符下登录到 Azure 帐户：

   ```azurecli-interactive
   az login
   ```

浏览器将打开，使用您的凭据登录。 登录后，可以从命令提示符开始部署资源。

部署脚本`deploy-powershell.ps1`并`deploy-bash.sh`包含部署整个应用程序的代码。
要部署解决方案：

1. 如果在 PowerShell 上运行`deploy-powershell.ps1`文件，则键入`./deploy-powershell.ps1 REGION RESOURCE_GROUP_NAME`将区域和资源组名称替换为合适的 Azure 区域和资源组的名称
2. 如果您在 Linux 上通过键入`deploy-bash.sh``/deploy-bash.sh REGION RESOURCE_GROUP_NAME`来运行该文件，则可能需要通过键入使文件可执行`chmod +x deploy-bash.sh`

以下示例演示了关键组件的代码段。 您可以通过运行部署文件单独部署示例，也可以与组件的其余部分一起部署这些示例。

### <a name="implementation-guidance"></a>实施指南

部署脚本是一个脚本，可以分解为四个阶段。 每个阶段部署和配置[体系结构关系图](#architecture)中的 Azure 资源。

四个阶段是：

- 部署 Azure 密钥保管库。
- 为 PostgreSQL 部署 Azure 数据库。
- 在 Linux 上部署 Azure Web 应用。
- 使用 Web 应用程序防火墙部署应用程序网关。

每个阶段都使用以前部署的资源中的配置在前一个阶段构建。

要完成实施步骤，请确保已安装"[先决条件](#prerequisites)"下列出的工具。

#### <a name="deploy-azure-key-vault"></a>部署 Azure 密钥保管库

在本节中，创建和部署用于存储机密和证书的 Azure 密钥保管库实例。

完成部署后，在 Azure 上部署了 Azure 密钥保管库实例。

要使用 Azure CLI 部署 Azure 密钥保管库，请使用：

1. 声明 Azure 密钥保管库的变量。
2. 注册 Azure 密钥保管库提供程序。
3. 为实例创建资源组。
4. 在步骤 3 中创建的资源组中创建 Azure 密钥保管库实例。

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

最佳做法是在使用密钥保管库访问资源的应用中对 Azure 资源使用托管标识。 当密钥保管库的访问密钥未存储在代码或配置中时，您的安全状态会提高。

#### <a name="deploy-azure-database-for-postgresql"></a>为 PostgreSQL 部署 Azure 数据库

PostgreSQL 的 Azure 数据库的工作方式如下，首先创建数据库服务器，然后创建用于存储架构和数据的数据库。

完成部署后，在 Azure 上运行 PostgreSQL 服务器和数据库。

要使用 Azure CLI 部署后格雷SQL的 Azure 数据库，请使用：

1. 使用 Azure CLI 和 Azure 订阅设置打开终端。
2. 生成用于访问数据库的安全用户名和密码组合。 （这些应存储在 Azure 密钥保管库中，用于使用它们的应用。
3. 创建 PostgreSQL 服务器实例。
4. 在步骤 3 中创建的服务器实例上创建数据库。
5. 在 PostgreSQL 实例上运行 PostgreSQL 脚本。

以下代码依赖于上面部署 KeyVault 步骤中存储在 Azure KeyVault 中的 PGUSERNAME 和 PGPASSWORD 机密。

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

部署数据库后，需要将其凭据和连接字符串存储在 Azure 密钥保管库中。
在脚本文件夹中，有一个`functions.sql`文件包含 PL/pgSQL 代码，在运行时创建存储的函数。 运行此文件参数化输入以限制 SQL 注入。

PostgreSQL 与一个称为用于`psql`连接到数据库的工具捆绑在一起。 要运行`functions.sql`，需要从本地计算机连接到 PostgreSQL 实例的 Azure 数据库，并从那里运行它。 psql 工具的安装包含在每个操作系统 PostgreSQL 的默认安装中。
有关详细信息，请参阅[psql 文档](https://www.postgresql.org/docs/9.3/app-psql.html)。

Azure 云外壳还包括该工具`psql`。 您可以通过选择云外壳图标直接从 Azure 门户使用云外壳。

要启用对 PostgreSQL 实例的远程访问，您需要在 PostgreSQL 中授权 IP 地址。
通过访问**连接安全**选项卡、选择 **"添加客户端 IP**"以及保存新设置来启用此访问权限。

![授权客户端 IP](./media/secure-web-app/add-client-ip-postgres.png)

如果使用云外壳而不是本地 psql 工具，请选择"**允许访问 Azure 服务**"并将其值更改为**ON**以允许云外壳访问。

然后，通过运行以下 psql 命令与 Azure 门户上的 PostgreSQL 实例**的连接字符串**选项卡的连接字符串参数连接到实例。
将空大括号替换为数据库的连接字符串边栏选项卡中的参数，用 Azure 密钥保管库的密码替换密码。

```shell
psql "host={} port=5432 dbname=hellodb user={} password=PGPASSWORD sslmode=require"
```

在确保已连接到数据库后，请运行以下 PL/pgSQL 脚本。 该脚本创建用于将数据插入数据库的存储函数。

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

有关如何为 PostgreSQL 设置 TLS 和证书颁发机构 （CA） 验证的详细信息，请参阅[为 PostgreSQL 在 Azure 数据库中配置 TLS 连接](/azure/postgresql/concepts-ssl-connection-security)。

容器中包含根证书。 获取证书的步骤包括：

1. 从[证书颁发机构](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)下载证书文件。
2. [在您的计算机上下载并安装 OpenSSL。](/azure/postgresql/concepts-ssl-connection-security)
3. 解码证书文件：

   ```shell
   openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
   ```

阅读有关如何为 PostgreSQL 配置 TLS 安全的更多内容，[在此处配置 TLS 连接安全性](/azure/postgresql/concepts-ssl-connection-security)。

#### <a name="deploy-azure-web-apps-on-linux"></a>在 Linux 上部署 Azure Web 应用

在 Azure 应用服务的基础上轻松构建 Linux 服务，因为 Azure 为广泛使用的语言（如 Python、Ruby、C# 和 Java）提供了一组预构建的容器和映像。 Azure 还支持自定义容器，它允许在 Azure 应用服务平台上运行几乎所有编程语言。

正在部署的应用程序是一个简单的Python应用程序，运行在最新的UbuntuLinux发行版。 它分别连接到在前几节中为凭据管理和数据存储创建的 Azure 密钥保管库和 PostgreSQL 实例。

以下 Docker 文件在应用程序的根文件夹中提供：

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

上面的 Dockerfile 用于生成托管在 Azure 容器注册表上的`mcr.microsoft.com/samples/basic-linux-app`容器。

以下代码：

1. 声明应用服务实例的变量和名称。
2. 为应用服务计划创建资源组。
3. 在 Linux 容器实例上设置 Azure Web 应用。
4. 启用 Web 应用容器的日志记录。
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

此脚本为应用服务实例创建分配的身份，该标识可与 MSI 一起使用，与 Azure 密钥保管库进行交互，而不会在代码或配置中使用硬编码机密。

转到门户中的 Azure 密钥保管库实例，以授权访问策略选项卡上分配的标识。选择 **"添加新访问策略**"。 在**Select 主体**下，搜索与创建的应用服务实例的名称类似的应用程序名称。
附加到应用程序的服务主体应可见。 选择它并保存访问策略页，如下图所示。

由于应用程序只需要检索密钥，因此在机密选项中选择 **"获取**"权限，允许访问，同时减少授予的权限。

![密钥保管库访问策略](./media/secure-web-app/kv-access-policy.png)

*创建密钥保管库访问策略*

保存访问策略，然后在 **"访问策略"** 选项卡上保存新更改以更新策略。

#### <a name="deploy-application-gateway-with-web-application-firewall-enabled"></a>在启用了 Web 应用程序防火墙后部署应用程序网关

在 Web 应用中，不建议您在互联网上直接向外部世界公开服务。
负载平衡和防火墙规则为传入流量提供了更多的安全性和控制，并帮助您对其进行管理。

要部署应用程序网关实例：

1. 创建资源组以容纳应用程序网关。
2. 预配虚拟网络以附加到网关。
3. 为虚拟网络中的网关创建子网。
4. 预配公共 IP 地址。
5. 预配应用程序网关。
6. 在网关上启用 Web 应用程序防火墙。

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

前面的脚本：

1. 在 Azure 上创建新的自签名证书。
2. 将自签名证书下载为 base64 编码文件。
3. 为自签名证书生成密码。
4. 将证书导出为使用密码签名的 PFX 文件。
5. 将证书的密码存储在 Azure 密钥保管库中。

本节部署应用程序网关：

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

完成部署后，您有一个启用了 Web 应用程序防火墙的应用程序网关。

网关实例公开 HTTPS 端口 443。 此配置可确保我们的应用只能通过 HTTPS 在端口 443 上访问。

阻止未使用的端口和限制攻击面暴露是一种安全最佳做法。

#### <a name="add-network-security-groups-to-the-app-service-instance"></a>将网络安全组添加到应用服务实例

应用服务实例可以与虚拟网络集成。 此集成允许使用管理应用传入和传出流量的网络安全组策略配置它们。

1. 要启用此功能，请在 Azure 应用服务实例边栏选项卡上，**在"设置"** 下选择 **"网络**"。 在右侧窗格中，在**VNet 集成**下，选择 **"单击此处配置**"。

   ![新的虚拟网络集成](./media/secure-web-app/app-vnet-menu.png)

    *应用服务的新虚拟网络集成*

1. 在下一页上，选择 **"添加 VNET（预览）"。**

1. 在下一个菜单上，选择在以`hello-vnet`开始的部署中创建的虚拟网络。 您可以创建新子网或选择现有子网。
   在这种情况下，创建一个新的子网。 将**地址范围**设置为**10.0.3.0/24，** 并命名子**网应用子网**。

   ![应用服务虚拟网络配置](./media/secure-web-app/app-vnet-config.png)

    *应用服务的虚拟网络配置*

现在，您已经启用了虚拟网络集成，您可以将网络安全组添加到我们的应用。

1. 使用搜索框，搜索**网络安全组**。 在结果中选择**网络安全组**。

    ![搜索网络安全组](./media/secure-web-app/nsg-search-menu.png)

    *搜索网络安全组*

2. 在下一个菜单上，选择 **"添加**"。 输入应位于的 NSG**和资源组****的名称**。 此 NSG 将应用于应用程序网关的子网。

    ![创建 NSG](./media/secure-web-app/nsg-create-new.png)

    *创建 NSG*

3. 创建 NSG 后，选择它。 在其边栏选项卡中，在 **"设置"** 下，选择 **"入站安全规则**"。 配置这些设置以允许通过端口 443 进入应用程序网关的连接。

   ![配置 NSG](./media/secure-web-app/nsg-gateway-config.png)

   *配置 NSG*

4. 在网关 NSG 的出站规则中，通过创建以服务标记`AppService`为目标的规则，添加允许出站连接到应用服务实例的规则：

   ![添加 NSG 的出站规则](./media/secure-web-app/nsg-outbound-allowappserviceout.png)

   *添加 NSG 的出站规则*

    添加另一个出站规则，允许网关将出站规则发送到虚拟网络。

   ![添加其他出站规则](./media/secure-web-app/nsg-outbound-vnet.png)

    *添加其他出站规则*

5. 在 NSG 的子网边栏选项卡上，选择 **"关联**"，选择在部署中创建的虚拟网络，然后选择名为**gw-子网的**网关子网 。 NSG 应用于子网。

6. 创建另一个 NSG，如前面的步骤中，此时为应用服务实例。 给它一个名字。 添加端口 443 的入站规则，就像对应用程序网关 NSG 所做的那样。

   如果在应用服务环境实例上部署了应用服务实例（此应用不是这种情况，则可以通过打开应用服务 NSG 的入站安全组上的端口 454-455 来添加入站规则以允许 Azure 服务运行状况探测。 配置：

   ![添加 Azure 服务运行状况探测的规则](./media/secure-web-app/nsg-create-healthprobes.png)

    *添加 Azure 服务运行状况探测的规则（仅限应用服务环境）*

7. 在出站安全规则中，创建一个新的出站安全规则，允许应用服务实例与 PostgreSQL 数据库进行通信。 像这样配置它：

   ![允许出站后 SQL 连接的规则](./media/secure-web-app/nsg-outbound-postgresql.png)

   *添加规则以允许出站 PostgreSQL 连接*

要限制攻击面，请修改应用服务网络设置，以仅允许应用程序网关访问应用程序。
通过进入应用服务网络选项卡、选择 **"IP 限制"** 选项卡以及创建只允许应用程序网关的 IP 直接访问服务的允许规则来执行此操作。

可以从网关的概述页检索网关的 IP 地址。 在**IP 地址 CIDR**选项卡上，输入此格式的`<GATEWAY_IP_ADDRESS>/32`IP 地址： 。

![仅允许网关](./media/secure-web-app/app-allow-gw-only.png)

*仅允许网关 IP 访问应用服务*

#### <a name="implement-azure-active-directory-oauth"></a>实现 Azure 活动目录 OAuth

在示例 Web 应用页上分发的 Azure 文档是我们应用中可能需要保护的资源。 可以使用 Azure 活动目录 （Azure AD） 使用不同的身份验证流实现 Web、桌面和移动应用的身份验证。
该应用程序使用**与 Microsoft 登录**，允许应用读取已添加到单租户 Azure AD 用户列表中的用户的配置文件。

在 Azure 门户中，将应用配置为使用所需的凭据：

1. 选择**Azure 活动目录**，或使用搜索框搜索它。

2. 选择**新注册**：

   ![创建注册](./media/secure-web-app/ad-auth-create.png)

   *创建 Azure AD 应用注册*

3. 在下一页上，输入应用名称。 在 **"支持帐户类型**"下，**选择"仅在此组织目录中的帐户**"。
    在**重定向 URI**下，输入应用将运行的基本域加上带有令牌终结点的一个域。 例如 *：GATEWAY_HASH*.cloudapp.net/token。

   ![配置 Azure AD 应用注册](./media/secure-web-app/ad-auth-type.png)

   *配置 Azure AD 应用注册*

4. 将显示显示已注册应用及其信息的屏幕。 您需要将此信息添加到 Azure 密钥保管库实例中。
   1. 复制应用程序（客户端）ID 并将其保存在密钥保管库中为`CLIENTID`。
   2. 复制您在上一步中输入的重定向 URI 并将其另存为`REDIRECTURI`。
   3. 复制 Azure AD 默认目录名称（其格式*名称*.microsoftonline.com，并将其保存在密钥保管库中为`TENANT`。
   4. 转到以前创建的 Azure AD 应用的 **"证书&机密**"选项卡，然后选择 **"新建客户端机密**"，如下图所示。 设置到期日期，然后复制生成的值并将其保存在密钥保管库中为`CLIENTSECRET`。

      ![Azure AD 授权密钥](./media/secure-web-app/ad-auth-secrets.png)

      *Azure AD 授权密钥*

   5. 使用任何命令行/联机工具生成安全的随机密钥。 将其另存到密钥保管`FLASKSECRETKEY`库中。 应用程序框架使用此密钥创建会话。
        要了解如何生成密钥，请参阅["跳蚤会话](http://flask.pocoo.org/docs/1.0/quickstart/#sessions)"。

5. 配置登录后，需要将用户添加到 Azure AD 链接，以允许他们登录到资源。 要添加它们，请转到 Azure AD 中的 **"用户"** 选项卡，选择 **"所有用户**"，然后选择 **"新用户**"或 **"新来宾用户**"。 对于测试，您可以添加来宾用户并邀请用户进入目录。 或者，如果应用正在运行的域已过验证，则可以添加新用户。 在此示例中，只能注册在 Azure AD 租户中注册的用户才能进行访问。 有关多租户登录访问的信息，请参阅文档。

   ![将用户添加到默认域](./media/secure-web-app/ad-auth-add-user.png)

   *将用户添加到默认的 Azure 活动目录域*

将 Azure AD 配置和机密添加到密钥保管库后，可以使用 Azure OAuth 身份验证将用户身份验证到应用中。
在应用代码中，这由 Azure 活动目录身份验证库 （ADAL） 处理。

在密钥保管库中包含机密并应用程序可以访问机密和数据库后，可以通过网关的应用程序 URL （https：\//GATEWAY_HASH.cloudapp.net） 访问应用程序服务，您可以通过其边栏选项卡获取该应用程序服务。

如果在登录到 Azure AD 时，会收到一个错误，指出"用户未在尝试登录的目录中注册"，则需要添加该用户。 要添加用户，请转到 Azure AD 的 **"用户"** 选项卡，并通过输入用户的详细信息手动添加用户，或者通过在 **"邀请来宾"** 边栏选项卡中输入以来宾用户身份的用户电子邮件地址来邀请用户加入 Azure AD。

#### <a name="deploy-application-insights"></a>部署 Application Insights
现在，应用已部署并工作，您需要处理应用中发生的错误以及日志记录和跟踪数据收集。
日志记录和跟踪数据收集提供了对应用中发生的审核事件的视图。

应用程序见解是收集用户或系统可以生成的日志的服务。

要创建应用程序见解实例，

1. 使用 Azure 门户中的搜索框搜索**应用程序见解**。
2. 选择“Application Insights”****。 提供此处显示的详细信息以创建实例。

   ![创建应用程序见解实例](./media/secure-web-app/app-insights-data.png)

部署完成后，您有一个应用程序见解实例。

创建应用程序见解实例后，需要使应用了解允许其向云发送日志的检测密钥。 为此，请检索应用程序见解密钥，并在 Azure 为应用程序见解提供的应用程序库中使用它。 最佳做法是在 Azure 密钥保管库中存储密钥和机密，以确保它们的安全。

对于基本示例应用，在创建应用程序见解实例后，需要使应用了解允许其向云发送日志的检测密钥。
在密钥保管库中，设置`APPINSIGHTSKEY`机密并将其值设置为检测密钥。 这样做允许应用向应用程序见解发送日志和指标。

#### <a name="implement-multi-factor-authentication-for-azure-active-directory"></a>实现 Azure 活动目录的多重身份验证

管理员需要确保门户中的订阅帐户受到保护。 订阅容易受到攻击，因为它管理您创建的资源。 为了保护订阅，请在订阅的**Azure 活动目录**选项卡上启用多重身份验证。

Azure AD 基于应用于符合特定条件的用户或用户组的策略进行操作。
Azure 创建一个默认策略，指定管理员需要双重身份验证才能登录到门户。
启用此策略后，系统可能会提示您注销并重新登录到 Azure 门户。

要为管理员登录启用 MFA：：

1. 转到 Azure 门户中的**Azure 活动目录**选项卡
2. 在安全类别下，选择条件访问。 将看到以下屏幕：

   ![条件访问 - 策略](./media/secure-web-app/ad-mfa-conditional-add.png)

如果无法创建新策略：

1. 转到**MFA**选项卡。
2. 选择 Azure AD 高级**免费试用版**链接以订阅免费试用版。

   ![Azure AD 高级免费试用版](./media/secure-web-app/ad-trial-premium.png)

返回到条件访问屏幕。

1. 选择新的策略选项卡。
2. 输入策略名称。
3. 选择要为其启用 MFA 的用户或组。
4. 在 **"访问"控件**下，选择"**授予**"选项卡，然后选择 **"需要多重身份验证**"（如果需要，则选择其他设置）。

   ![要求 MFA](./media/secure-web-app/ad-mfa-conditional-add.png)

您可以通过选择屏幕顶部的复选框来启用策略，也可以在 **"条件访问**"选项卡上启用策略。启用策略后，用户需要 MFA 才能登录到门户。

有一个基线策略，它要求所有 Azure 管理员使用 MFA。 您可以立即在门户中启用它。 启用此策略可能会使当前会话无效，并迫使您再次登录。

如果未启用基线策略：

1. 为**管理员选择"需要 MFA"。**
2. **选择立即使用策略**。

   ![立即选择使用策略](./media/secure-web-app/ad-mfa-conditional-enable.png)

#### <a name="use-azure-sentinel-to-monitor-apps-and-resources"></a>使用 Azure 哨兵监视应用和资源

随着应用程序的增长，很难聚合从资源接收的所有安全信号和指标，并使它们以面向操作的方式有用。

Azure Sentinel 旨在收集数据、检测可能的威胁类型，并提供安全事件的可见性。
在等待手动干预时，Azure Sentinel 可以依赖预先编写的行动手册来启动警报和事件管理过程。

示例应用由 Azure Sentinel 可以监视的多个资源组成。
要设置 Azure Sentinel，首先需要创建日志分析工作区，该工作区存储从各种资源收集的所有数据。

要创建此工作区，

1. 在 Azure 门户中的搜索框中，搜索**日志分析**。 选择“Log Analytics 工作区”****。

   ![搜索日志分析工作区](./media/secure-web-app/sentinel-log-analytics.png)

    *搜索日志分析工作区*

2. 在下一页上，选择 **"添加**"，然后为工作区提供名称、资源组和位置。
   ![创建 Log Analytics 工作区](./media/secure-web-app/sentinel-log-analytics-create.png)

   *创建日志分析工作区*

3. 使用搜索框搜索 Azure**哨兵**。

   ![搜索 Azure Sentinel](./media/secure-web-app/sentinel-add.png)

    *搜索 Azure 哨兵*

4. 选择 **"添加**"，然后选择之前创建的日志分析工作区。

   ![添加日志分析工作区](./media/secure-web-app/sentinel-workspace-add.png)

    *添加日志分析工作区*

5. 在**Azure 哨兵 - 数据连接器**页上，在**配置**下，选择 **"数据连接器**"。 您将看到一系列 Azure 服务，可以链接到 Azure Sentinel 中的日志分析存储实例进行分析。

   ![日志分析数据连接器](./media/secure-web-app/sentinel-connectors.png)

    *向 Azure 哨兵添加数据连接器*

   例如，要连接应用程序网关，请执行以下步骤：

   1. 打开 Azure 应用程序网关实例边栏选项卡。
   2. 在“监视”下，选择“诊断设置”********。
   3. 选择“添加诊断设置”。****

      ![添加应用程序网关诊断](./media/secure-web-app/sentinel-gateway-connector.png)

      *添加应用程序网关诊断*

   4. 在 **"诊断设置"** 页上，选择您创建的日志分析工作区，然后选择要收集的所有指标并将其发送到 Azure Sentinel。 选择“保存”。 

        ![Azure 哨兵连接器设置](./media/secure-web-app/sentinel-connector-settings.png)

        *Azure 哨兵连接器设置*

  来自资源的指标位于 Azure Sentinel 中，您可以在其中查询和调查它们。

   在 Azure 密钥保管库、公共 IP 地址、PostgreSQL Azure 数据库以及帐户中支持诊断日志的任何服务的诊断设置中添加相同的指标。

设置指标后，Azure Sentinel 具有要分析的数据。

## <a name="evaluate-and-verify"></a>评估和验证

开发和部署体系结构后，需要确保代码和部署的服务符合安全标准。 以下是验证软件的一些步骤：

- 静态代码分析
- 漏洞扫描
- 查找和修复应用程序依赖项中的漏洞

这些是安全开发最佳实践的基本构建基块。

### <a name="static-code-analysis"></a>静态代码分析

对于示例应用，使用静态分析工具进行验证涉及使用污点检查和数据流分析等技术查找应用代码中的漏洞。 Python 静态分析工具让您更有信心你的应用是安全的。

**Linting**

PyFlakes 是 Python 林丁库，可帮助您从应用中删除死代码和未使用的函数，如下所示：

![皮片](./media/secure-web-app/pyflakes.png)

林亭提供了提示和可能的更改，这些提示和可能更改可以使代码更简洁，在运行时不易出错。

**PyLint**

PyLint 为这个项目提供了最大的价值。 它执行代码标准检查、错误检查和重构提示，以确保在服务器上运行的代码是安全的。 通过使用 PyLint 更新代码，您可以消除错误并改进 PyLint 评级，如下图显示。

![皮林特之前](./media/secure-web-app/before-pylint.png)

*皮林特之前*

修复林廷工具发现的某些代码错误后，您更有信心代码不容易出错。 修复错误可显著降低将代码部署到生产环境时可能出现的安全风险。

![皮林特之后](./media/secure-web-app/after-pylint.png)

*皮林特之后*

### <a name="vulnerability-scanning"></a>漏洞扫描

[OWASP 的 ZAP](https://www.zaproxy.org/)工具是一个开源 Web 应用程序漏洞扫描程序，可用于检查示例应用是否存在漏洞。 在示例应用上运行该工具会显示一些可能的错误和攻击媒介。

![ZAP 工具](./media/secure-web-app/zap-tool.png)

*ZAP 工具*

### <a name="find-and-fix-vulnerabilities-in-app-dependencies"></a>查找和修复应用依赖项中的漏洞

要查找和修复应用程序依赖项，可以使用[OWASP 的依赖项检查](https://owasp.org/www-project-dependency-check/)。

安全性是检查依赖项的类似应用程序。 你可以在[GitHub](https://github.com/pyupio/safety)上找到它。 安全扫描在已知漏洞数据库中发现的漏洞。

![安全](./media/secure-web-app/pysafety.png)

*安全*

## <a name="next-steps"></a>后续步骤

以下文章可帮助您设计、开发和部署安全应用程序。

- [设计](secure-design.md)
- [开发](secure-develop.md)
- [部署](secure-deploy.md)
