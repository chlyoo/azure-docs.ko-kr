---
title: 보안 Azure AD Domain Services | Microsoft Docs
description: Azure Active Directory Domain Services 관리 되는 도메인에 대해 weak 암호화, 이전 프로토콜 및 NTLM 암호 해시 동기화를 사용 하지 않도록 설정 하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: a89c898e150facc9860d86e18a7acc42f5e0f441
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618861"
---
# <a name="disable-weak-ciphers-and-password-hash-synchronization-to-secure-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services 관리 되는 도메인을 보호 하기 위해 약한 암호화 및 암호 해시 동기화 사용 안 함

기본적으로 Azure Active Directory Domain Services (Azure AD DS)는 NTLM v1 및 TLS v1과 같은 암호를 사용할 수 있도록 합니다. 이러한 암호화는 일부 레거시 응용 프로그램에 필요할 수 있지만 weak로 간주 되며 필요 하지 않은 경우 사용 하지 않도록 설정할 수 있습니다. Azure AD Connect를 사용 하 여 온-프레미스 하이브리드 연결을 사용 하는 경우 NTLM 암호 해시의 동기화를 사용 하지 않도록 설정할 수도 있습니다.

이 문서에서는 NTLM v1 및 TLS v1 암호화를 사용 하지 않도록 설정 하 고 NTLM 암호 해시 동기화를 사용 하지 않도록 설정 하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서를 완료하려면 다음 리소스가 필요합니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 [Azure Active Directory Domain Services 관리형 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].
* Azure PowerShell을 설치하고 구성합니다.
    * 필요한 경우 지침에 따라 [Azure PowerShell 모듈을 설치하고 Azure 구독에 연결](/powershell/azure/install-az-ps)합니다.
    * [Connect-AzAccount][Connect-AzAccount] cmdlet을 사용하여 Azure 구독에 로그인합니다.
* Azure AD PowerShell을 설치하고 구성합니다.
    * 필요한 경우 지침에 따라 [Azure AD PowerShell 모듈을 설치하고 Azure AD에 연결](/powershell/azure/active-directory/install-adv2)합니다.
    * [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 로그인합니다.

## <a name="disable-weak-ciphers-and-ntlm-password-hash-sync"></a>약한 암호화 및 NTLM 암호 해시 동기화 사용 안 함

약한 암호 그룹 및 NTLM 자격 증명 해시 동기화를 사용 하지 않도록 설정 하려면 Azure 계정에 로그인 한 다음 [AzResource][Get-AzResource] cmdlet을 사용 하 여 azure AD DS 리소스를 가져옵니다.

> [!TIP]
> [AzResource][Get-AzResource] 명령을 사용 하 여 *Microsoft AAD/domainservices* 리소스가 존재 하지 않는 오류가 발생 하면 [모든 Azure 구독 및 관리 그룹을 관리 하기 위해 액세스 권한을 높여야][global-admin]합니다.

```powershell
Login-AzAccount

$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"
```

다음으로 *Domainsecuritysettings* 를 정의 하 여 다음 보안 옵션을 구성 합니다.

1. NTLM v1 지원 사용 안 함
2. 온-프레미스 AD에서 NTLM 암호 해시 동기화를 사용하지 않도록 설정합니다.
3. TLS v1을 사용 하지 않도록 설정 합니다.

> [!IMPORTANT]
> Azure AD DS 관리 되는 도메인에서 NTLM 암호 해시 동기화를 사용 하지 않도록 설정 하면 사용자 및 서비스 계정에서 LDAP 단순 바인딩을 수행할 수 없습니다. LDAP 단순 바인딩을 수행 해야 하는 경우 다음 명령에서 *"SyncNtlmPasswords" = "Disabled";* 보안 구성 옵션을 설정 하지 마세요.

```powershell
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}
```

마지막으로 [AzResource][Set-AzResource] cmdlet을 사용 하 여 관리 되는 도메인에 정의 된 보안 설정을 적용 합니다. 첫 번째 단계에서 Azure AD DS 리소스를 지정 하 고 이전 단계의 보안 설정을 지정 합니다.

```powershell
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

보안 설정이 관리 되는 도메인에 적용 되는 데 몇 분 정도 걸립니다.

> [!IMPORTANT]
> NTLM을 사용 하지 않도록 설정한 후 Azure AD Connect에서 전체 암호 해시 동기화를 수행 하 여 관리 되는 도메인에서 모든 암호 해시를 제거 합니다. NTLM을 사용 하지 않는 경우 암호 해시 동기화를 적용 하지 않는 경우 사용자 계정에 대 한 NTLM 암호 해시는 다음 암호 변경 시에만 제거 됩니다. 이 동작을 통해 NTLM이 인증 방법으로 사용 되는 시스템에 캐시 된 자격 증명이 있는 경우 사용자가 계속 로그인 할 수 있습니다.
>
> NTLM 암호 해시가 Kerberos 암호 해시와 다르면 NTLM으로의 대체가 작동 하지 않습니다. 또한 VM이 관리 되는 도메인 컨트롤러에 연결 되어 있으면 캐시 된 자격 증명은 더 이상 작동 하지 않습니다.  

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대 한 자세한 내용은 관리 되는 [도메인에서 개체 및 자격 증명을 동기화 하는 방법][synchronization]을 참조 하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[global-admin]: ../role-based-access-control/elevate-access-global-admin.md
[synchronization]: synchronization.md

<!-- EXTERNAL LINKS -->
[Get-AzResource]: /powershell/module/az.resources/Get-AzResource
[Set-AzResource]: /powershell/module/Az.Resources/Set-AzResource
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
