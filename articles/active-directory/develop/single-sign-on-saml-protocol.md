---
title: Azure-SAML-protocol voor eenmalige aanmelding | Microsoft Docs
description: In dit artikel wordt het SAML-protocol voor eenmalige aanmelding in Azure Active Directory beschreven.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262423"
---
# <a name="single-sign-on-saml-protocol"></a>SAML-protocol voor eenmalige aanmelding

In dit artikel worden de SAML 2,0-verificatie aanvragen en antwoorden beschreven die Azure Active Directory (Azure AD) ondersteunt voor eenmalige aanmelding.

In het onderstaande protocol diagram wordt de volg orde van eenmalige aanmelding beschreven. De Cloud service (de service provider) gebruikt een binding voor HTTP-omleiding om een `AuthnRequest`-element (verificatie aanvraag) door te geven aan Azure AD (de ID-provider). Azure AD maakt vervolgens gebruik van een HTTP Post-binding om een `Response`-element te plaatsen bij de Cloud service.

![Werk stroom voor eenmalige aanmelding](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Als u een gebruikers verificatie wilt aanvragen, stuurt Cloud Services een `AuthnRequest`-element naar Azure AD. Een voor beeld van een SAML 2,0-`AuthnRequest` kan er als volgt uitzien:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parameter |  | Beschrijving |
| --- | --- | --- |
| Id | Vereist | Azure AD gebruikt dit kenmerk om het `InResponseTo` kenmerk van het geretourneerde antwoord in te vullen. De ID mag niet beginnen met een getal, dus een algemene strategie is om een teken reeks als ' id ' te laten voorafgaan door naar de teken reeks representatie van een GUID. `id6c1c178c166d486687be4aaf5e482730` is bijvoorbeeld een geldige ID. |
| Version | Vereist | Deze para meter moet worden ingesteld op **2,0**. |
| IssueInstant | Vereist | Dit is een datum/tijd-teken reeks met een UTC-waarde en een [notatie voor retour afronding ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). In azure AD wordt een DateTime-waarde van dit type verwacht, maar de waarde wordt niet geëvalueerd of gebruikt. |
| AssertionConsumerServiceUrl | Optioneel | Als deze para meter wordt opgegeven, moet deze overeenkomen met de `RedirectUri` van de Cloud service in azure AD. |
| ForceAuthn | Optioneel | Dit is een Booleaanse waarde. Als deze eigenschap waar is, betekent dit dat de gebruiker wordt afgedwongen om zich opnieuw te verifiëren, zelfs als ze een geldige sessie met Azure AD hebben. |
| IsPassive | Optioneel | Dit is een Booleaanse waarde die aangeeft of Azure AD de gebruiker zonder tussen komst van de gebruiker op de achtergrond moet verifiëren, met behulp van de sessie cookie als er een bestaat. Als dit het geval is, probeert Azure AD de gebruiker te verifiëren met behulp van de sessie cookie. |

Alle andere `AuthnRequest` kenmerken, zoals toestemming, doel, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex en ProviderName, worden **genegeerd**.

Azure AD negeert ook het `Conditions`-element in `AuthnRequest`.

### <a name="issuer"></a>Verlener

Het `Issuer`-element in een `AuthnRequest` moet exact overeenkomen met een van de **ServicePrincipalNames** in de Cloud service in azure AD. Dit is normaal gesp roken ingesteld op de **App-ID-URI** die is opgegeven tijdens de registratie van de toepassing.

Een SAML-fragment met het `Issuer`-element ziet eruit als in het volgende voor beeld:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Dit element vraagt een bepaalde naam-ID-indeling aan in het antwoord en is optioneel in `AuthnRequest` elementen die naar Azure AD worden verzonden.

Een `NameIdPolicy`-element ziet eruit als in het volgende voor beeld:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Als `NameIDPolicy` is geleverd, kunt u het optionele `Format` kenmerk toevoegen. Het kenmerk `Format` kan slechts een van de volgende waarden hebben: een andere waarde resulteert in een fout.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory geeft de NameID-claim als een Pairwise-id uit.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory geeft de NameID-claim in de indeling van het e-mail adres.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: met deze waarde kan Azure Active Directory de indeling van de claim selecteren. Azure Active Directory wordt het NameID als een Pairwise-id uitgegeven.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory geeft de NameID-claim als een wille keurig gegenereerde waarde die uniek is voor de huidige SSO-bewerking. Dit betekent dat de waarde tijdelijk is en niet kan worden gebruikt om de verifiërende gebruiker te identificeren.

Azure AD negeert het kenmerk `AllowCreate`.

### <a name="requestauthncontext"></a>RequestAuthnContext
Met het element `RequestedAuthnContext` worden de gewenste verificatie methoden opgegeven. Het is optioneel in `AuthnRequest` elementen die naar Azure AD worden verzonden. Azure AD ondersteunt `AuthnContextClassRef` waarden, zoals `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Bereik
Het element `Scoping`, dat een lijst met id-providers bevat, is optioneel in `AuthnRequest`-elementen die naar Azure AD worden verzonden.

Als u deze opgeeft, neemt u het kenmerk `ProxyCount`, `IDPListOption` of `RequesterID` niet op omdat deze niet worden ondersteund.

### <a name="signature"></a>Handtekening
Neem geen `Signature` element op in `AuthnRequest` elementen, omdat Azure AD geen ondertekende verificatie aanvragen ondersteunt.

### <a name="subject"></a>Onderwerp
Azure AD negeert het `Subject` element van `AuthnRequest` elementen.

## <a name="response"></a>Antwoord
Wanneer een aangevraagde aanmelding is voltooid, boekt Azure AD een reactie op de Cloud service. Een reactie op een geslaagde aanmeldings poging ziet eruit als in het volgende voor beeld:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Antwoord

Het `Response`-element bevat het resultaat van de autorisatie aanvraag. Azure AD stelt de `ID`, `Version` en `IssueInstant` waarden in het element `Response` in. Ook worden de volgende kenmerken ingesteld:

* `Destination`: wanneer de aanmelding is voltooid, wordt deze ingesteld op de `RedirectUri` van de service provider (Cloud service).
* `InResponseTo`: dit is ingesteld op het kenmerk `ID` van het `AuthnRequest`-element dat het antwoord heeft gestart.

### <a name="issuer"></a>Verlener

Azure AD stelt het `Issuer`-element in op `https://login.microsoftonline.com/<TenantIDGUID>/` waarbij \<TenantIDGUID > de Tenant-ID is van de Azure AD-Tenant.

Een antwoord met het element Issuer kan er bijvoorbeeld als volgt uitzien:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Het `Status`-element geeft aan of het aanmelden is geslaagd of mislukt. Het bevat het `StatusCode`-element, dat een code of een set geneste codes bevat die de status van de aanvraag vertegenwoordigt. Het bevat ook het `StatusMessage`-element, dat aangepaste fout berichten bevat die tijdens het aanmeldings proces worden gegenereerd.

<!-- TODO: Add an authentication protocol error reference -->

Het volgende voor beeld is een SAML-reactie op een mislukte aanmeldings poging.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Assertion

Naast de `ID`, `IssueInstant` en `Version`, stelt Azure AD de volgende elementen in het `Assertion`-element van het antwoord in.

#### <a name="issuer"></a>Verlener

Dit is ingesteld op `https://sts.windows.net/<TenantIDGUID>/`waarbij \<TenantIDGUID > de Tenant-ID is van de Azure AD-Tenant.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Handtekening

Azure AD ondertekent de bevestiging als reactie op een geslaagde aanmelding. Het `Signature`-element bevat een digitale hand tekening die de Cloud service kan gebruiken om de bron te verifiëren om de integriteit van de bevestiging te controleren.

Voor het genereren van deze digitale hand tekening maakt Azure AD gebruik van de handtekening sleutel in het `IDPSSODescriptor` element van het meta gegevens document.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Onderwerp

Hiermee geeft u de principal op die het onderwerp is van de instructies in de verklaring. Het bevat een `NameID`-element dat de geverifieerde gebruiker vertegenwoordigt. De `NameID`-waarde is een doel-id die alleen wordt omgeleid naar de service provider die de doel groep is voor het token. Het is permanent. deze kan worden ingetrokken, maar wordt nooit opnieuw toegewezen. Het is ook ondoorzichtig, in dat betekent dat het geen informatie over de gebruiker onthult en niet kan worden gebruikt als een id voor kenmerk query's.

Het kenmerk `Method` van het element `SubjectConfirmation` is altijd ingesteld op `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Voorwaarden

Dit element bevat voor waarden die het acceptabele gebruik van SAML-bevestigingen definiëren.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Met de kenmerken `NotBefore` en `NotOnOrAfter` geeft u het interval op waarmee de bevestiging geldig is.

* De waarde van het kenmerk `NotBefore` is gelijk aan of enigszins (kleiner dan een seconde) later dan de waarde van `IssueInstant` kenmerk van het `Assertion`-element. Azure AD houdt geen rekening met het tijds verschil tussen zichzelf en de Cloud service (service provider) en voegt deze tijd niet toe aan een buffer.
* De waarde van het kenmerk `NotOnOrAfter` is 70 minuten later dan de waarde van het kenmerk `NotBefore`.

#### <a name="audience"></a>Doelgroep

Dit bevat een URI die een beoogde doel groep identificeert. Azure AD stelt de waarde van dit element in op de waarde van `Issuer` element van de `AuthnRequest` die de aanmelding heeft gestart. Als u de `Audience` waarde wilt evalueren, gebruikt u de waarde van de `App ID URI` die is opgegeven tijdens de registratie van de toepassing.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Net als de `Issuer` waarde moet de `Audience`-waarde exact overeenkomen met een van de service-principal-namen die de Cloud service in azure AD vertegenwoordigt. Als de waarde van het `Issuer` element echter geen URI-waarde is, is de `Audience` waarde in het antwoord de `Issuer` waarde die met `spn:`wordt voorafgegaan.

#### <a name="attributestatement"></a>AttributeStatement

Dit bevat claims over het onderwerp of de gebruiker. Het volgende fragment bevat een voor beeld-`AttributeStatement` element. Het weglatings teken geeft aan dat het element meerdere kenmerken en kenmerk waarden kan bevatten.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Claim naam** : de waarde van het kenmerk `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) is de User Principal name van de geverifieerde gebruiker, zoals `testuser@managedtenant.com`.
* **Claim voor ObjectIdentifier** : de waarde van het kenmerk `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) is de `ObjectId` van het Directory-object dat de geverifieerde gebruiker in azure AD vertegenwoordigt. `ObjectId` is een onveranderbare, wereld wijd uniek en veilige id van de geverifieerde gebruiker opnieuw gebruiken.

#### <a name="authnstatement"></a>AuthnStatement

Dit element beweringt dat het onderwerp van de verklaring op een bepaald moment is geverifieerd met een bepaalde methode.

* Het kenmerk `AuthnInstant` geeft de tijd aan waarop de gebruiker is geverifieerd met Azure AD.
* Het `AuthnContext`-element geeft de verificatie context op die wordt gebruikt voor het verifiëren van de gebruiker.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
