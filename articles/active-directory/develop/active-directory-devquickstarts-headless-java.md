---
title: "Azure AD Java 命令行入门 | Microsoft 文档"
description: "如何生成使用户登录以访问 API 的 Java 命令行应用。"
services: active-directory
documentationcenter: java
author: xerners
manager: mbaldwin
editor: 
ms.assetid: 51e1a8f9-6ff0-4643-a350-0ba794e26fd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 01/23/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 4e6d859ee621a730e0d2c1062d3a31e7c053e798
ms.lasthandoff: 03/18/2017


---
# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>通过 Azure AD 使用 Java 命令行应用访问 API
[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

使用 Azure AD，只需编写几行代码，就能简单直接地外包 Web 应用的标识管理，提供单一登录和注销。  在 Java Web Apps 中，你可以使用社区驱动 ADAL4J 的 Microsoft 实现来达到此目的。

  现在，我们将使用 ADAL4J 来执行以下操作：

* 使用 Azure AD 作为标识提供者将用户登录到应用。
* 显示有关用户的一些信息。
* 从应用中注销用户。

为此，你需要：

1. 将一个应用程序注册到 Azure AD
2. 将应用设置为使用 ADAL4J 库。
3. 使用 ADAL4J 库向 Azure AD 发出登录和注销请求。
4. 列显有关用户的数据。

若要开始，请[下载应用框架](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip)或[下载已完成的示例](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\\/archive/complete.zip)。  你还需要一个用于注册应用程序的 Azure AD 租户。  如果还没有此租户，请[了解如何获取租户](active-directory-howto-tenant.md)。

## <a name="1--register-an-application-with-azure-ad"></a>1.将应用程序注册到 Azure AD
若要使应用程序对用户进行身份验证，你首先需要在租户中注册新的应用程序。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在顶栏上单击你的帐户，并在“目录”列表下选择要注册应用程序的 Active Directory 租户。
3. 单击左侧导航栏中的“更多服务”，然后选择“Azure Active Directory”。
4. 单击“应用注册”并选择“添加”。
5. 根据提示创建一个新的 **Web 应用程序和/或 WebAPI**。
  * 应用程序的“名称”向最终用户描述你的应用程序
  * “登录 URL”是应用的基本 URL。  框架的默认值为 `http://localhost:8080/adal4jsample/`。
6. 完成注册后，AAD 将为应用分配唯一的应用程序 ID。  在后面的部分中将会用到此值，因此，请从“应用程序”选项卡中复制此值。
7. 从应用程序的“设置” -> “属性”页中，更新应用 ID URI。 “应用程序 ID URI”是应用程序的唯一标识符。  约定使用 `https://<tenant-domain>/<app-name>`，例如 `http://localhost:8080/adal4jsample/`。

进入门户后，从“设置”页为应用创建“密钥”，并复制该密钥。  稍后您将需要它。

## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2.使用 Maven 将应用设置为使用 ADAL4J 库和必备组件
在这里，我们要将 ADAL4J 配置为使用 OpenID Connect 身份验证协议。  ADAL4J 将用于发出登录和注销请求、管理用户的会话、获取有关用户的信息，等等。

* 在项目的根目录中，打开/创建 `pom.xml`，找到 `// TODO: provide dependencies for Maven` 并替换为以下代码：

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3.创建 Java PublicClient 文件
如上所述，我们将使用图形 API 来获取有关已登录的用户的数据。 为顺利进行，应该创建一个表示“目录对象”的文件以及一个表示“用户”的单独文件，这样就可以使用 Java 的 OO 模式。

* 创建一个名为 `DirectoryObject.java` 的文件，我们将用它来存储有关任何 DirectoryObject 的基本数据（你稍后可以随意使用它来执行任何其他图形查询）。 你可以从下面剪切/粘贴：

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


## <a name="compile-and-run-the-sample"></a>编译并运行示例
切换回到根目录，并运行以下命令来生成你刚刚使用 `maven` 组成的示例。 这会使用你针对依赖项编写的 `pom.xml` 文件。

`$ mvn package`

你的 `/targets` 目录中现在应包含 `adal4jsample.war` 文件。 你可以在 Tomcat 容器中部署该文件并访问 URL 

`http://localhost:8080/adal4jsample/`

> [!NOTE]
> 使用最新的 Tomcat 服务器部署 WAR 非常容易。 只需导航到 `http://localhost:8080/manager/` 并遵循有关上传“adal4jsample.war”文件的说明即可。 它会为你自动部署正确的终结点。
> 
> 

## <a name="next-steps"></a>后续步骤
祝贺你！ 现在，你已创建一个有效的 Java 应用程序，它可以对用户进行身份验证，使用 OAuth 2.0 安全调用 Web API，并获取有关用户的基本信息。  如果你尚未这样做，可以在租户中填充一些用户。

[此处以 .zip 格式提供了](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip)完整示例（不包括配置值）以供参考，也可以从 GitHub 克隆该示例：

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```


