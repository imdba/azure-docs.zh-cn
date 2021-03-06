---
title: "Azure AD v2.0 OAuth 授权代码流 | Microsoft 文档"
description: "使用 Azure AD 的 OAuth 2.0 身份验证协议实现构建 Web 应用程序。"
services: active-directory
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: ae1d7d86-7098-468c-aa32-20df0a10ee3d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 1cffe40c14b931485cc5cec48a95e02ae770764e
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2017
---
# <a name="v20-protocols---oauth-20-authorization-code-flow"></a>v2.0 协议 — OAuth 2.0 授权代码流
OAuth 2.0 授权代码授予可用于设备上所安装的应用中，以访问受保护的资源，例如 Web API。  使用应用模型 v2.0 的 OAuth 2.0 实现，可以将登录名及 API 访问添加到移动应用和桌面应用。  本指南与语言无关，介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

> [!NOTE]
> v2.0 终结点并不支持所有 Azure Active Directory 方案和功能。  若要确定是否应使用 v2.0 终结点，请阅读 [v2.0 限制](active-directory-v2-limitations.md)。
> 
> 

有关 OAuth 2.0 授权代码流的说明，请参阅 [OAuth 2.0 规范 4.1 节](http://tools.ietf.org/html/rfc6749)。  它用于在大部分的应用类型（包括 [Web 应用](active-directory-v2-flows.md#web-apps)和[本地安装应用](active-directory-v2-flows.md#mobile-and-native-apps)）中执行身份验证与授权。  它可让应用程序安全地获取 access_tokens，用于访问以 v2.0 终结点保护的资源。  

## <a name="protocol-diagram"></a>协议图
从较高层面讲，本机/移动应用程序的整个身份验证流有点类似于：

![OAuth 授权代码流](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="request-an-authorization-code"></a>请求授权代码
授权代码流始于客户端将用户定向到的 `/authorize` 终结点。  在这项请求中，客户端指示必须向用户获取的权限：

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&state=12345
```

> [!TIP]
> 单击下面的链接以执行此请求！ 登录之后，浏览器应重定向至地址栏中具有 `code` 的 `https://localhost/myapp/`。
> <a href="https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345" target="_blank">https://login.microsoftonline.com/common/oauth2/v2.0/authorize...</a>
> 
> 

| 参数 |  | 说明 |
| --- | --- | --- |
| tenant |必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。  允许的值为 `common`、`organizations`、`consumers` 和租户标识符。  有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| client_id |必填 |注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 分配给应用的应用程序 ID。 |
| response_type |必填 |必须包括授权代码流的 `code`。 |
| redirect_uri |建议 |应用程序的 redirect_uri，应用程序可在此发送及接收身份验证响应。  其必须完全符合在门户中注册的其中一个 redirect_uris，否则必须是编码的 url。  对于本机和移动应用，应使用默认值 `https://login.microsoftonline.com/common/oauth2/nativeclient`。 |
| 作用域 |必填 |希望用户同意的[范围](active-directory-v2-scopes.md)的空格分隔列表。 |
| response_mode |建议 |指定将生成的令牌送回到应用程序所应该使用的方法。  可以是 `query` 或 `form_post`。 |
| state |建议 |同样随令牌响应返回的请求中所包含的值。  它可以是你想要的任何内容的字符串。  随机生成的唯一值通常用于[防止跨站点请求伪造攻击](http://tools.ietf.org/html/rfc6749#section-10.12)。  该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或视图。 |
| prompt |可选 |表示需要的用户交互类型。  此时唯一有效的值为“login”、“none”和“consent”。  `prompt=login` 将强制用户在该请求上输入其凭据，从而使单一登录无效。  `prompt=none` 完全相反，它将确保无论如何都不会向用户显示任何交互提示。  如果请求无法通过单一登录静默完成，该 v2.0 终结点将返回一个错误。  `prompt=consent` 在用户登录后将触发 OAuth 同意对话框，要求用户向应用授予权限。 |
| login_hint |可选 |如果事先知道其用户名称，可用于预先填充用户登录页面的用户名称/电子邮件地址字段。  通常，应用会在重新身份验证期间使用此参数，并且已经使用 `preferred_username` 声明从前次登录提取用户名。 |
| domain_hint |可选 |可以是 `consumers` 或 `organizations` 之一。  如果包含，它跳过用户在 v2.0 登录页面上经历的基于电子邮件的发现过程，导致稍微更加流畅的用户体验。  通常，应用会在重新身份验证期间使用此参数，方法是从前次登录提取 `tid`。  如果 `tid` 声明值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，应该使用 `domain_hint=consumers`。  否则使用 `domain_hint=organizations`。 |

此时，请求用户输入其凭据并完成身份验证。  v2.0 终结点也将确保用户已经同意 `scope` 查询参数中指示的权限。  如果用户未曾同意这些权限的任何一项，就请求用户同意请求的权限。  [此处提供了权限、许可与多租户应用](active-directory-v2-scopes.md)的详细信息。

用户经过身份验证并同意后，v2.0 终结点将使用 `response_mode` 参数中指定的方法，将响应返回到位于所指示的 `redirect_uri` 的应用。

#### <a name="successful-response"></a>成功的响应
使用 `response_mode=query` 的成功响应如下所示：

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 参数 | 说明 |
| --- | --- |
| 代码 |应用程序请求的 authorization_code。 应用程序可以使用授权代码请求目标资源的访问令牌。  Authorization_codes 的生存期很短，通常约 10 分钟后即过期。 |
| state |如果请求中包含状态参数，响应中就应该出现相同的值。 应用程序应该验证请求和响应中的状态值是否完全相同。 |

#### <a name="error-response"></a>错误响应
错误响应可能也发送到 `redirect_uri`，让应用可以适当地处理：

```
GET https://login.microsoftonline.com/common/oauth2/nativeclient?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>授权终结点错误的错误代码
下表描述了可在错误响应的 `error` 参数中返回的各个错误代码。

| 错误代码 | 说明 | 客户端操作 |
| --- | --- | --- |
| invalid_request |协议错误，例如，缺少必需的参数。 |修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| unauthorized_client |不允许客户端应用程序请求授权代码。 |客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| access_denied |资源所有者拒绝了许可 |客户端应用程序可以通知用户除非用户许可，否则无法继续。 |
| unsupported_response_type |授权服务器不支持请求中的响应类型。 |修复并重新提交请求。 这通常是在初始测试期间捕获的开发错误。 |
| server_error |服务器遇到意外的错误。 |重试请求。 这些错误可能是临时状态导致的。 客户端应用程序可以向用户说明，其响应由于临时错误而延迟。 |
| temporarily_unavailable |服务器暂时繁忙，无法处理请求。 |重试请求。 客户端应用程序可以向用户说明，其响应由于临时状态而延迟。 |
| invalid_resource |目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 |这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |

## <a name="request-an-access-token"></a>请求访问令牌
已获取 authorization_code 并获得用户授权，现在可以将 `POST` 请求发送到 `/token` 终结点，兑换 `code` 以获取所需资源的 `access_token`：

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [!TIP]
> 尝试在 Postman 中执行此请求！ （别忘了替换 `code`）[![在 Postman 中运行](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| 参数 |  | 说明 |
| --- | --- | --- |
| tenant |必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。  允许的值为 `common`、`organizations`、`consumers` 和租户标识符。  有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| client_id |必填 |注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 分配给应用的应用程序 ID。 |
| grant_type |必填 |必须是授权代码流的 `authorization_code`。 |
| 作用域 |必填 |范围的空格分隔列表。  在此阶段中请求的范围必须相当于或为第一个阶段中所请求的范围子集。  如果这个请求中指定的范围遍及多个资源服务器，v2.0 终结点将返回第一个范围内所指定资源的令牌。  有关范围的更加详细的说明，请参阅[权限、许可和范围](active-directory-v2-scopes.md)。 |
| 代码 |必填 |在流的第一个阶段中获取的 authorization_code。 |
| redirect_uri |必填 |用于获取 authorization_code 的相同 redirect_uri 值。 |
| client_secret |Web 应用所需 |在应用程序注册门户中为应用程序创建的应用程序机密。  其不应用于本机应用程序，因为设备无法可靠地存储 client_secrets。  Web 应用和 Web API 都需要应用程序机密，能够将 client_secret 安全地存储在服务器端。 |

#### <a name="successful-response"></a>成功的响应
成功的令牌响应如下：

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 参数 | 说明 |
| --- | --- |
| access_token |请求的访问令牌。 应用可以使用此令牌验证受保护的资源，例如 Web API。 |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer |
| expires_in |访问令牌的有效期（以秒为单位）。 |
| 作用域 |access_token 有效的范围。 |
| refresh_token |OAuth 2.0 刷新令牌。 应用程序可以使用此令牌，在当前的访问令牌过期之后获取其他访问令牌。  Refresh_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。  有关更多详细信息，请参阅 [v2.0 令牌参考](active-directory-v2-tokens.md)。 <br> **注意：**仅当已请求 `offline_access` 作用域时提供。 |
| id_token |无符号 JSON Web 令牌 (JWT)。 应用程序可以 base64Url 解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值获取任何授权或安全边界。  有关 id_token 的详细信息，请参阅 [v2.0 终结点令牌参考](active-directory-v2-tokens.md)。 <br> **注意：**仅当已请求 `openid` 作用域时提供。 |
#### <a name="error-response"></a>错误响应
错误响应如下所示：

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| error_codes |帮助诊断的 STS 特定错误代码列表。 |
| timestamp |发生错误的时间。 |
| trace_id |帮助诊断的请求唯一标识符。 |
| correlation_id |帮助跨组件诊断的请求唯一标识符。 |

#### <a name="error-codes-for-token-endpoint-errors"></a>令牌终结点错误的错误代码
| 错误代码 | 说明 | 客户端操作 |
| --- | --- | --- |
| invalid_request |协议错误，例如，缺少必需的参数。 |修复并重新提交请求。 |
| invalid_grant |授权代码无效或已过期。 |请尝试对 `/authorize` 终结点发出新请求 |
| unauthorized_client |经过身份验证的客户端无权使用此权限授予类型。 |客户端应用程序未注册到 Azure AD 中或者未添加到用户的 Azure AD 租户时，通常会出现这种情况。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| invalid_client |客户端身份验证失败。 |客户端凭据无效。 若要修复，应用程序管理员应更新凭据。 |
| unsupported_grant_type |授权服务器不支持权限授予类型。 |更改请求中的授权类型。 这种类型的错误应该只在开发过程中发生，并且应该在初始测试过程中检测到。 |
| invalid_resource |目标资源无效，原因是它不存在，Azure AD 找不到它，或者未正确配置。 |这表示未在租户中配置该资源（如果存在）。 应用程序可以提示用户，并说明如何安装应用程序并将其添加到 Azure AD。 |
| interaction_required |请求需要用户交互。 例如，需要额外的身份验证步骤。 |使用同一资源重试请求。 |
| temporarily_unavailable |服务器暂时繁忙，无法处理请求。 |重试请求。 客户端应用程序可以向用户说明，其响应由于临时状态而延迟。 |

## <a name="use-the-access-token"></a>使用访问令牌
已经成功获取 `access_token`，现在可以通过在 `Authorization` 标头中包含令牌，在 Web API 的请求中使用令牌。

> [!TIP]
> 在 Postman 中执行此请求！ （先替换 `Authorization` 标头）[![在 Postman 中运行](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-access-token"></a>刷新访问令牌
Access_token 生存期很短，必须在其过期后刷新，才能继续访问资源。  为此，可以向 `/token` 终结点提交另一个 `POST` 请求，但这次要提供 `refresh_token` 而不是 `code`：

```
// Line breaks for legibility only

POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh      // NOTE: Only required for web apps
```

> [!TIP]
> 尝试在 Postman 中执行此请求！ （别忘了替换 `refresh_token`）[![在 Postman 中运行](./media/active-directory-v2-protocols-oauth-code/runInPostman.png)](https://app.getpostman.com/run-collection/8f5715ec514865a07e6a)
> 
> 

| 参数 |  | 说明 |
| --- | --- | --- |
| tenant |必填 |请求路径中的 `{tenant}` 值可用于控制哪些用户可以登录应用程序。  允许的值为 `common`、`organizations`、`consumers` 和租户标识符。  有关更多详细信息，请参阅[协议基础知识](active-directory-v2-protocols.md#endpoints)。 |
| client_id |必填 |注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) 分配给应用的应用程序 ID。 |
| grant_type |必填 |必须是授权代码流的此阶段的 `refresh_token`。 |
| 作用域 |必填 |范围的空格分隔列表。  在此阶段请求的范围必须等效于或者为原始 authorization_code 请求阶段中所请求的范围子集。  如果这个请求中指定的范围遍及多个资源服务器，v2.0 终结点将返回第一个范围内所指定资源的令牌。  有关范围的更加详细的说明，请参阅[权限、许可和范围](active-directory-v2-scopes.md)。 |
| refresh_token |必填 |在流的第二个阶段获取的 refresh_token。 |
| redirect_uri |必填 |用于获取 authorization_code 的相同 redirect_uri 值。 |
| client_secret |Web 应用所需 |在应用程序注册门户中为应用程序创建的应用程序机密。  其不应用于本机应用，因为设备无法可靠地存储 client_secrets。  Web 应用和 Web API 都需要应用程序机密，能够将 client_secret 安全地存储在服务器端。 |

#### <a name="successful-response"></a>成功的响应
成功的令牌响应如下：

```
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 参数 | 说明 |
| --- | --- |
| access_token |请求的访问令牌。 应用可以使用此令牌验证受保护的资源，例如 Web API。 |
| token_type |指示令牌类型值。 Azure AD 唯一支持的类型是 Bearer |
| expires_in |访问令牌的有效期（以秒为单位）。 |
| 作用域 |access_token 有效的范围。 |
| refresh_token |新的 OAuth 2.0 刷新令牌。 应该将旧刷新令牌替换为新获取的这个刷新令牌，以确保刷新令牌的有效期尽可能地长。 <br> **注意：**仅当已请求 `offline_access` 作用域时提供。 |
| id_token |无符号 JSON Web 令牌 (JWT)。 应用程序可以 base64Url 解码此令牌的段，以请求已登录用户的相关信息。 应用可以缓存并显示值，但不应依赖于这些值获取任何授权或安全边界。  有关 id_token 的详细信息，请参阅 [v2.0 终结点令牌参考](active-directory-v2-tokens.md)。 <br> **注意：**仅当已请求 `openid` 作用域时提供。 |

#### <a name="error-response"></a>错误响应
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数 | 说明 |
| --- | --- |
| error |用于分类发生的错误类型与响应错误的错误码字符串。 |
| error_description |帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| error_codes |帮助诊断的 STS 特定错误代码列表。 |
| timestamp |发生错误的时间。 |
| trace_id |帮助诊断的请求唯一标识符。 |
| correlation_id |帮助跨组件诊断的请求唯一标识符。 |

有关错误代码的描述和建议的客户端操作，请参阅[令牌终结点错误的错误代码](#error-codes-for-token-endpoint-errors)。

