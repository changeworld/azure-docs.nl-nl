---
title: Een technische SAML-profiel definiëren in een aangepast beleid
titleSuffix: Azure AD B2C
description: Definieer een technische SAML-profiel in een aangepast beleid in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/13/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8c81d2bc499c3d9cae262ef62be2dac2d7280be7
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78183836"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Een technische SAML-profiel definiëren in een Azure Active Directory B2C aangepast beleid

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) biedt ondersteuning voor de SAML 2,0-ID-provider. In dit artikel worden de specifieke specificaties beschreven van een technisch profiel voor interactie met een claim provider die ondersteuning biedt voor dit gestandaardiseerde protocol. Met een SAML-technische profiel kunt u met een id-provider op basis van SAML communiceren, zoals [ADFS](identity-provider-adfs2016-custom.md) en [Sales Force](identity-provider-salesforce-custom.md). Met deze Federatie kunnen uw gebruikers zich aanmelden met hun bestaande sociale of bedrijfs identiteiten.

## <a name="metadata-exchange"></a>Uitwisseling van meta gegevens

Meta gegevens worden gebruikt in het SAML-protocol om de configuratie van een SAML-partij, zoals een service provider of ID-provider, beschikbaar te stellen. Meta gegevens definiëren de locatie van de services, zoals aanmelden en afmelden, certificaten, aanmeldings methode en meer. De ID-provider gebruikt de meta gegevens om te weten hoe er met Azure AD B2C kan worden gecommuniceerd. De meta gegevens worden geconfigureerd in XML-indeling en kunnen met een digitale hand tekening worden ondertekend, zodat de andere partij de integriteit van de meta gegevens kan valideren. Wanneer Azure AD B2C federeert met een SAML-ID-provider, fungeert deze als een service provider die een SAML-aanvraag initieert en wacht op een SAML-reactie. En, in sommige gevallen, accepteert niet-aangevraagde SAML-authenticatie, ook wel bekend als id-provider gestart.

De meta gegevens kunnen in beide partijen worden geconfigureerd als "statische meta gegevens" of "dynamische meta gegevens". In de statische modus kopieert u de volledige meta gegevens van een partij en stelt u deze in bij de andere partij. In de dynamische modus stelt u de URL in op de meta gegevens terwijl de andere partij de configuratie dynamisch leest. De principes zijn hetzelfde, u kunt de meta gegevens van het Azure AD B2C technische profiel instellen in uw ID-provider en de meta gegevens van de ID-provider instellen in Azure AD B2C.

Elke SAML-ID-provider heeft verschillende stappen om de service provider weer te geven en in te stellen, in dit geval Azure AD B2C, en de Azure AD B2C meta gegevens in de ID-provider in te stellen. Raadpleeg de documentatie van uw identiteits provider voor richt lijnen over hoe u dit doet.

In het volgende voor beeld ziet u een URL-adres naar de SAML-meta gegevens van een Azure AD B2C technisch profiel:

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

Vervang de volgende waarden:

- **uw-Tenant naam** met de naam van uw Tenant, zoals fabrikam.b2clogin.com.
- **uw-beleid** met de naam van uw beleid. Gebruik het beleid voor het configureren van het technische profiel voor de SAML-provider of een beleid dat van dat beleid wordt overgenomen.
- **uw technische profiel** met uw naam van het technische profiel van uw SAML-identiteits provider.

## <a name="digital-signing-certificates-exchange"></a>Certificaten voor digitale ondertekening uitwisselen

Als u een vertrouwens relatie tussen Azure AD B2C en uw SAML-ID-provider wilt maken, moet u een geldig x509-certificaat met de persoonlijke sleutel opgeven. U uploadt het certificaat met de persoonlijke sleutel (. pfx-bestand) naar het sleutel archief Azure AD B2C beleid. Azure AD B2C het SAML-aanmeldings verzoek digitaal ondertekenen met het certificaat dat u opgeeft.

Het certificaat wordt op de volgende manieren gebruikt:

- Azure AD B2C genereert en ondertekent een SAML-aanvraag met behulp van de Azure AD B2C persoonlijke sleutel van het certificaat. De SAML-aanvraag wordt verzonden naar de ID-provider, die de aanvraag valideert met Azure AD B2C open bare sleutel van het certificaat. Het open bare certificaat van Azure AD B2C is toegankelijk via de meta gegevens van technische profielen. U kunt het CER-bestand ook hand matig uploaden naar uw SAML-ID-provider.
- De ID-provider ondertekent de gegevens die naar Azure AD B2C worden verzonden met behulp van de persoonlijke sleutel van het certificaat van de identiteits provider. Azure AD B2C valideert de gegevens met behulp van het open bare certificaat van de identiteits provider. Elke id-provider heeft verschillende stappen voor Setup. Raadpleeg de documentatie van uw identiteits provider voor richt lijnen over hoe u dit doet. In Azure AD B2C moet uw beleid toegang hebben tot de open bare sleutel van het certificaat met behulp van de meta gegevens van de identiteits provider.

