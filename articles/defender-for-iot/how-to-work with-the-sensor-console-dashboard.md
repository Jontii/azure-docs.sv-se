---
title: Arbeta med sensor konsolens instrument panel
description: På instrument panelen kan du snabbt se nätverkets säkerhets status. Det ger en översikt över hot på hela systemet på en tids linje tillsammans med information om relaterade enheter.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 735b1ce4391598d05a1bf0b4486503092f4de37d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842920"
---
# <a name="the-dashboard"></a>Instrument panelen

På instrument panelen kan du snabbt se nätverkets säkerhets status. Det ger en översikt över hot på hela systemet på en tids linje tillsammans med information om relaterade enheter, inklusive:

- Aviseringar på olika allvarlighets grader:

- Kritiskt

- Större

- Mindre

- Varningar

- De två mätarna i mitten av sidan visar de paket per sekund (PPS) och ej accepterade varningar (UA). **PPS** är antalet paket som bekräftats av systemet per sekund. **UA** är antalet aviseringar som ännu inte har bekräftats.

- Lista över ej accepterade aviseringar med deras beskrivning.

- Tids linje med aviserings beskrivningen.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Instrumentpanel":::

## <a name="viewing-the-latest-alerts"></a>Visa de senaste aviseringarna

UA-mätaren i mitten av sidan visar antalet sådana aviseringar. Om du vill visa en lista över aviseringar väljer du **fler aviseringar** längst ned på sidan instrument panel eller väljer **aviseringar** på sido menyn.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Aviseringar som inte godkänts":::

## <a name="status-boxes"></a>Status rutor

Varje status ruta beskrivs i det här avsnittet.

| Status ruta och mätare | Beskrivning |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Kritiska aviseringar"::: | **Kritiska aviseringar** – rutan längst upp i mitten av sidan visar antalet kritiska aviseringar. Markera den här rutan om du vill visa beskrivningar av aviseringarna på tids linjen och i listan under mätare, om sådana finns.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Huvud varningar"::: | **Större aviseringar** – rutan längst upp till höger på sidan visar antalet huvud varningar. Markera den här rutan om du vill visa beskrivningar av aviseringarna på tids linjen och i listan under mätare, om sådana finns.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Mindre aviseringar"::: | **Mindre aviseringar** – rutan längst ned till vänster på sidan anger antalet mindre aviseringar. Markera den här rutan om du vill visa beskrivningar av aviseringarna på tids linjen och i listan under mätare, om sådana finns.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Varnings aviseringar"::: | **Varnings aviseringar** – rutan längst ned på sidan visar antalet varnings aviseringar. Markera den här rutan om du vill visa beskrivningar av aviseringarna på tids linjen och i listan under mätare, om sådana finns.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Alla aviseringar** – rutan längst ned till höger på sidan visar det totala antalet kritiska varningar, större, små och varningar. Markera den här rutan om du vill visa beskrivningar av aviseringarna på tids linjen och i listan under mätare, om sådana finns. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Paket per sekund"::: | **Paket per sekund (PPS)** – PPS-måttet är en indikator för nätverkets prestanda. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Ej accepterade händelser (UA)"::: | Ej **accepterade händelser** – det här måttet anger antalet ej accepterade händelser.

## <a name="using-the-timeline"></a>Använda tids linjen

Aviseringarna visas utmed en lodrät tids linje som innehåller information om datum och tid.

Tids linjen visar grafiskt:

- Kritiska aviseringar

- Huvud varningar

- Mindre aviseringar

- Varnings aviseringar

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Tids linje diagram":::

## <a name="viewing-alerts"></a>Visa aviseringar

Välj nedåtpilen **V** längst ned i varnings rutan för att visa information om aviserings post och enheter.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Information om aviserings post och enheter":::

- Välj enheten eller **Visa enheter** för att visa den fysiska läges kartan. De enheter som omfattas är markerade.

- Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: för att exportera en CSV-fil om aviseringen.

- Endast administratörer och säkerhetsanalytiker – Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="Bekräfta alla"::: för att **Bekräfta alla** associerade aviseringar.

- Markera aviserings posten om du vill visa typen och beskrivningen av aviseringen:

- Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::för att ladda ned en varnings rapport som en PDF-fil.

- Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="Fäst":::för att fästa eller ta bort aviseringen.

- Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Hämta"::: för att undersöka aviseringen genom att hämta pcap-filen som innehåller en analys av nätverks protokoll.

- Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="moln"::: för att ladda ned en filtrerad pcap-fil som bara innehåller de aviserings relevanta paketen och därmed minska storleken på utdatafilen och göra en mer fokuserad analys. Du kan visa den med hjälp av [wireshark](https://www.wireshark.org/).

- Välj :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="navigering"::: för att navigera till händelse tids linjen vid tidpunkten för den begärda aviseringen.

- Administratörer och säkerhetsanalytiker endast – ändra statusen för aviseringen från ej godkänd till godkänd. Välj Läs om du vill godkänna identifierad aktivitet.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Obehörig Internet anslutning upptäckt":::

## <a name="see-also"></a>Se även

[Arbeta med aviseringar på sensorn](how-to-work-with-alerts-on-your-sensor.md)
