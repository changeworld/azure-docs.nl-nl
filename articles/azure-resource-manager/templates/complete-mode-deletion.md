---
title: Modus voor volledige verwijdering
description: Toont hoe bron typen het verwijderen van de modus volt ooien in Azure Resource Manager sjablonen verwerken.
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 5f797974212636460306c6a17869d6b8380545ab
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664403"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Verwijdering van Azure-resources voor implementaties in de volledige modus

In dit artikel wordt beschreven hoe bron typen worden verwijderd wanneer dat niet het geval is in een sjabloon die in de modus voltooid wordt geïmplementeerd.

De resource typen die zijn gemarkeerd met **Ja** , worden verwijderd wanneer het type niet in de sjabloon staat die is geïmplementeerd met de modus volt ooien.

De resource typen die zijn gemarkeerd met **Nee** , worden niet automatisch verwijderd wanneer deze niet in de sjabloon staan; ze worden echter verwijderd als de bovenliggende resource wordt verwijderd. Zie [Azure Resource Manager implementatie modi](deployment-modes.md)voor een volledige beschrijving van het gedrag.

Als u naar [meer dan één resource groep in een sjabloon](cross-resource-group-deployment.md)implementeert, kunnen resources in de resource groep die zijn opgegeven in de implementatie bewerking worden verwijderd. Resources in de secundaire resource groepen worden niet verwijderd.