Een zelfondertekend certificaat is acceptabel voor de meeste scenario's. Voor productie omgevingen kunt u het beste een x509-certificaat gebruiken dat is uitgegeven door een certificerings instantie. Zoals verderop in dit document wordt beschreven, kunt u voor een niet-productie omgeving de SAML-ondertekening aan beide zijden uitschakelen.

In het volgende diagram ziet u de meta gegevens en de uitwisseling van certificaten:

![meta gegevens en certificaat uitwisseling](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)

## <a name="digital-encryption"></a>Digitale versleuteling

De ID-provider maakt altijd gebruik van een open bare sleutel van een versleutelings certificaat in een Azure AD B2C technisch profiel om de SAML-antwoord bevestiging te versleutelen. Als Azure AD B2C de gegevens moet ontsleutelen, wordt het persoonlijke deel van het versleutelings certificaat gebruikt.

De bevestiging van een SAML-reactie versleutelen:

1. Upload een geldig x509-certificaat met de persoonlijke sleutel (. pfx-bestand) naar het sleutel archief Azure AD B2C beleid.
2. Voeg een **CryptographicKey** -element met de id `SamlAssertionDecryption` toe aan de **CryptographicKeys** -verzameling van het technische profiel. Stel de **StorageReferenceId** in op de naam van de beleids sleutel die u in stap 1 hebt gemaakt.
3. Stel de **WantsEncryptedAssertions** van de technische profielen in op `true`.
4. Werk de ID-provider bij met de nieuwe meta gegevens voor het technische profiel van Azure AD B2C. U moet de sleutel **descriptor** zien waarvan de eigenschap **gebruiken** is ingesteld op `encryption` die de open bare sleutel van uw certificaat bevat.

In het volgende voor beeld wordt de sectie technische profiel versleuteling Azure AD B2C van de meta gegevens weer gegeven:

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol

Het **naam** kenmerk van het protocol element moet worden ingesteld op `SAML2`.

## <a name="output-claims"></a>Uitvoer claims

Het **OutputClaims** -element bevat een lijst met claims die zijn geretourneerd door de SAML-ID-provider in het gedeelte `AttributeStatement`. Mogelijk moet u de naam van de claim die in uw beleid is gedefinieerd, toewijzen aan de naam die is gedefinieerd in de ID-provider. U kunt ook claims toevoegen die niet worden geretourneerd door de ID-provider zolang u het kenmerk `DefaultValue` hebt ingesteld.

Als u de **NamedId** van de SAML-verklaring als een genormaliseerde claim wilt lezen, stelt u de claim **PartnerClaimType** **in op** `assertionSubjectName`. Zorg ervoor dat het **NameID** de eerste waarde in Assertion XML is. Wanneer u meer dan één bevestiging definieert, wordt in Azure AD B2C de onderwerpwaarde van de laatste bevestiging gekozen.

Het **OutputClaimsTransformations** -element kan een verzameling **OutputClaimsTransformation** -elementen bevatten die worden gebruikt voor het wijzigen van de uitvoer claims of voor het genereren van nieuwe.

In het volgende voor beeld worden de claims weer gegeven die zijn geretourneerd door de Facebook-ID-provider:

- De claim **issuerUserId** wordt toegewezen aan de **assertionSubjectName** -claim.
- De **first_name** claim wordt toegewezen aan de claim voor de **OpgegevenNaam** .
- De **last_name** claim wordt toegewezen aan de claim **Achternaam** .
- De **DisplayName** claim zonder naam toewijzing.
- De **e-mail** claim zonder naam toewijzing.

Het technische profiel retourneert ook claims die niet worden geretourneerd door de ID-provider:

- De claim **Identity provider** die de naam van de ID-provider bevat.
- De **authenticationSource** claim met de standaard waarde **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metagegevens

