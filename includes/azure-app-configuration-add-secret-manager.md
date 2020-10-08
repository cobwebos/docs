---
ms.openlocfilehash: c3c5b8ef94b507cad433e587c9ebfc2ec16c0ff9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91440408"
---
## <a name="add-secret-manager"></a>添加机密管理器

一个称为“机密管理器”的工具会存储敏感数据，以用于项目树之外的开发工作。 此方法有助于防止意外共享源代码中的应用密码。 完成以下步骤可在 ASP.NET Core 项目中启用机密管理器：

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

导航到项目的根目录，并运行以下命令以在项目中启用机密存储：

```dotnetcli
dotnet user-secrets init
```

包含 GUID 的 `UserSecretsId` 元素将添加到 .csproj 文件中：

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. 打开 *.csproj* 文件。

1. 将 `UserSecretsId` 元素添加到 .csproj 文件中，如下所示。 可以使用相同的 GUID，也可以将此值替换为你自己的值。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. 保存 *.csproj* 文件。

---

> [!TIP]
> 若要详细了解机密管理器，请参阅[在开发 ASP.NET Core 应用时安全存储应用机密](/aspnet/core/security/app-secrets)。