Ga naar de naam ruimte van een resource provider:
> [!div class="op_single_selector"]
> - [Micro soft. AAD](#microsoftaad)
> - [Micro soft. Addons](#microsoftaddons)
> - [Micro soft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Micro soft. Advisor](#microsoftadvisor)
> - [Micro soft. AlertsManagement](#microsoftalertsmanagement)
> - [Micro soft. AnalysisServices](#microsoftanalysisservices)
> - [Micro soft. ApiManagement](#microsoftapimanagement)
> - [Micro soft. AppConfiguration](#microsoftappconfiguration)
> - [Micro soft. AppPlatform](#microsoftappplatform)
> - [Micro soft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Micro soft. Automation](#microsoftautomation)
> - [Micro soft. Azconfig](#microsoftazconfig)
> - [Micro soft. Azure. Genève](#microsoftazuregeneva)
> - [Micro soft. AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Micro soft. Azureworden](#microsoftazuredata)
> - [Micro soft. AzureStack](#microsoftazurestack)
> - [Micro soft. batch](#microsoftbatch)
> - [Micro soft. billing](#microsoftbilling)
> - [Micro soft. BingMaps](#microsoftbingmaps)
> - [Micro soft. Block Chain](#microsoftblockchain)
> - [Micro soft. blauw druk](#microsoftblueprint)
> - [Micro soft. BotService](#microsoftbotservice)
> - [Micro soft. cache](#microsoftcache)
> - [Micro soft. capacity](#microsoftcapacity)
> - [Micro soft. CDN](#microsoftcdn)
> - [Micro soft. CertificateRegistration](#microsoftcertificateregistration)
> - [Micro soft. ClassicCompute](#microsoftclassiccompute)
> - [Micro soft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Micro soft. ClassicNetwork](#microsoftclassicnetwork)
> - [Micro soft. ClassicStorage](#microsoftclassicstorage)
> - [Micro soft. CognitiveServices](#microsoftcognitiveservices)
> - [Micro soft. commerce](#microsoftcommerce)
> - [Micro soft. compute](#microsoftcompute)
> - [Micro soft. verbruik](#microsoftconsumption)
> - [Micro soft. ContainerInstance](#microsoftcontainerinstance)
> - [Micro soft. ContainerRegistry](#microsoftcontainerregistry)
> - [Micro soft. container service](#microsoftcontainerservice)
> - [Micro soft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Micro soft. CostManagement](#microsoftcostmanagement)
> - [Micro soft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Micro soft. CustomProviders](#microsoftcustomproviders)
> - [Micro soft. DataBox](#microsoftdatabox)
> - [Micro soft. DataBoxEdge](#microsoftdataboxedge)
> - [Micro soft. Databricks](#microsoftdatabricks)
> - [Micro soft. DataCatalog](#microsoftdatacatalog)
> - [Micro soft. DataFactory](#microsoftdatafactory)
> - [Micro soft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Micro soft. data Lake Store](#microsoftdatalakestore)
> - [Micro soft. DataMigration](#microsoftdatamigration)
> - [Micro soft. DataShare](#microsoftdatashare)
> - [Micro soft. DBforMariaDB](#microsoftdbformariadb)
> - [Micro soft. DBforMySQL](#microsoftdbformysql)
> - [Micro soft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Micro soft. DeploymentManager](#microsoftdeploymentmanager)
> - [Micro soft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Micro soft.-apparaten](#microsoftdevices)
> - [Micro soft. DevOps](#microsoftdevops)
> - [Micro soft. DevSpaces](#microsoftdevspaces)
> - [Micro soft. DevTestLab](#microsoftdevtestlab)
> - [Micro soft. DocumentDB](#microsoftdocumentdb)
> - [Micro soft. DomainRegistration](#microsoftdomainregistration)
> - [Micro soft. DynamicsLcs](#microsoftdynamicslcs)
> - [Micro soft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Micro soft. EventGrid](#microsofteventgrid)
> - [Micro soft. EventHub](#microsofteventhub)
> - [Micro soft. features](#microsoftfeatures)
> - [Micro soft. Gallery](#microsoftgallery)
> - [Micro soft. Genomics](#microsoftgenomics)
> - [Micro soft. GuestConfiguration](#microsoftguestconfiguration)
> - [Micro soft. HanaOnAzure](#microsofthanaonazure)
> - [Micro soft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Micro soft. HDInsight](#microsofthdinsight)
> - [Micro soft. HealthcareApis](#microsofthealthcareapis)
> - [Micro soft. HybridCompute](#microsofthybridcompute)
> - [Micro soft. HybridData](#microsofthybriddata)
> - [Micro soft. Hydra](#microsofthydra)
> - [Micro soft. ImportExport](#microsoftimportexport)
> - [Micro soft. intune](#microsoftintune)
> - [Micro soft. IoTCentral](#microsoftiotcentral)
> - [Micro soft. IoTSpaces](#microsoftiotspaces)
> - [Micro soft.-sleutel kluis](#microsoftkeyvault)
> - [Micro soft. Kusto](#microsoftkusto)
> - [Micro soft. LabServices](#microsoftlabservices)
> - [Micro soft. Logic](#microsoftlogic)
> - [Micro soft. MachineLearning](#microsoftmachinelearning)
> - [Micro soft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Micro soft. ManagedIdentity](#microsoftmanagedidentity)
> - [Micro soft. ManagedServices](#microsoftmanagedservices)
> - [Micro soft. Management](#microsoftmanagement)
> - [Micro soft. Maps](#microsoftmaps)
> - [Micro soft. Marketplace](#microsoftmarketplace)
> - [Micro soft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Micro soft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Micro soft. Media](#microsoftmedia)
> - [Micro soft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Micro soft. migrate](#microsoftmigrate)
> - [Micro soft. MixedReality](#microsoftmixedreality)
> - [Micro soft. NetApp](#microsoftnetapp)
> - [Micro soft. notebooks](#microsoftnotebooks)
> - [Micro soft. Network](#microsoftnetwork)
> - [Micro soft. notification hubs](#microsoftnotificationhubs)
> - [Micro soft. ObjectStore](#microsoftobjectstore)
> - [Micro soft. OffAzure](#microsoftoffazure)
> - [Micro soft. OperationalInsights](#microsoftoperationalinsights)
> - [Micro soft. OperationsManagement](#microsoftoperationsmanagement)
> - [Micro soft. peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Micro soft. Portal](#microsoftportal)
> - [Micro soft. PowerBI](#microsoftpowerbi)
> - [Micro soft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Micro soft. ProjectBabylon](#microsoftprojectbabylon)
> - [Micro soft. Recovery Services](#microsoftrecoveryservices)
> - [Micro soft. relay](#microsoftrelay)
> - [Micro soft. RemoteApp](#microsoftremoteapp)
> - [Micro soft. ResourceGraph](#microsoftresourcegraph)
> - [Micro soft. ResourceHealth](#microsoftresourcehealth)
> - [Micro soft. resources](#microsoftresources)
> - [Micro soft. SaaS](#microsoftsaas)
> - [Micro soft. Search](#microsoftsearch)
> - [Micro soft. Security](#microsoftsecurity)
> - [Micro soft. SecurityGraph](#microsoftsecuritygraph)
> - [Micro soft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Micro soft. ServiceFabric](#microsoftservicefabric)
> - [Micro soft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Micro soft. Services](#microsoftservices)
> - [Micro soft. SignalRService](#microsoftsignalrservice)
> - [Micro soft. SiteRecovery](#microsoftsiterecovery)
> - [Micro soft. SoftwarePlan](#microsoftsoftwareplan)
> - [Micro soft. Solutions](#microsoftsolutions)
> - [Micro soft. SpoolService](#microsoftspoolservice)
> - [Micro soft. SQL](#microsoftsql)
> - [Micro soft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Micro soft. Storage](#microsoftstorage)
> - [Micro soft. StorageCache](#microsoftstoragecache)
> - [Micro soft. StorageReplication](#microsoftstoragereplication)
> - [Micro soft. StorageSync](#microsoftstoragesync)
> - [Micro soft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Micro soft. StorageSyncInt](#microsoftstoragesyncint)
> - [Micro soft. StorSimple](#microsoftstorsimple)
> - [Micro soft. StreamAnalytics](#microsoftstreamanalytics)
> - [Micro soft. Subscription](#microsoftsubscription)
> - [Micro soft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Micro soft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Micro soft. VnfManager](#microsoftvnfmanager)
> - [Micro soft. Web](#microsoftweb)
> - [Micro soft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Micro soft. WindowsIoT](#microsoftwindowsiot)
> - [Micro soft. WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DomainServices | Ja |
> | DomainServices / oucontainer | Nee |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | supportProviders | Nee |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aadsupportcases | Nee |
> | addsservices | Nee |
> | middelen | Nee |
> | anonymousapiusers | Nee |
> | configuratie | Nee |
> | logboeken | Nee |
> | rapporten | Nee |
> | servicehealthmetrics | Nee |
> | services | Nee |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configuraties | Nee |
> | generateRecommendations | Nee |
> | metagegevens | Nee |
> | vereisten | Nee |
> | onderdrukkingen | Nee |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | actionRules | Ja |
> | waarschuwingen | Nee |
> | alertsList | Nee |
> | alertsMetaData | Nee |
> | alertsSummary | Nee |
> | alertsSummaryList | Nee |
> | feedback | Nee |
> | smartDetectorAlertRules | Ja |
> | smartDetectorRuntimeEnvironments | Nee |
> | smartGroups | Nee |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Server | Ja |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | reportFeedback | Nee |
> | service | Ja |
> | validateServiceName | Nee |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nee |

## <a name="microsoftappplatform"></a>Micro soft. AppPlatform

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Spring | Ja |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | attestationProviders | Nee |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicAdministrators | Nee |
> | dataAliases | Nee |
> | denyAssignments | Nee |
> | elevateAccess | Nee |
> | findOrphanRoleAssignments | Nee |
> | vergren delingen | Nee |
> | machtigingen | Nee |
> | policyAssignments | Nee |
> | policyDefinitions | Nee |
> | policySetDefinitions | Nee |
> | providerOperations | Nee |
> | roleAssignments | Nee |
> | roleAssignmentsUsageMetrics | Nee |
> | roleDefinitions | Nee |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | automationAccounts | Ja |
> | automationAccounts/configuraties | Ja |
> | automationAccounts/Jobs | Nee |
> | automationAccounts / privateEndpointConnectionProxies | Nee |
> | automationAccounts / privateEndpointConnections | Nee |
> | automationAccounts / privateLinkResources | Nee |
> | automationAccounts/runbooks | Ja |
> | automationAccounts / softwareUpdateConfigurations | Nee |
> | automationAccounts/webhooks | Nee |

## <a name="microsoftazconfig"></a>Micro soft. Azconfig

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | configurationStores | Ja |
> | configurationStores / eventGridFilters | Nee |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | verschillend | Nee |
> | omgevingen/accounts | Nee |
> | omgevingen/accounts/naam ruimten | Nee |
> | omgevingen/accounts/naam ruimten/configuraties | Nee |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | b2cDirectories | Ja |
> | b2ctenants | Nee |

## <a name="microsoftazuredata"></a>Micro soft. Azureworden

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridDataManagers | Ja |
> | postgresInstances | Ja |
> | sqlBigDataClusters | Ja |
> | sqlInstances | Ja |
> | sqlServerRegistrations | Ja |
> | sqlServerRegistrations / sqlServers | Nee |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | registraties | Ja |
> | registraties/customerSubscriptions | Nee |
> | registraties/producten | Nee |
> | verificationKeys | Nee |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | batchAccounts | Ja |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | billingAccounts | Nee |
> | billingAccounts/overeenkomsten | Nee |
> | billingAccounts / billingPermissions | Nee |
> | billingAccounts / billingProfiles | Nee |
> | billingAccounts / billingProfiles / billingPermissions | Nee |
> | billingAccounts / billingProfiles / billingRoleAssignments | Nee |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Nee |
> | billingAccounts / billingProfiles / billingSubscriptions | Nee |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Nee |
> | billingAccounts/billingProfiles/klanten | Nee |
> | billingAccounts/billingProfiles/instructies | Nee |
> | billingAccounts/billingProfiles/facturen | Nee |
> | billingAccounts/billingProfiles/facturen/prijzen overzicht | Nee |
> | billingAccounts/billingProfiles/facturen/trans acties | Nee |
> | billingAccounts / billingProfiles / invoiceSections | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Nee |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Nee |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/overdracht | Nee |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Nee |
> | billingAccounts/billingProfiles/invoiceSections/trans acties | Nee |
> | billingAccounts/billingProfiles/invoiceSections/transfers | Nee |
> | billingAccounts / BillingProfiles / patchOperations | Nee |
> | billingAccounts / billingProfiles / paymentMethods | Nee |
> | billingAccounts/billingProfiles/beleid | Nee |
> | billingAccounts/billingProfiles/prijzen overzicht | Nee |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Nee |
> | billingAccounts/billingProfiles/producten | Nee |
> | billingAccounts/billingProfiles/trans acties | Nee |
> | billingAccounts / billingRoleAssignments | Nee |
> | billingAccounts / billingRoleDefinitions | Nee |
> | billingAccounts / billingSubscriptions | Nee |
> | billingAccounts/billingSubscriptions/facturen | Nee |
> | billingAccounts / createBillingRoleAssignment | Nee |
> | billingAccounts / createInvoiceSectionOperations | Nee |
> | billingAccounts/klanten | Nee |
> | billingAccounts/klanten/billingPermissions | Nee |
> | billingAccounts/klanten/billingSubscriptions | Nee |
> | billingAccounts/klanten/initiateTransfer | Nee |
> | billingAccounts/klanten/beleids regels | Nee |
> | billingAccounts/klanten/producten | Nee |
> | billingAccounts/klanten/trans acties | Nee |
> | billingAccounts/klanten/overdrachten | Nee |
> | billingAccounts/afdelingen | Nee |
> | billingAccounts / enrollmentAccounts | Nee |
> | billingAccounts/facturen | Nee |
> | billingAccounts / invoiceSections | Nee |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Nee |
> | billingAccounts / invoiceSections / billingSubscriptions | Nee |
> | billingAccounts/invoiceSections/billingSubscriptions/overdracht | Nee |
> | billingAccounts/invoiceSections/verhoogde bevoegdheid | Nee |
> | billingAccounts / invoiceSections / initiateTransfer | Nee |
> | billingAccounts / invoiceSections / patchOperations | Nee |
> | billingAccounts / invoiceSections / productMoveOperations | Nee |
> | billingAccounts/invoiceSections/producten | Nee |
> | billingAccounts/invoiceSections/producten/overdracht | Nee |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Nee |
> | billingAccounts/invoiceSections/trans acties | Nee |
> | billingAccounts/invoiceSections/overdrachten | Nee |
> | billingAccounts / lineOfCredit | Nee |
> | billingAccounts / patchOperations | Nee |
> | billingAccounts / paymentMethods | Nee |
> | billingAccounts/producten | Nee |
> | billingAccounts/trans acties | Nee |
> | billingPeriods | Nee |
> | billingPermissions | Nee |
> | billingProperty | Nee |
> | billingRoleAssignments | Nee |
> | billingRoleDefinitions | Nee |
> | createBillingRoleAssignment | Nee |
> | Afdeling | Nee |
> | enrollmentAccounts | Nee |
> | factureer | Nee |
> | Making | Nee |
> | overdrachten/acceptTransfer | Nee |
> | overdrachten/declineTransfer | Nee |
> | overdrachten/operationStatus | Nee |
> | overdrachten/validateTransfer | Nee |
> | validateAddress | Nee |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | mapApis | Ja |
> | updateCommunicationPreference | Nee |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blockchainMembers | Ja |
> | cordaMembers | Ja |
> | Volg | Ja |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | blueprintAssignments | Nee |
> | blueprintAssignments / assignmentOperations | Nee |
> | blueprintAssignments/bewerkingen | Nee |
> | blauw drukken | Nee |
> | blauw drukken/artefacten | Nee |
> | blauw drukken/versies | Nee |
> | blauw drukken/versies/artefacten | Nee |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | botServices | Ja |
> | botServices/kanalen | Nee |
> | botServices/verbindingen | Nee |
> | Talen | Nee |
> | sjablonen | Nee |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Redis | Ja |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appliedReservations | Nee |
> | autoQuotaIncrease | Nee |
> | calculateExchange | Nee |
> | calculatePrice | Nee |
> | calculatePurchasePrice | Nee |
> | catalogi | Nee |
> | commercialReservationOrders | Nee |
> | Exchange | Nee |
> | placePurchaseOrder | Nee |
> | reservationOrders | Nee |
> | reservationOrders / calculateRefund | Nee |
> | reservationOrders/samen voegen | Nee |
> | reservationOrders/reserve ringen | Nee |
> | reservationOrders/reserve ringen/revisies | Nee |
> | reservationOrders/retour neren | Nee |
> | reservationOrders/splitsen | Nee |
> | reservationOrders/swap | Nee |
> | ringen | Nee |
> | resourceProviders | Nee |
> | resources | Nee |
> | validateReservationOrder | Nee |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Nee |
> | CdnWebApplicationFirewallPolicies | Ja |
> | edgenodes | Nee |
> | profiles | Ja |
> | profielen/eind punten | Ja |
> | profielen/eind punten/customdomains | Nee |
> | profielen/eind punten/oorsprong | Nee |
> | validateProbe | Nee |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | certificateOrders | Ja |
> | certificateOrders/certificaten | Nee |
> | validateCertificateRegistrationInformation | Nee |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | bieden | Nee |
> | Domein naam | Ja |
> | Domein naam/mogelijkheden | Nee |
> | Domein naam/internalLoadBalancers | Nee |
> | Domein naam/serviceCertificates | Nee |
> | Domein naam/sleuven | Nee |
> | Domein naam/sleuven/rollen | Nee |
> | Domein naam/sleuven/rollen/metricDefinitions | Nee |
> | Domein naam/sleuven/rollen/metrieken | Nee |
> | moveSubscriptionResources | Nee |
> | operatingSystemFamilies | Nee |
> | operatingSystems | Nee |
> | quotas | Nee |
> | resourceTypes | Nee |
> | validateSubscriptionMoveAvailability | Nee |
> | Informatie | Ja |
> | Informatie/diagnosticSettings | Nee |
> | Informatie/metricDefinitions | Nee |
> | Informatie/meet waarden | Nee |

## <a name="microsoftclassicinfrastructuremigrate"></a>Micro soft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicInfrastructureResources | Nee |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | bieden | Nee |
> | expressRouteCrossConnections | Nee |
> | expressRouteCrossConnections/peerings | Nee |
> | gatewaySupportedDevices | Nee |
> | networkSecurityGroups | Ja |
> | quotas | Nee |
> | reservedIps | Ja |
> | virtualNetworks | Ja |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Nee |
> | virtualNetworks/virtualNetworkPeerings | Nee |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | bieden | Nee |
> | cd's | Nee |
> | images | Nee |
> | osImages | Nee |
> | osPlatformImages | Nee |
> | publicImages | Nee |
> | quotas | Nee |
> | storageAccounts | Ja |
> | Storage accounts/blobServices | Nee |
> | Storage accounts/fileServices | Nee |
> | Storage accounts/metricDefinitions | Nee |
> | Storage accounts/meet waarden | Nee |
> | Storage accounts/queueServices | Nee |
> | Storage accounts/Services | Nee |
> | Storage accounts/Services/diagnosticSettings | Nee |
> | Storage accounts/Services/metricDefinitions | Nee |
> | Storage accounts/Services/metrische gegevens | Nee |
> | Storage accounts/tableServices | Nee |
> | Storage accounts/vmImages | Nee |
> | vmImages | Nee |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | RateCard | Nee |
> | UsageAggregates | Nee |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Availability sets | Ja |
> | diskEncryptionSets | Ja |
> | cd's | Ja |
> | Galerij | Ja |
> | galerieën/toepassingen | Nee |
> | galerieën/toepassingen/versies | Nee |
> | galerieën/afbeeldingen | Nee |
> | galerieën/afbeeldingen/versies | Nee |
> | hostGroups | Ja |
> | hostGroups/hosts | Ja |
> | images | Ja |
> | proximityPlacementGroups | Ja |
> | restorePointCollections | Ja |
> | restorePointCollections / restorePoints | Nee |
> | sharedVMImages | Ja |
> | sharedVMImages/versies | Nee |
> | momentopnamen | Ja |
> | Informatie | Ja |
> | Informatie/extensies | Ja |
> | Informatie/metricDefinitions | Nee |
> | virtualMachineScaleSets | Ja |
> | virtualMachineScaleSets/extensies | Nee |
> | virtualMachineScaleSets/networkInterfaces | Nee |
> | virtualMachineScaleSets/publicIPAddresses | Nee |
> | virtualMachineScaleSets/informatie | Nee |
> | virtualMachineScaleSets/informatie/networkInterfaces | Nee |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | AggregatedCost | Nee |
> | Tegoeden | Nee |
> | Budgetten | Nee |
> | Kosten | Nee |
> | CostTags | Nee |
> | aanvragen | Nee |
> | events | Nee |
> | Prognoses | Nee |
> | extra | Nee |
> | Markt plaatsen | Nee |
> | Pricesheets | Nee |
> | producten | Nee |
> | ReservationDetails | Nee |
> | ReservationRecommendations | Nee |
> | ReservationSummaries | Nee |
> | ReservationTransactions | Nee |
> | Tags | Nee |
> | tenants | Nee |
> | Voorwaarden | Nee |
> | UsageDetails | Nee |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerGroups | Ja |
> | serviceAssociationLinks | Nee |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | registers | Ja |
> | registers/builds | Nee |
> | registers/builds/annuleren | Nee |
> | registers/builds/getLogLink | Nee |
> | registers/buildTasks | Ja |
> | registers/buildTasks/stappen | Nee |
> | registers/eventGridFilters | Nee |
> | registers/generateCredentials | Nee |
> | registers/getBuildSourceUploadUrl | Nee |
> | registers/GetCredentials | Nee |
> | registers/importImage | Nee |
> | registers/privateEndpointConnectionProxies | Nee |
> | registers/privateEndpointConnectionProxies/valideren | Nee |
> | registers/privateEndpointConnections | Nee |
> | registers/privateLinkResources | Nee |
> | registers/queueBuild | Nee |
> | registers/regenerateCredential | Nee |
> | registers/regenerateCredentials | Nee |
> | registers/replicaties | Ja |
> | registers/uitvoeringen | Nee |
> | registers/uitvoeringen/annuleren | Nee |
> | registers/scheduleRun | Nee |
> | registers/scopeMaps | Nee |
> | registers/taskRuns | Ja |
> | registers/taken | Ja |
> | registers/tokens | Nee |
> | registers/updatePolicies | Nee |
> | registers/webhooks | Ja |
> | registers/webhooks/getCallbackConfig | Nee |
> | registers/webhooks/ping | Nee |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | containerServices | Ja |
> | managedClusters | Ja |
> | openShiftManagedClusters | Ja |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Waarschuwingen | Nee |
> | BillingAccounts | Nee |
> | Budgetten | Nee |
> | CloudConnectors | Nee |
> | Connectors | Ja |
> | Afdeling | Nee |
> | Dimensies | Nee |
> | EnrollmentAccounts | Nee |
> | Dump | Nee |
> | ExternalBillingAccounts | Nee |
> | ExternalBillingAccounts/waarschuwingen | Nee |
> | ExternalBillingAccounts/dimensies | Nee |
> | ExternalBillingAccounts/prognose | Nee |
> | ExternalBillingAccounts/query | Nee |
> | ExternalSubscriptions | Nee |
> | ExternalSubscriptions/waarschuwingen | Nee |
> | ExternalSubscriptions/dimensies | Nee |
> | ExternalSubscriptions/prognose | Nee |
> | ExternalSubscriptions/query | Nee |
> | Functies | Nee |
> | Query's uitvoeren | Nee |
> | inschrijving | Nee |
> | Reportconfigs | Nee |
> | Rapporten | Nee |
> | Instellingen | Nee |
> | showbackRules | Nee |
> | Weergaven | Nee |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aanvragen | Nee |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lidkoppelingen | Nee |
> | resourceProviders | Ja |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Functies | Ja |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ja |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | werk ruimten | Ja |
> | werk ruimten/dbWorkspaces | Nee |
> | werk ruimten/storageEncryption | Nee |
> | werk ruimten/virtualNetworkPeerings | Nee |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | catalogi | Ja |
> | datacatalogs | Ja |
> | datacatalogs/gegevens bronnen | Nee |
> | datacatalogs/gegevens bronnen/scans | Nee |
> | datacatalogs/gegevens bronnen/scans/gegevens sets | Nee |
> | datacatalogs/gegevens bronnen/scans/triggers | Nee |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataFactories | Ja |
> | dataFactories / diagnosticSettings | Nee |
> | dataFactories / metricDefinitions | Nee |
> | dataFactorySchema | Nee |
> | factory's | Ja |
> | fabrieken/integrationRuntimes | Nee |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/dataLakeStoreAccounts | Nee |
> | accounts/Storage accounts | Nee |
> | accounts/Storage accounts/containers | Nee |
> | accounts/transferAnalyticsUnits | Nee |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/eventGridFilters | Nee |
> | accounts/firewallRules | Nee |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |
> | Services/projecten | Ja |

## <a name="microsoftdatashare"></a>Micro soft. DataShare

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/shares | Nee |
> | accounts/shares/gegevens sets | Nee |
> | accounts/shares/uitnodigingen | Nee |
> | accounts/shares/providersharesubscriptions | Nee |
> | accounts/shares/synchronizationSettings | Nee |
> | accounts/sharesubscriptions | Nee |
> | accounts/sharesubscriptions/consumerSourceDataSets | Nee |
> | accounts/sharesubscriptions/datasetmappings | Nee |
> | accounts/sharesubscriptions/triggers | Nee |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Server | Ja |
> | servers/adviseurs | Nee |
> | servers/sleutels | Nee |
> | servers/privateEndpointConnectionProxies | Nee |
> | servers/privateEndpointConnections | Nee |
> | servers/privateLinkResources | Nee |
> | servers/queryTexts | Nee |
> | servers/recoverableServers | Nee |
> | servers/topQueryStatistics | Nee |
> | servers/virtualNetworkRules | Nee |
> | servers/waitStatistics | Nee |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Server | Ja |
> | servers/adviseurs | Nee |
> | servers/sleutels | Nee |
> | servers/privateEndpointConnectionProxies | Nee |
> | servers/privateEndpointConnections | Nee |
> | servers/privateLinkResources | Nee |
> | servers/queryTexts | Nee |
> | servers/recoverableServers | Nee |
> | servers/topQueryStatistics | Nee |
> | servers/virtualNetworkRules | Nee |
> | servers/waitStatistics | Nee |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | serverGroups | Ja |
> | Server | Ja |
> | servers/adviseurs | Nee |
> | servers/sleutels | Nee |
> | servers/privateEndpointConnectionProxies | Nee |
> | servers/privateEndpointConnections | Nee |
> | servers/privateLinkResources | Nee |
> | servers/queryTexts | Nee |
> | servers/recoverableServers | Nee |
> | servers/topQueryStatistics | Nee |
> | servers/virtualNetworkRules | Nee |
> | servers/waitStatistics | Nee |
> | serversv2 | Ja |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | artifactSources | Ja |
> | implementaties | Ja |
> | serviceTopologies | Ja |
> | serviceTopologies/Services | Ja |
> | serviceTopologies/Services/serviceUnits | Ja |
> | stappen | Ja |

## <a name="microsoftdesktopvirtualization"></a>Micro soft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationgroups | Ja |
> | applicationgroups/toepassingen | Nee |
> | applicationgroups/Bureau bladen | Nee |
> | applicationgroups / startmenuitems | Nee |
> | hostpools | Ja |
> | hostpools / sessionhosts | Nee |
> | hostpools / sessionhosts / usersessions | Nee |
> | hostpools / usersessions | Nee |
> | werk ruimten | Ja |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | ElasticPools | Ja |
> | ElasticPools / IotHubTenants | Ja |
> | ElasticPools/IotHubTenants/securitySettings | Nee |
> | IotHubs | Ja |
> | IotHubs/eventGridFilters | Nee |
> | IotHubs/securitySettings | Nee |
> | provisioningServices | Ja |
> | gebruik | Nee |

## <a name="microsoftdevops"></a>Micro soft. DevOps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | pijp lijnen | Ja |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | fungeren | Ja |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labcenters | Ja |
> | Labs | Ja |
> | Labs/omgevingen | Ja |
> | Labs-serviceRunners | Ja |
> | Labs-informatie | Ja |
> | schema's | Ja |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | databaseAccountNames | Nee |
> | databaseAccounts | Ja |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | domeinen | Ja |
> | domeinen/domainOwnershipIdentifiers | Nee |
> | generateSsoRequest | Nee |
> | topLevelDomains | Nee |
> | validateDomainRegistrationInformation | Nee |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | lcsprojects | Nee |
> | lcsprojects / clouddeployments | Nee |
> | lcsprojects/connectors | Nee |

## <a name="microsoftenterpriseknowledgegraph"></a>Micro soft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | domeinen | Ja |
> | domeinen/onderwerpen | Nee |
> | eventSubscriptions | Nee |
> | extensionTopics | Nee |
> | partnerNamespaces | Ja |
> | partnerNamespaces/eventChannels | Nee |
> | partnerRegistrations | Ja |
> | partnerTopics | Ja |
> | partnerTopics / eventSubscriptions | Nee |
> | systemTopics | Ja |
> | systemTopics / eventSubscriptions | Nee |
> | onderwerp | Ja |
> | topicTypes | Nee |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | naam ruimten | Ja |
> | naam ruimten/authorizationrules | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee |
> | naam ruimten/Event hubs | Nee |
> | naam ruimten/Event hubs/authorizationrules | Nee |
> | naam ruimten/Event hubs/consumergroups | Nee |
> | naam ruimten/networkrulesets | Nee |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | functies | Nee |
> | Providers | Nee |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | inschrijven | Nee |
> | galleryitems | Nee |
> | generateartifactaccessuri | Nee |
> | myareas | Nee |
> | myareas/gebieden | Nee |
> | myareas/gebieden/gebieden | Nee |
> | myareas/gebieden/gebieden/galleryitems | Nee |
> | myareas/areas/galleryitems | Nee |
> | myareas / galleryitems | Nee |
> | inschrijving | Nee |
> | resources | Nee |
> | retrieveresourcesbyid | Nee |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftguestconfiguration"></a>Micro soft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | autoManagedAccounts | Ja |
> | autoManagedVmConfigurationProfiles | Ja |
> | configurationProfileAssignments | Nee |
> | guestConfigurationAssignments | Nee |
> | software | Nee |
> | softwareUpdateProfile | Nee |
> | softwareUpdates | Nee |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hanaInstances | Ja |
> | sapMonitors | Ja |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedHSMs | Ja |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | clusters/toepassingen | Nee |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | services | Ja |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apparaten | Ja |
> | computers/uitbrei dingen | Ja |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dataManagers | Ja |

## <a name="microsofthydra"></a>Micro soft. Hydra

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | materialen | Ja |
> | networkScopes | Ja |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Functies | Ja |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appTemplates | Nee |
> | IoTApps | Ja |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Graph | Ja |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | deletedVaults | Nee |
> | hsmPools | Ja |
> | kluizen | Ja |
> | kluizen/accessPolicies | Nee |
> | kluizen/eventGridFilters | Nee |
> | kluizen/geheimen | Nee |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | clusters/attacheddatabaseconfigurations | Nee |
> | clusters/data bases | Nee |
> | clusters/data bases/dataConnections | Nee |
> | clusters/data bases/eventhubconnections | Nee |
> | clusters/data bases/principalassignments | Nee |
> | clusters/principalassignments | Nee |
> | clusters/sharedidentities | Nee |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | labaccounts | Ja |
> | gebruikers | Nee |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hostingEnvironments | Ja |
> | integrationAccounts | Ja |
> | integrationServiceEnvironments | Ja |
> | integrationServiceEnvironments/Beheerdeapi's | Ja |
> | isolatedEnvironments | Ja |
> | stroom | Ja |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | commitmentPlans | Ja |
> | webServices | Ja |
> | Workspaces | Ja |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | werk ruimten | Ja |
> | werk ruimten/reken bewerkingen | Nee |
> | werk ruimten/eventGridFilters | Nee |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | identiteit | Nee |
> | userAssignedIdentities | Ja |

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Nee |
> | registrationAssignments | Nee |
> | registrationDefinitions | Nee |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | getEntities | Nee |
> | managementGroups | Nee |
> | managementGroups/instellingen | Nee |
> | resources | Nee |
> | startTenantBackfill | Nee |
> | tenantBackfillStatus | Nee |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |
> | accounts/eventGridFilters | Nee |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | over | Nee |
> | offerTypes | Nee |
> | offerTypes/uitgevers | Nee |
> | offerTypes/uitgevers/aanbiedingen | Nee |
> | offerTypes/uitgevers/aanbiedingen/abonnementen | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/overeenkomsten | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/configuraties | Nee |
> | offerTypes/uitgevers/aanbiedingen/plannen/configuraties/importImage | Nee |
> | privategalleryitems | Nee |
> | privateStoreClient | Nee |
> | producten | Nee |
> | uitgevers | Nee |
> | uitgevers/aanbiedingen | Nee |
> | uitgevers/aanbiedingen/wijzigingen | Nee |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | classicDevServices | Ja |
> | updateCommunicationPreference | Nee |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | gesloten | Nee |
> | offertypes | Nee |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Media Services | Ja |
> | Media Services/accountFilters | Nee |
> | Media Services/assets | Nee |
> | Media Services/assets/assetFilters | Nee |
> | Media Services/contentKeyPolicies | Nee |
> | Media Services/eventGridFilters | Nee |
> | Media Services/liveEventOperations | Nee |
> | Media Services/liveEvents | Ja |
> | Media Services/liveEvents/liveOutputs | Nee |
> | Media Services/liveOutputOperations | Nee |
> | Media Services/mediaGraphs | Nee |
> | Media Services/streamingEndpointOperations | Nee |
> | Media Services/streamingEndpoints | Ja |
> | Media Services/streamingLocators | Nee |
> | Media Services/streamingPolicies | Nee |
> | Media Services/trans formaties | Nee |
> | Media Services/trans formaties/taken | Nee |

## <a name="microsoftmicroservices4spring"></a>Micro soft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | appClusters | Ja |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | assessmentProjects | Ja |
> | migrateprojects | Ja |
> | moveCollections | Ja |
> | projecten | Ja |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ja |
> | objectUnderstandingAccounts | Ja |
> | remoteRenderingAccounts | Ja |
> | spatialAnchorsAccounts | Ja |
> | surfaceReconstructionAccounts | Ja |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | netAppAccounts | Ja |
> | netAppAccounts / capacityPools | Ja |
> | netAppAccounts/capacityPools/volumes | Ja |
> | netAppAccounts/capacityPools/volumes/mountTargets | Ja |
> | netAppAccounts/capacityPools/volumes/moment opnamen | Ja |

## <a name="microsoftnotebooks"></a>Micro soft. notebooks

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | NotebookProxies | Nee |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationGateways | Ja |
> | applicationGatewayWebApplicationFirewallPolicies | Ja |
> | applicationSecurityGroups | Ja |
> | azureFirewallFqdnTags | Nee |
> | azureFirewalls | Ja |
> | bastionHosts | Ja |
> | bgpServiceCommunities | Nee |
> | inbel | Ja |
> | ddosCustomPolicies | Ja |
> | ddosProtectionPlans | Ja |
> | dnsOperationStatuses | Nee |
> | dnszones | Ja |
> | dnszones/A | Nee |
> | dnszones/AAAA | Nee |
> | dnszones/alle | Nee |
> | dnszones/CAA | Nee |
> | dnszones/CNAME | Nee |
> | dnszones/MX | Nee |
> | dnszones/NS | Nee |
> | dnszones/PTR | Nee |
> | dnszones/record sets | Nee |
> | dnszones/SOA | Nee |
> | dnszones/SRV | Nee |
> | dnszones/TXT | Nee |
> | expressRouteCircuits | Ja |
> | expressRouteCrossConnections | Ja |
> | expressRouteGateways | Ja |
> | expressRoutePorts | Ja |
> | expressRouteServiceProviders | Nee |
> | firewallPolicies | Ja |
> | frontdoors | Ja |
> | frontdoorWebApplicationFirewallManagedRuleSets | Nee |
> | frontdoorWebApplicationFirewallPolicies | Ja |
> | getDnsResourceReference | Nee |
> | internalNotify | Nee |
> | loadBalancers | Ja |
> | localNetworkGateways | Ja |
> | natGateways | Ja |
> | networkIntentPolicies | Ja |
> | networkInterfaces | Ja |
> | networkProfiles | Ja |
> | networkSecurityGroups | Ja |
> | networkWatchers | Ja |
> | networkWatchers / connectionMonitors | Ja |
> | networkWatchers/lenzen | Ja |
> | networkWatchers / pingMeshes | Ja |
> | p2sVpnGateways | Ja |
> | privateDnsOperationStatuses | Nee |
> | privateDnsZones | Ja |
> | privateDnsZones/A | Nee |
> | privateDnsZones/AAAA | Nee |
> | privateDnsZones/alle | Nee |
> | privateDnsZones/CNAME | Nee |
> | privateDnsZones/MX | Nee |
> | privateDnsZones/PTR | Nee |
> | privateDnsZones/SOA | Nee |
> | privateDnsZones/SRV | Nee |
> | privateDnsZones/TXT | Nee |
> | privateDnsZones / virtualNetworkLinks | Ja |
> | privateEndpoints | Ja |
> | privateLinkServices | Ja |
> | publicIPAddresses | Ja |
> | publicIPPrefixes | Ja |
> | routeFilters | Ja |
> | routeTables | Ja |
> | serviceEndpointPolicies | Ja |
> | trafficManagerGeographicHierarchies | Nee |
> | trafficmanagerprofiles | Ja |
> | trafficmanagerprofiles/heatMaps | Nee |
> | trafficManagerUserMetricsKeys | Nee |
> | virtualHubs | Ja |
> | virtualNetworkGateways | Ja |
> | virtualNetworks | Ja |
> | virtualNetworkTaps | Ja |
> | virtualWans | Ja |
> | vpnGateways | Ja |
> | vpnSites | Ja |
> | webApplicationFirewallPolicies | Ja |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | naam ruimten | Ja |
> | naam ruimten/notification hubs | Ja |

## <a name="microsoftobjectstore"></a>Micro soft. ObjectStore

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | osNamespaces | Ja |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | HyperVSites | Ja |
> | ImportSites | Ja |
> | ServerSites | Ja |
> | VMwareSites | Ja |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | clusters | Ja |
> | linkTargets | Nee |
> | storageInsightConfigs | Nee |
> | werk ruimten | Ja |
> | werk ruimten/dataExports | Nee |
> | werk ruimten/gegevens bronnen | Nee |
> | werk ruimten/linkedServices | Nee |
> | werk ruimten/privateEndpointConnectionProxies | Nee |
> | werk ruimten/privateEndpointConnections | Nee |
> | werk ruimten/privateLinkResources | Nee |
> | werk ruimten/query | Nee |
> | werk ruimten/scopedPrivateLinkProxies | Nee |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managementassociations | Nee |
> | managementconfigurations | Ja |
> | oplossingen | Ja |
> | Weergaven | Ja |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | legacyPeerings | Nee |
> | peerAsns | Nee |
> | Peerings | Ja |
> | peeringServiceCountries | Nee |
> | peeringServiceProviders | Nee |
> | peeringServices | Ja |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | policyEvents | Nee |
> | policyMetadata | Nee |
> | policyStates | Nee |
> | policyTrackedResources | Nee |
> | herstel | Nee |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | consoles | Nee |
> | Dash boards | Ja |
> | userSettings | Nee |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | workspaceCollections | Ja |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | beschikt | Ja |

## <a name="microsoftprojectbabylon"></a>Micro soft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Ja |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | backupProtectedItems | Nee |
> | kluizen | Ja |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | naam ruimten | Ja |
> | naam ruimten/authorizationrules | Nee |
> | naam ruimten/hybridconnections | Nee |
> | naam ruimten/hybridconnections/authorizationrules | Nee |
> | naam ruimten/wcfrelays | Nee |
> | naam ruimten/wcfrelays/authorizationrules | Nee |

## <a name="microsoftremoteapp"></a>Micro soft. RemoteApp

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | accounts | Nee |
> | reeksen | Ja |
> | verzamelingen/toepassingen | Nee |
> | verzamelingen/securityprincipals | Nee |
> | templateImages | Nee |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | query's | Ja |
> | resourceChangeDetails | Nee |
> | resourceChanges | Nee |
> | resources | Nee |
> | resourcesHistory | Nee |
> | subscriptionsStatus | Nee |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | availabilityStatuses | Nee |
> | childAvailabilityStatuses | Nee |
> | childResources | Nee |
> | emergingissues | Nee |
> | events | Nee |
> | impactedResources | Nee |
> | metagegevens | Nee |
> | meldingen | Nee |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | implementaties | Nee |
> | implementaties/bewerkingen | Nee |
> | deploymentScripts | Ja |
> | deploymentScripts/logboeken | Nee |
> | koppelen | Nee |
> | notifyResourceJobs | Nee |
> | Providers | Nee |
> | resourceGroups | Nee |
> | geabonneerd | Nee |
> | tenants | Nee |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | saasresources | Nee |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | resourceHealthMetadata | Nee |
> | searchServices | Ja |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Nee |
> | advancedThreatProtectionSettings | Nee |
> | waarschuwingen | Nee |
> | allowedConnections | Nee |
> | applicationWhitelistings | Nee |
> | assessmentMetadata | Nee |
> | evaluaties | Nee |
> | autoDismissAlertsRules | Nee |
> | automatisering | Ja |
> | AutoProvisioningSettings | Nee |
> | Ingebouwde strengste | Nee |
> | dataCollectionAgents | Nee |
> | deviceSecurityGroups | Nee |
> | discoveredSecuritySolutions | Nee |
> | externalSecuritySolutions | Nee |
> | InformationProtectionPolicies | Nee |
> | iotSecuritySolutions | Ja |
> | iotSecuritySolutions / analyticsModels | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Nee |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Nee |
> | jitNetworkAccessPolicies | Nee |
> | networkData | Nee |
> | restrictie | Nee |
> | prijzen | Nee |
> | regulatoryComplianceStandards | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Nee |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Nee |
> | securityContacts | Nee |
> | securitySolutions | Nee |
> | securitySolutionsReferenceData | Nee |
> | securityStatuses | Nee |
> | securityStatusesSummaries | Nee |
> | serverVulnerabilityAssessments | Nee |
> | instellingen | Nee |
> | subevaluaties | Nee |
> | taken | Nee |
> | topologieën | Nee |
> | workspaceSettings | Nee |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | aggregaties | Nee |
> | alertRules | Nee |
> | alertRuleTemplates | Nee |
> | Wijzer | Nee |
> | meldingen | Nee |
> | dataConnectors | Nee |
> | dataConnectorsCheckRequirements | Nee |
> | Rijg | Nee |
> | entityQueries | Nee |
> | Gevallen | Nee |
> | officeConsents | Nee |
> | instellingen | Nee |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | naam ruimten | Ja |
> | naam ruimten/authorizationrules | Nee |
> | naam ruimten/disasterrecoveryconfigs | Nee |
> | naam ruimten/eventgridfilters | Nee |
> | naam ruimten/networkrulesets | Nee |
> | naam ruimten/wacht rijen | Nee |
> | naam ruimten/wacht rijen/authorizationrules | Nee |
> | naam ruimten/onderwerpen | Nee |
> | naam ruimten/onderwerpen/authorizationrules | Nee |
> | naam ruimten/onderwerpen/abonnementen | Nee |
> | naam ruimten/onderwerpen/abonnementen/regels | Nee |
> | premiumMessagingRegions | Nee |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | clusters | Ja |
> | clusters/toepassingen | Nee |
> | containerGroups | Ja |
> | containerGroupSets | Ja |
> | edgeclusters | Ja |
> | edgeclusters/toepassingen | Nee |
> | managedclusters | Ja |
> | managedclusters / nodetypes | Nee |
> | netwerken | Ja |
> | secretstores | Ja |
> | secretstores/certificaten | Nee |
> | secretstores/geheimen | Nee |
> | volumes | Ja |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | toepassingen | Ja |
> | containerGroups | Ja |
> | gateways | Ja |
> | netwerken | Ja |
> | geheimen | Ja |
> | volumes | Ja |

## <a name="microsoftservices"></a>Micro soft. Services

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | providerRegistrations | Nee |
> | providerRegistrations / resourceTypeRegistrations | Nee |
> | implementaties | Ja |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SignalR | Ja |
> | Signaal sterkte/eventGridFilters | Nee |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ja |

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | hybridUseBenefits | Nee |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | applicationDefinitions | Ja |
> | toepassingen | Ja |
> | jitRequests | Ja |

## <a name="microsoftspoolservice"></a>Micro soft. SpoolService

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | registeredSubscriptions | Nee |
> | afdruk wachtrij geplaatst | Ja |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | managedInstances | Ja |
> | managedInstances/data bases | Ja |
> | managedInstances/data bases/backupShortTermRetentionPolicies | Nee |
> | managedInstances/data bases/schema's/tabellen/kolommen/sensitivityLabels | Nee |
> | managedInstances/data bases/vulnerabilityAssessments | Nee |
> | managedInstances/data bases/vulnerabilityAssessments/Rules/basis lijnen | Nee |
> | managedInstances / encryptionProtector | Nee |
> | managedInstances/sleutels | Nee |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Nee |
> | managedInstances / vulnerabilityAssessments | Nee |
> | Server | Ja |
> | servers/beheerders | Nee |
> | servers/communicationLinks | Nee |
> | servers/data bases | Ja |
> | servers/encryptionProtector | Nee |
> | servers/firewallRules | Nee |
> | servers/sleutels | Nee |
> | servers/restorableDroppedDatabases | Nee |
> | servers/serviceobjectives | Nee |
> | servers/tdeCertificates | Nee |
> | virtualClusters | Nee |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ja |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Nee |
> | SqlVirtualMachines | Ja |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageAccounts | Ja |
> | Storage accounts/blobServices | Nee |
> | Storage accounts/fileServices | Nee |
> | Storage accounts/queueServices | Nee |
> | Storage accounts/Services | Nee |
> | Storage accounts/Services/metricDefinitions | Nee |
> | Storage accounts/tableServices | Nee |
> | gebruik | Nee |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | caches | Ja |
> | caches/storageTargets | Nee |
> | usageModels | Nee |

## <a name="microsoftstoragereplication"></a>Micro soft. StorageReplication

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | replicationGroups | Nee |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServer | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices/werk stromen | Nee |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServer | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices/werk stromen | Nee |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | storageSyncServices | Ja |
> | storageSyncServices/registeredServer | Nee |
> | storageSyncServices / syncGroups | Nee |
> | storageSyncServices / syncGroups / cloudEndpoints | Nee |
> | storageSyncServices / syncGroups / serverEndpoints | Nee |
> | storageSyncServices/werk stromen | Nee |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | leider | Ja |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | streamingjobs | Ja |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | Annuleren | Nee |
> | CreateSubscription | Nee |
> | kunt | Nee |
> | domeinnaam | Nee |
> | SubscriptionDefinitions | Nee |
> | SubscriptionOperations | Nee |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | verschillend | Ja |
> | omgevingen/accessPolicies | Nee |
> | omgevingen/eventsources | Ja |
> | omgevingen/referenceDataSets | Ja |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ja |
> | dedicatedCloudServices | Ja |
> | Informatie | Ja |

## <a name="microsoftvnfmanager"></a>Micro soft. VnfManager

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apparaten | Ja |
> | registeredSubscriptions | Nee |
> | crediteur | Nee |
> | leveranciers/sku's | Nee |
> | leveranciers/vnfs | Nee |
> | vnfs | Ja |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | apiManagementAccounts | Nee |
> | apiManagementAccounts / apiAcls | Nee |
> | apiManagementAccounts/api's | Nee |
> | apiManagementAccounts/api's/apiAcls | Nee |
> | apiManagementAccounts/api's/connectionAcls | Nee |
> | apiManagementAccounts/api's/Connections | Nee |
> | apiManagementAccounts/api's/Connections/connectionAcls | Nee |
> | apiManagementAccounts/api's/localizedDefinitions | Nee |
> | apiManagementAccounts / connectionAcls | Nee |
> | apiManagementAccounts/verbindingen | Nee |
> | billingMeters | Nee |
> | bewijzen | Ja |
> | connectionGateways | Ja |
> | inbel | Ja |
> | customApis | Ja |
> | deletedSites | Nee |
> | hostingEnvironments | Ja |
> | hostingEnvironments / eventGridFilters | Nee |
> | hostingEnvironments / multiRolePools | Nee |
> | hostingEnvironments / workerPools | Nee |
> | publishingUsers | Nee |
> | vereisten | Nee |
> | resourceHealthMetadata | Nee |
> | Runtimes | Nee |
> | Server farms | Ja |
> | Server farms/eventGridFilters | Nee |
> | sites | Ja |
> | sites/configuratie  | Nee |
> | sites/eventGridFilters | Nee |
> | sites/hostNameBindings | Nee |
> | sites/networkConfig | Nee |
> | sites/premieraddons | Ja |
> | sites/sleuven | Ja |
> | sites/sleuven/eventGridFilters | Nee |
> | sites/sleuven/hostNameBindings | Nee |
> | sites/sleuven/networkConfig | Nee |
> | sourceControls | Nee |
> | staticSites | Ja |
> | subelementid | Nee |
> | verifyHostingEnvironmentVnet | Nee |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | diagnosticSettings | Nee |
> | diagnosticSettingsCategories | Nee |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | DeviceServices | Ja |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Resourcetype | Modus voor volledige verwijdering |
> | ------------- | ----------- |
> | materialen | Nee |
> | componentsSummary | Nee |
> | monitorInstances | Nee |
> | monitorInstancesSummary | Nee |
> | monitors | Nee |
> | notificationSettings | Nee |

## <a name="next-steps"></a>Volgende stappen

Down load [complete-mode-deletion. CSV](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv)om dezelfde gegevens op te halen als een bestand met door komma's gescheiden waarden.