| Kenmerk | Vereist | Beschrijving |
| --------- | -------- | ----------- |
| PartnerEntity | Ja | URL van de meta gegevens van de SAML-ID-provider. Kopieer de meta gegevens van de identiteits provider en voeg deze toe in het CDATA-element `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | Nee | Geeft aan of voor het technische profiel alle uitgaande verificatie aanvragen moeten worden ondertekend. Mogelijke waarden: `true` of `false`. De standaard waarde is `true`. Wanneer de waarde is ingesteld op `true`, moet de cryptografie sleutel **SamlMessageSigning** worden opgegeven en alle uitgaande verificatie aanvragen worden ondertekend. Als de waarde is ingesteld op `false`, worden de para meters voor **SigAlg** en **hand tekeningen** (query teken reeks of post parameter) wegge laten uit de aanvraag. Deze meta gegevens bepalen ook het kenmerk **AuthnRequestsSigned** van meta gegevens, dat wordt uitgevoerd in de meta gegevens van het Azure AD B2C technische profiel dat wordt gedeeld met de ID-provider. Azure AD B2C de aanvraag niet ondertekent als de waarde van **WantsSignedRequests** in de meta gegevens van het technische profiel is ingesteld op `false` en de meta gegevens **WantAuthnRequestsSigned** van de identiteits provider is ingesteld op `false` of niet is opgegeven. |
| XmlSignatureAlgorithm | Nee | De methode die Azure AD B2C gebruikt voor het ondertekenen van de SAML-aanvraag. Met deze meta gegevens bepaalt u de waarde van de para meter **SigAlg** (query teken reeks of post parameter) in de SAML-aanvraag. Mogelijke waarden: `Sha256`, `Sha384`, `Sha512`of `Sha1`. Zorg ervoor dat u het handtekening algoritme aan beide zijden met dezelfde waarde configureert. Gebruik alleen de algoritme die door uw certificaat wordt ondersteund. |
| WantsSignedAssertions | Nee | Geeft aan of voor het technische profiel alle binnenkomende verklaringen moeten worden ondertekend. Mogelijke waarden: `true` of `false`. De standaard waarde is `true`. Als de waarde is ingesteld op `true`, moeten alle bevestigingen `saml:Assertion` verzonden door de ID-provider naar Azure AD B2C worden ondertekend. Als de waarde is ingesteld op `false`, mag de ID-provider de bevestigingen niet ondertekenen, maar zelfs als dit wel het geval is, Azure AD B2C de hand tekening niet valideren. Deze meta gegevens bepalen ook de **WantsAssertionsSigned**van de meta gegevens, die wordt uitgevoerd in de meta gegevens van het Azure AD B2C technische profiel dat wordt gedeeld met de ID-provider. Als u de validatie van beweringen uitschakelt, kunt u ook de validatie van de reactie handtekening uitschakelen (Zie **ResponsesSigned**) voor meer informatie. |
| ResponsesSigned | Nee | Mogelijke waarden: `true` of `false`. De standaard waarde is `true`. Als de waarde is ingesteld op `false`, mag de ID-provider het SAML-antwoord niet ondertekenen, maar zelfs als dit wel het geval is, wordt de hand tekening niet door Azure AD B2C gevalideerd. Als de waarde is ingesteld op `true`, wordt het SAML-antwoord dat door de ID-provider is verzonden naar Azure AD B2C ondertekend en moet het worden gevalideerd. Als u de SAML-antwoord validatie uitschakelt, kunt u ook de validatie van de hand tekening van de bevestiging uitschakelen (Zie **WantsSignedAssertions**) voor meer informatie. |
| WantsEncryptedAssertions | Nee | Geeft aan of alle binnenkomende verklaringen moeten worden versleuteld met het technische profiel. Mogelijke waarden: `true` of `false`. De standaard waarde is `false`. Als de waarde is ingesteld op `true`, moeten bevestigingen die door de ID-provider naar Azure AD B2C worden verzonden, worden ondertekend en moet de cryptografische **SamlAssertionDecryption** worden opgegeven. Als de waarde is ingesteld op `true`, bevatten de meta gegevens van het technische profiel van Azure AD B2C de sectie **versleuteling** . De ID-provider leest de meta gegevens en versleutelt de SAML-reactie bevestiging met de open bare sleutel die is opgenomen in de meta gegevens van het technische profiel van Azure AD B2C. Als u de beweringen versleuteling inschakelt, moet u mogelijk ook de validatie van de reactie handtekening uitschakelen (Zie **ResponsesSigned**) voor meer informatie. |
| IdpInitiatedProfileEnabled | Nee | Hiermee geeft u op of een sessie profiel voor eenmalige aanmelding is ingeschakeld dat is geïnitieerd door een SAML-ID-provider profiel. Mogelijke waarden: `true` of `false`. De standaardwaarde is `false`. In de stroom die door de ID-provider is gestart, wordt de gebruiker extern geverifieerd en wordt een ongevraagde reactie verzonden naar Azure AD B2C, die vervolgens het token gebruikt, worden de stappen voor het uitvoeren van de indeling uitgevoerd en wordt vervolgens een reactie naar de Relying Party toepassing verzonden. |
| NameIdPolicyFormat | Nee | Hiermee geeft u beperkingen op voor de naam-id die moet worden gebruikt om het aangevraagde onderwerp weer te geven. Als u dit weglaat, kan elk type id dat wordt ondersteund door de ID-provider voor het aangevraagde onderwerp, worden gebruikt. Bijvoorbeeld `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`. **NameIdPolicyFormat** kan worden gebruikt met **NameIdPolicyAllowCreate**. Raadpleeg de documentatie van uw identiteits provider voor meer informatie over welke naam-ID-beleids regels worden ondersteund. |
| NameIdPolicyAllowCreate | Nee | Wanneer u **NameIdPolicyFormat**gebruikt, kunt u ook de eigenschap `AllowCreate` van **NameIDPolicy**opgeven. De waarde van deze meta gegevens is `true` of `false` om aan te geven of de ID-provider een nieuw account mag maken tijdens de aanmeldings stroom. Raadpleeg de documentatie van uw identiteits provider voor richt lijnen over hoe u dit doet. |
| AuthenticationRequestExtensions | Nee | Optionele protocol bericht extensie-elementen die zijn overeengekomen tussen Azure AD BC en de ID-provider. De uitbrei ding wordt weer gegeven in XML-indeling. U voegt de XML-gegevens in het CDATA-element `<![CDATA[Your IDP metadata]]>`. Raadpleeg de documentatie van uw identiteits provider om na te gaan of het element Extensions wordt ondersteund. |
| IncludeAuthnContextClassReferences | Nee | Hiermee geeft u een of meer URI-verwijzingen op die verificatie context klassen identificeren. Als u bijvoorbeeld een gebruiker wilt toestaan zich aan te melden met gebruikers naam en wacht woord, stelt u de waarde in op `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`. Als u het aanmelden via de gebruikers naam en het wacht woord via een beveiligde sessie (SSL/TLS) wilt toestaan, geeft u `PasswordProtectedTransport`op. Raadpleeg de documentatie van uw identiteits provider voor meer informatie over de **AuthnContextClassRef** -uri's die worden ondersteund. Geef meerdere Uri's op als een door komma's gescheiden lijst. |
| IncludeKeyInfo | Nee | Geeft aan of de SAML-verificatie aanvraag de open bare sleutel van het certificaat bevat wanneer de binding is ingesteld op `HTTP-POST`. Mogelijke waarden: `true` of `false`. |
| IncludeClaimResolvingInClaimsHandling  | Nee | Voor invoer-en uitvoer claims geeft u op of [claim omzetting](claim-resolver-overview.md) in het technische profiel is opgenomen. Mogelijke waarden: `true`, of `false` (standaard). Als u een claim conflict Oplosser wilt gebruiken in het technische profiel, stelt u dit in op `true`. |

## <a name="cryptographic-keys"></a>Cryptografische sleutels

Het **CryptographicKeys** -element bevat de volgende kenmerken:

| Kenmerk |Vereist | Beschrijving |
| --------- | ----------- | ----------- |
| SamlMessageSigning |Ja | Het x509-certificaat (RSA key set) dat moet worden gebruikt voor het ondertekenen van SAML-berichten. Azure AD B2C gebruikt deze sleutel om de aanvragen te ondertekenen en te verzenden naar de ID-provider. |
| SamlAssertionDecryption |Ja | Het x509-certificaat (RSA key set) dat wordt gebruikt voor het ontsleutelen van SAML-berichten. Dit certificaat moet worden verschaft door de ID-provider. Azure AD B2C gebruikt dit certificaat voor het ontsleutelen van de gegevens die zijn verzonden door de ID-provider. |
| MetadataSigning |Nee | Het x509-certificaat (RSA key set) dat wordt gebruikt voor het ondertekenen van SAML-meta gegevens. Azure AD B2C gebruikt deze sleutel om de meta gegevens te ondertekenen.  |

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor voor beelden van het werken met SAML-id-providers in Azure AD B2C:

- [ADFS toevoegen als een SAML-ID-provider met behulp van aangepast beleid](identity-provider-adfs2016-custom.md)
- [Meld u aan met Sales Force-accounts via SAML](identity-provider-salesforce-custom.md)
