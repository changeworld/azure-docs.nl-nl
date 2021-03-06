---
title: StringToArray in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToArray in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 18acbd94fa3d717fc20b9e1020b9bf7c6db7744d
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302913"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Retourneert een expressie die is vertaald naar een matrix. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een JSON-matrix expressie. 
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een matrix expressie of is niet gedefinieerd. 
  
## <a name="remarks"></a>Opmerkingen
  Geneste teken reeks waarden moeten worden geschreven met dubbele aanhalings tekens die een geldige JSON moeten hebben. Zie [JSON.org](https://json.org/) voor meer informatie over de JSON-indeling.
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe `StringToArray` zich gedraagt in verschillende typen. 
  
 Hier volgen enkele voor beelden met geldige invoer.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Hier volgt de resultatenset.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Hier volgt een voor beeld van ongeldige invoer. 
   
 Enkele aanhalings tekens in de matrix zijn geen geldige JSON.
Hoewel ze wel geldig zijn binnen een query, worden ze niet geparseerd naar geldige matrices. Teken reeksen binnen de matrix teken reeks moeten worden voorafgegaan door ' [\\'\\'] ' of de omliggende aanhaling moet één ' ['] ' zijn.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Hier volgt de resultatenset.

```json
[{}]
```

Hier volgen enkele voor beelden van ongeldige invoer.
   
 De door gegeven expressie wordt geparseerd als een JSON-matrix. de volgende evaluaties zijn niet van het type matrix en daarom wordt ongedefinieerd geretourneerd.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Hier volgt de resultatenset.

```json
[{}]
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
