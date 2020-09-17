---
title: 如何创作 Azure 证明策略并对其签名
description: 有关如何创作证明策略并对其签名的说明。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a3afb12ac831d87b03d0bb16d1b7ef553f1bb906
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90006813"
---
# <a name="how-to-author-and-sign-an-attestation-policy"></a>如何创作证明策略并对其签名

证明策略是上传到 Microsoft Azure 证明的文件。 Azure 证明提供了以特定于证明的策略格式上传策略的灵活性。 此外，也可以上传 JSON Web 签名中已编码的策略版本。 策略管理员负责编写证明策略。 在大多数证明场景中，信赖方充当策略管理员。 发出此证明调用的客户端将发送证明证据，服务将分析该证据并将其转换为传入声明（属性集、值）。 然后，服务根据策略中定义的内容处理声明，并返回计算结果。

策略包含确定授权条件、属性和证明令牌内容的规则。 示例策略文件如下所示：

```
version=1.0;
authorizationrules
{
   c:[type="secureBootEnables", issuer=="AttestationService"]=> permit()
};

issuancerules
{
  c:[type="secureBootEnables", issuer=="AttestationService"]=> issue(claim=c)
  c:[type="notSafeMode", issuer=="AttestationService"]=> issue(claim=c)
};
```
 
策略文件有三个段，如上所示：

- **版本**：版本是所遵循语法的版本号。 

    ```
    version=MajorVersion.MinorVersion   
    ```

    目前唯一支持的版本是版本 1.0。

- **authorizationrules**：将首先检查的声明规则集合，用于确定 Azure 证明是否应继续执行 issuancerules。 声明规则按其定义的顺序应用。

- **issuancerules**：将计算的声明规则的集合，用于将其他信息添加到策略中定义的证明结果。 声明规则按其定义的顺序应用（也是可选的）。

有关详细信息，请参阅[声明和声明规则](claim-rule-grammar.md)。
   
## <a name="drafting-the-policy-file"></a>起草策略文件

1. 创建新的文件。
1. 向文件添加版本。
1. 添加 authorizationrules 和 issuancerules 部分 。

  ```
  version=1.0;
  authorizationrules
  {
  =>deny();
  };
  
  issuancerules
  {
  };
  ```

  授权规则包含没有任何条件的 deny() 操作，以确保不处理任何颁发规则。 或者，授权规则也可以包含 permit() 操作，以允许处理颁发规则。
  
4. 向授权规则添加声明规则

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  };
  ```

  如果传入声明集包含与类型、值和颁发者匹配的声明，则 permit() 操作将通知策略引擎处理 issuancerules。
  
5. 将声明规则添加到 issuancerules。

  ```
  version=1.0;
  authorizationrules
  {
  [type=="secureBootEnabled", value==true, issuer=="AttestationService"]=>permit();
  };
  
  issuancerules
  {
  => issue(type="SecurityLevelValue", value=100);
  };
  ```
  
  传出声明集将包含一个声明，内容如下：

  ```
  [type="SecurityLevelValue", value=100, valueType="Integer", issuer="AttestationPolicy"]
  ```

  可以用类似方式制定复杂的策略。 有关详细信息，请参阅[证明策略示例](policy-examples.md)。
  
6. 保存文件。

## <a name="creating-the-policy-file-in-json-web-signature-format"></a>以 JSON Web 签名格式创建策略文件

创建策略文件后，要以 JWS 格式上传策略，请执行以下步骤。

1. 生成带有策略（utf-8 编码）的 JWS RFC 7515 作为有效负载
     - Base64Url 编码策略的有效负载标识符应为“AttestationPolicy”。
     
     示例 JWT：
     ```
     Header: {"alg":"none"}
     Payload: {"AttestationPolicy":" Base64Url (policy)"}
     Signature: {}

     JWS format: eyJhbGciOiJub25lIn0.XXXXXXXXX.
     ```

2. （可选）对策略进行签名。 Azure 证明支持以下算法：
     - **无**：不要对策略有效负载进行签名。
     - **RS256**：支持对策略负载进行签名的算法

3. 上传 JWS 并验证策略。
     - 如果策略文件没有语法错误，则服务将接受该策略文件。
     - 如果策略文件包含语法错误，则服务将拒绝该策略文件。

## <a name="signing-the-policy"></a>对策略进行签名

下面是有关如何执行策略签名操作的示例 Python 脚本

```python
from OpenSSL import crypto
import jwt
import getpass
       
def cert_to_b64(cert):
              cert_pem = crypto.dump_certificate(crypto.FILETYPE_PEM, cert)
              cert_pem_str = cert_pem.decode('utf-8')
              return ''.join(cert_pem_str.split('\n')[1:-2])
       
print("Provide the path to the PKCS12 file:")
pkcs12_path = str(input())
pkcs12_password = getpass.getpass("\nProvide the password for the PKCS12 file:\n")
pkcs12_bin = open(pkcs12_path, "rb").read()
pkcs12 = crypto.load_pkcs12(pkcs12_bin, pkcs12_password.encode('utf8'))
ca_chain = pkcs12.get_ca_certificates()
ca_chain_b64 = []
for chain_cert in ca_chain:
   ca_chain_b64.append(cert_to_b64(chain_cert))
   signing_cert_pkey = crypto.dump_privatekey(crypto.FILETYPE_PEM, pkcs12.get_privatekey())
signing_cert_b64 = cert_to_b64(pkcs12.get_certificate())
ca_chain_b64.insert(0, signing_cert_b64)

print("Provide the path to the policy text file:")
policy_path = str(input())
policy_text = open(policy_path, "r").read()
encoded = jwt.encode({'text': policy_text }, signing_cert_pkey, algorithm='RS256', headers={'x5c' : ca_chain_b64})
print("\nAttestation Policy JWS:")
print(encoded.decode('utf-8'))
```

## <a name="next-steps"></a>后续步骤
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)
- [使用代码示例证明 SGX enclave](https://docs.microsoft.com/samples/browse/?expanded=azure&terms=attestation)
