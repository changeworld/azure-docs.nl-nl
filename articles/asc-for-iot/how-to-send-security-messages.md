---
title: Uw beveiligings berichten verzenden naar Azure Security Center voor IoT | Microsoft Docs
description: Meer informatie over het verzenden van uw beveiligings berichten met Azure Security Center voor IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 8bbbd8248c7418b667e34389cb47bd3f6b4f06ab
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963815"
---
# <a name="send-security-messages-sdk"></a>SDK voor beveiligings berichten verzenden

In deze hand leiding vindt u informatie over de Azure Security Center voor IoT-service mogelijkheden wanneer u ervoor kiest om de beveiligings berichten van uw apparaat te verzamelen en te verzenden zonder een Azure Security Center voor IoT-agent te gebruiken en wordt uitgelegd hoe u dit doet.  

In deze handleiding leert u het volgende: 
> [!div class="checklist"]
> * Beveiligings berichten verzenden met behulp van de Azure IoT C-SDK
> * Beveiligings berichten verzenden met behulp van C# de Azure IOT SDK
> * Beveiligings berichten verzenden met behulp van de Azure IoT python SDK
> * Beveiligings berichten verzenden met behulp van de Azure IoT node. js SDK
> * Beveiligings berichten verzenden met behulp van de Azure IoT Java SDK


## <a name="azure-security-center-for-iot-capabilities"></a>Azure Security Center voor IoT-mogelijkheden

Azure Security Center voor IoT kan elk soort beveiligings bericht gegevens verwerken en analyseren, zolang de gegevens die worden verzonden, voldoen aan de [Azure Security Center voor IOT-schema](https://aka.ms/iot-security-schemas) en het bericht is ingesteld als een beveiligings bericht.

## <a name="security-message"></a>Beveiligings bericht

Azure Security Center voor IoT definieert een beveiligings bericht aan de hand van de volgende criteria:
- Als het bericht is verzonden met de Azure IoT SDK
- Als het bericht voldoet aan het schema voor het [beveiligings bericht](https://aka.ms/iot-security-schemas)
- Als het bericht is ingesteld als een beveiligings bericht voordat het wordt verzonden

Elk beveiligings bericht bevat de meta gegevens van de afzender, zoals `AgentId`, `AgentVersion`, `MessageSchemaVersion` en een lijst met beveiligings gebeurtenissen.
Het schema definieert de geldige en vereiste eigenschappen van het beveiligings bericht, met inbegrip van de typen gebeurtenissen.

>[!Note]
> Berichten die worden verzonden die niet voldoen aan het schema, worden genegeerd. Zorg ervoor dat u het schema verifieert voordat u het verzenden van gegevens start, omdat genegeerde berichten momenteel niet zijn opgeslagen. 

>[!Note]
> Berichten die worden verzonden die niet zijn ingesteld als een beveiligings bericht met behulp van de Azure IoT SDK, worden niet doorgestuurd naar de Azure Security Center voor IoT-pijp lijn.

## <a name="valid-message-example"></a>Geldig bericht voorbeeld

In het onderstaande voor beeld ziet u een geldig beveiligings bericht object. Het voor beeld bevat de meta gegevens van het bericht en één `ProcessCreate` beveiligings gebeurtenis.

Eenmaal ingesteld als een beveiligings bericht en verzonden, wordt dit bericht verwerkt door Azure Security Center voor IoT.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Beveiligings berichten verzenden 

Beveiligings berichten verzenden *zonder* Azure Security Center te gebruiken voor IOT-agent, met behulp van de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), [Azure C# IoT-apparaat-SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IOT node. js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IOT python SDK](https://github.com/Azure/azure-iot-sdk-python)of [Azure IOT Java SDK](https://github.com/Azure/azure-iot-sdk-java).

Als u de apparaatgegevens van uw apparaten wilt verzenden voor verwerking door Azure Security Center voor IoT, gebruikt u een van de volgende Api's om berichten te markeren voor juiste route ring naar Azure Security Center voor IoT-verwerkings pijplijn. 

Alle gegevens die worden verzonden, zelfs als deze zijn gemarkeerd met de juiste header, moeten ook voldoen aan de [Azure Security Center voor IOT-berichten schema](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Beveiligings bericht-API verzenden 

De API voor het **verzenden van beveiligings berichten** is momenteel beschikbaar C#in C en, Python, node. js en Java.  

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {
 
    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
 
    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);
 
    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }
 
    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }
 
cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }
 
    return success;
}
 
static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }
 
    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```
#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```
#### <a name="nodejs-api"></a>Node. js-API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python-API

Als u de python-API wilt gebruiken, moet u het pakket [Azure-IOT-Device](https://pypi.org/project/azure-iot-device/)installeren.

Wanneer u de python-API gebruikt, kunt u het beveiligings bericht via de module of via het apparaat verzenden met behulp van het unieke apparaat of de module connection string. Gebruik, als u het volgende python-script voorbeeld gebruikt, met een apparaat, **IoTHubDeviceClient**en met een module, **IoTHubModuleClient**. 

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java-API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```


## <a name="next-steps"></a>Volgende stappen
- Lees het [overzicht](overview.md) van de Azure Security Center voor IOT-service
- Meer informatie over Azure Security Center voor IoT- [architectuur](architecture.md)
- De [service](quickstart-onboard-iot-hub.md) inschakelen
- Lees de [Veelgestelde vragen](resources-frequently-asked-questions.md)
- Meer informatie over het openen van [onbewerkte beveiligings gegevens](how-to-security-data-access.md)
- Meer informatie over [aanbevelingen](concept-recommendations.md)
- Meer informatie over [waarschuwingen](concept-security-alerts.md)
