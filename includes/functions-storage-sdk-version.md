---
title: bestand opnemen
description: bestand opnemen
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 6a37850eb6536c5399d63144e60ea210fbc194d8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198412"
---
#### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-versie in functions 1. x

In functions 1. x gebruiken de opslag triggers en bindingen versie 7.2.1 van het Azure Storage SDK-pakket ([WindowsAzure. Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet). Als u verwijst naar een andere versie van de opslag-SDK en u koppelt aan een opslag-SDK in uw functie handtekening, kan de functions-runtime rapporteren dat deze niet kan worden gebonden aan dat type. De oplossing is te controleren of uw project verwijst naar [WindowsAzure. Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
