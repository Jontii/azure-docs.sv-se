---
title: Azure Monitor för SAP-lösnings leverantörer | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Monitor för SAP Solutions-leverantörer.
author: rdeltcheva
ms.service: virtual-machines
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 235572cc4d697e7488765c464b12f9349c1e012b
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91994168"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor för SAP Solutions-providers (för hands version)

## <a name="overview"></a>Översikt  

I samband med Azure Monitor för SAP-lösningar refererar en *providertyp* till en speciell *Provider*. Till exempel *SAP HANA*, som har kon figurer ATS för en speciell komponent i SAP-landskap, t. ex. SAP HANA Database. En provider innehåller anslutnings informationen för motsvarande komponent och hjälper till att samla in telemetridata från den komponenten. En Azure Monitor för SAP-lösningar (även kallat SAP Monitor-resurs) kan konfigureras med flera leverantörer av samma providertyp eller flera providers för flera typer av leverantörer.
   
Kunderna kan välja att konfigurera olika typer av leverantörer för att aktivera insamling av data från motsvarande komponenter i deras SAP-landskap. Kunder kan till exempel konfigurera en provider för SAP HANA typ av Provider, en annan provider för kluster leverantör med hög tillgänglighet och så vidare.  

Kunder kan också välja att konfigurera flera leverantörer av en speciell typ av Provider för att återanvända samma SAP Monitor-resurs och tillhör ande hanterade grupp. Luta mer om hanterad resurs grupp. För offentlig för hands version stöds följande typer av providers:   
- SAP HANA
- Kluster med hög tillgänglighet
- Microsoft SQL Server

![Azure Monitor för SAP Solutions-leverantörer](./media/azure-monitor-sap/azure-monitor-providers.png)

Kunderna rekommenderas att konfigurera minst en provider från de tillgängliga leverantörs typerna när du distribuerar SAP Monitor-resursen. Genom att konfigurera en leverantör initierar kunden data insamling från motsvarande komponent som providern är konfigurerad för.   

Om kunderna inte konfigurerar några providrar vid distribution av SAP Monitor-resursen, kommer inga telemetridata att samlas in, även om SAP Monitor-resursen kommer att distribueras. Kunder har möjlighet att lägga till providrar efter distribution via SAP Monitor-resurs inom Azure Portal. Kunder kan lägga till eller ta bort providrar från SAP Monitor-resursen när som helst.

> [!Tip]
> Om du vill att Microsoft ska implementera en speciell Provider måste du lämna feedback via länken i slutet av det här dokumentet eller kontakta ditt konto team.  

## <a name="provider-type-sap-hana"></a>Typ av Provider SAP HANA

Kunder kan konfigurera en eller flera providers av leverantörs typ *SAP HANA* för att aktivera data insamling från SAP HANA-databasen. SAP HANA-providern ansluter till SAP HANA-databasen via SQL-port, hämtar telemetridata från databasen och skickar den till Log Analytics-arbetsytan i kund prenumerationen. SAP HANA-providern samlar in data var 1 minut från SAP HANA-databasen.  

I en offentlig för hands version kan kunderna se följande data med SAP HANA provider: underliggande infrastruktur användning, SAP HANA värd status, SAP HANA systemreplikering och SAP HANA data för att säkerhetskopiera telemetri. Om du vill konfigurera SAP HANA-providern måste värd-IP-adress, HANA SQL-portnummer och SYSTEMDB användar namn och lösen ord anges. Kunder rekommenderas att konfigurera SAP HANA-providern mot SYSTEMDB, men ytterligare providers kan konfigureras mot andra databas klienter.

![Azure Monitor för SAP Solutions-providers – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-high-availability-cluster"></a>Typ av leverantörs kluster med hög tillgänglighet
Kunder kan konfigurera en eller flera leverantörer av leverantörs typ *kluster med hög tillgänglighet* för att aktivera data insamling från pacemaker-kluster i SAP-landskapet. Kluster leverantören med hög tillgänglighet ansluter till pacemaker, använder [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) slut punkt, hämtar telemetridata från databasen och skickar den till Log Analytics arbets yta i kund prenumerationen. Kluster leverantören med hög tillgänglighet samlar in data var 60 sekund från pacemaker.  

I en offentlig för hands version kan kunderna se följande data med kluster leverantör med hög tillgänglighet:   
 - Kluster status som visas som sammanslagning av nod-och resurs status 
 - [andra](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor för SAP Solutions-leverantörer – kluster med hög tillgänglighet](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Det finns två huvudsakliga steg för att konfigurera en kluster leverantör med hög tillgänglighet: 
1. Installera [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) i *varje* nod i pacemaker-kluster 
    - Kunder kan använda Azure Automation skript för att distribuera kluster med hög tillgänglighet. Skripten kommer att installera [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) på varje klusternod.  
    - eller kunder kan utföra manuell installation genom att följa anvisningarna på [den här sidan](https://github.com/ClusterLabs/ha_cluster_exporter) 
2. Konfigurera kluster leverantör med hög tillgänglighet i *varje* nod i pacemaker-kluster  
  För att konfigurera kluster leverantören med hög tillgänglighet krävs Prometheus-URL, kluster namn, värdnamn och system-ID.   
  Kunder rekommenderas att konfigurera en provider per klusternod.   

## <a name="provider-type-microsoft-sql-server"></a>Typ av Provider Microsoft SQL Server

Kunder kan konfigurera en eller flera providers av leverantörs typ *Microsoft SQL Server* för att aktivera data insamling från [SQL Server på virtuella datorer](https://azure.microsoft.com/services/virtual-machines/sql-server/). SQL Server providern ansluter till Microsoft SQL Server över SQL-porten hämtar telemetridata från databasen och skickar dem till arbets ytan Log Analytics i kund prenumerationen. SQL Server måste konfigureras för SQL-autentisering och en SQL Server inloggning, med SAP DB som standard databas för providern, måste skapas. SQL Server-Provider samlar in data mellan var 60: e sekund i varje timme från SQL Server.  

I en offentlig för hands version kan kunderna se följande data med SQL Server provider: underliggande infrastruktur användning, översta SQL-uttryck, högsta största tabell, problem som registrerats i SQL Server fel loggar, blockera processer och andra.  

För att kunna konfigurera Microsoft SQL Server-Provider krävs ID för SAP-system, värd-IP-adress, SQL Server port nummer samt SQL Server inloggnings namn och lösen ord.

![Azure Monitor för SAP Solutions-leverantörer – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="next-steps"></a>Nästa steg

- Skapa din första Azure Monitor för SAP-lösnings resurs.
- Har du frågor om Azure Monitor för SAP-lösningar? Läs avsnittet med [vanliga frågor och svar](./azure-monitor-faq.md)
