---
title: Web 应用教程 - 编写 Web 应用程序
description: 本教程将演练部署简单 Web 应用程序的示例。 本教程部分将演练如何编写 Web 应用程序。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87848020"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>编写用于读取 FHIR 数据的 Azure Web 应用程序
在可以连接到 FHIR 服务器并可以发布 (POST) 数据的情况下，便可以编写一个用于读取 FHIR 数据的 Web 应用程序。 本教程的这最后一个步骤将演练如何编写和访问 Web 应用程序。

## <a name="create-web-application"></a>创建 Web 应用程序
在 Azure 中，依次选择“创建资源”、“Web 应用”。   确保将 Web 应用程序命名为在客户端应用程序的重定向 URI 中指定的任何名称，或者返回并使用新名称更新重定向 URI。 

![创建 Web 应用程序](media/tutorial-web-app/create-web-app.png)

在 Web 应用程序可用后，选择“转到资源”。  在右侧的“开发工具”下选择“应用服务编辑器(预览版)”，然后选择“转到”。   选择“转到”会打开应用服务编辑器。 右键单击“浏览”下的灰色空间，并创建名为 index.html 的新文件。  

下面是可以在 index.html 中输入的代码。  需要更新以下各项：
* **clientId** - 使用客户端应用程序 ID 更新它。 此 ID 是检索令牌时提取的同一 ID
* **authority** - 使用 Azure AD 租户 ID 更新它
* **FHIRendpoint** - 将 FHIRendpoint 更新为包含你的 FHIR 服务名称
* **scopes** - 更新以反映受众的完整 URL

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

在此处，可以返回到 Web 应用程序资源，并打开“概述”页上显示的 URL。 登录以查看前面创建的患者 James Tiberious Kirk。

## <a name="next-steps"></a>后续步骤
你已成功部署了 Azure API for FHIR，注册了公共客户端应用程序，测试了访问，并创建了一个较小的 Web 应用程序。 接下来，请查看 Azure API for FHIR 支持的功能。

>[!div class="nextstepaction"]
>[支持的功能](fhir-features-supported.md)





