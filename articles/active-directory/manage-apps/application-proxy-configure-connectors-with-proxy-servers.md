---
title: Arbeta med befintliga lokala proxyservrar och Azure Active Directory
description: Beskriver hur du arbetar med befintliga lokala proxyservrar med Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 4c50e881fd6b7dda5c609a4ac6492d77fff1b537
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2020
ms.locfileid: "92208013"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Arbeta med befintliga lokala proxyservrar

Den här artikeln beskriver hur du konfigurerar Azure Active Directory (Azure AD) Application Proxy-kopplingar så att de fungerar med utgående proxyservrar. Den är avsedd för kunder med nätverksmiljöer som har befintliga proxyservrar.

Vi börjar med att titta på de här huvudsakliga distributions scenarierna:

* Konfigurera anslutningar för att kringgå dina lokala utgående proxyservrar.
* Konfigurera anslutningar för att använda en utgående proxy för att få åtkomst till Azure AD-programproxy.
* Konfigurera med hjälp av en proxy mellan anslutnings-och backend-programmet.

Mer information om hur anslutningsappar fungerar finns i avsnittet om att [förstå anslutningsappar för Azure AD-programproxy](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Kringgå utgående proxyservrar

Anslutningsappar har underliggande OS-komponenter som gör att utgående begäranden. De här komponenterna försöker automatiskt hitta en proxyserver i nätverket med hjälp av Web Proxy Auto-Discovery (WPAD).

OS-komponenterna försöker hitta en proxyserver genom att utföra en DNS-sökning efter wpad.domänsuffix. Om sökningen matchar i DNS görs en HTTP-begäran till IP-adressen för wpad.dat. Den här begäran blir proxykonfigurationsskriptet i din miljö. Anslutningsappen använder det här skriptet för att välja en utgående proxyserver. Anslutningsappens trafik kommer dock kanske inte igenom på grund av ytterligare konfigurationsinställningar som behövs på proxyn.

Du kan konfigurera anslutningsappen till att kringgå den lokala proxyn för att säkerställa att den använder direktanslutning till Azure-tjänsterna. Vi rekommenderar den här metoden förutsatt att din nätverksprincip tillåter det, eftersom det innebär att det blir en färre konfiguration att underhålla.

Om du vill inaktivera utgående proxy-användning för anslutningen redigerar du Connector\ApplicationProxyConnectorService.exe.config filen C:\Program Files\Microsoft AAD App proxy och lägger till avsnittet *system.net* som visas i följande kod exempel:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

För att säkerställa att tjänsten Connector Updater också kringgå proxyn, gör en liknande ändring i ApplicationProxyConnectorUpdaterService.exe.configs filen. Den här filen finns i C:\Program Files\Microsoft AAD App proxy Connector Updater.

Se till att göra kopior av originalfilerna i fall du behöver återställa till de standardmässiga .config-filerna.

## <a name="use-the-outbound-proxy-server"></a>Använd den utgående proxyservern

Vissa miljöer kräver all utgående trafik för att gå via en utgående proxy, utan undantag. Det innebär att det inte finns något alternativ att kringgå proxyn.

Du kan konfigurera anslutnings trafiken så att den går genom den utgående proxyn, som du ser i följande diagram:

 ![Konfigurera kopplings trafik för att gå via en utgående proxy till Azure AD-programproxy](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

På grund av att endast utgående trafik behövs behöver du inte konfigurera inkommande åtkomst genom brand väggarna.

> [!NOTE]
> Application Proxy stöder inte autentisering till andra proxyservrar. Anslutnings-/uppdaterings kontots nätverks tjänst konton ska kunna ansluta till proxyn utan att anropas för autentisering.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Steg 1: konfigurera anslutningen och relaterade tjänster att gå via den utgående proxyn

Om WPAD har Aktiver ATS i miljön och kon figurer ATS korrekt identifierar anslutnings tjänsten automatiskt den utgående proxyservern och försöker använda den. Du kan dock uttryckligen konfigurera anslutningen att gå via en utgående proxy.

Det gör du genom att redigera Connector\ApplicationProxyConnectorService.exe.config-filen C:\Program\Microsoft AAD App proxy och lägga till avsnittet *system.net* som visas i det här kod exemplet. Ändra *proxyserver: 8080* för att återspegla namnet på den lokala proxyservern eller IP-adressen och porten som den lyssnar på. Värdet måste ha prefixet http://även om du använder en IP-adress.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Konfigurera sedan Connector Updater-tjänsten så att den använder proxyn genom att göra en liknande ändring i mappen C:\Program Files\Microsoft AAD App proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config-filen.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Steg 2: Konfigurera proxyservern för att tillåta trafik från anslutningen och relaterade tjänster att flöda genom

Det finns fyra aspekter att tänka på vid utgående proxy:

* Utgående proxy-regler
* Proxyautentisering
* Proxy-portar
* TLS-kontroll

#### <a name="proxy-outbound-rules"></a>Utgående proxy-regler

Tillåt åtkomst till följande webbadresser:

| URL | Hur den används |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation mellan anslutningsprogrammet och molntjänsten för programproxy |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>www.d-trust.net<br>root-c3-ca2-2009.ocsp.d-trust.net<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | Anslutnings tjänsten använder dessa URL: er för att verifiera certifikat. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com <br> *. microsoftonline-p.com<br>*. msauth.net <br> *. msauthimages.net<br>*. msecnd.net <br> *. msftauth.net<br>*. msftauthimages.net <br> *. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com:80 | Anslutningsprogrammet använder dessa webbadresser under registreringen. |

Om din brand vägg eller proxy låter dig konfigurera listor över tillåtna DNS-listor kan du tillåta anslutningar till \* . msappproxy.net och \* . ServiceBus.Windows.net.

Om du inte kan tillåta anslutning av FQDN och behöver ange IP-adressintervall i stället, använder du följande alternativ:

* Tillåt anslutningen utgående åtkomst till alla destinationer.
* Tillåt anslutningen utgående åtkomst till alla Azure datacenter IP-intervall. Utmaningen med att använda listan över IP-intervall för Azure-datacenter är att den uppdateras varje vecka. Du måste placera en process för att se till att dina åtkomst regler uppdateras i enlighet med detta. Om du bara använder en delmängd av IP-adresserna kan konfigurationen brytas. Hämta de senaste IP-intervallen för Azure Data Center genom att gå till [https://download.microsoft.com](https://download.microsoft.com) och söka efter "Azure IP-intervall och service märken". Se till att välja det relevanta molnet. Till exempel kan de offentliga molnets IP-intervall hittas med "Azure IP-intervall och service märken – offentligt moln". Du hittar det amerikanska goverment-molnet genom att söka efter "Azure IP-intervall och service-Taggar – US goverment Cloud".

#### <a name="proxy-authentication"></a>Proxyautentisering

Proxyautentisering stöds inte för närvarande. Vår nuvarande rekommendation är att tillåta anslutningen anonym åtkomst till Internet-destinationer.

#### <a name="proxy-ports"></a>Proxy-portar

Anslutningen gör utgående TLS-baserade anslutningar med hjälp av metoden CONNECT. Den här metoden ställer in en tunnel genom utgående proxy. Konfigurera proxyservern för att tillåta tunnel trafik till portarna 443 och 80.

> [!NOTE]
> När Service Bus körs över HTTPS används port 443. Service Bus försöker som standard direkt TCP-anslutningar och går tillbaka till HTTPS endast om direkta anslutningar Miss lyckas.

#### <a name="tls-inspection"></a>TLS-kontroll

Använd inte TLS-inspektion för anslutnings trafiken, eftersom det orsakar problem med anslutnings trafiken. Anslutningen använder ett certifikat för att autentisera till Application Proxy-tjänsten och certifikatet kan gå förlorat under TLS-kontrollen.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Konfigurera med hjälp av en proxy mellan anslutnings-och Server dels programmet
Att använda en vidarebefordrande proxy för kommunikationen mot backend-appen kan vara ett särskilt krav i vissa miljöer.
Aktivera detta genom att följa nästa steg:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Steg 1: Lägg till det register värde som krävs på servern
1. Om du vill aktivera med standardproxyn lägger du till följande register värde (DWORD) i `UseDefaultProxyForBackendRequests = 1` register nyckeln för anslutnings konfigurationen i "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Steg 2: Konfigurera proxyservern manuellt med kommandot netsh
1.  Aktivera grup principen gör proxyinställningar per dator. Detta finns i: Computer Datorkonfiguration\principer\administrativa \ Mallar\windows-komponenter\Internet Explorer. Detta måste anges i stället för att den här principen har angetts till per användare.
2.  Kör `gpupdate /force` på servern eller starta om servern för att säkerställa att den använder de uppdaterade grup princip inställningarna.
3.  Starta en upphöjd kommando tolk med administratörs behörighet och ange `control inetcpl.cpl` .
4.  Konfigurera de proxyinställningar som krävs. 

De här inställningarna gör att anslutningen använder samma vidarebefordrande proxy för kommunikationen till Azure och Server dels programmet. Om anslutningen till Azure-kommunikationen inte kräver någon vidarebefordrande proxy eller en annan vidarebefordran proxy kan du konfigurera detta med att ändra filen ApplicationProxyConnectorService.exe.config enligt beskrivningen i avsnitten kringgå utgående proxyservrar eller använda den utgående proxyservern.

> [!NOTE]
> Det finns olika sätt att konfigurera Internet-proxyn i operativ systemet. Proxyinställningar som kon figurer ATS via NETSH WINHTTP (kör `NETSH WINHTTP SHOW PROXY` för att verifiera) Åsidosätt proxyinställningarna som du konfigurerade i steg 2. 

I anslutnings tjänsten för anslutningar används även datorns proxyserver. Du kan ändra det här beteendet genom att ändra filen ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Felsök problem med anslutnings proxy och tjänst anslutnings problem

Nu bör du se all trafik som flödar genom proxyservern. Om du har problem bör du ha följande felsöknings information.

Det bästa sättet att identifiera och felsöka anslutnings problem är att ta en nätverks avbildning när du startar kopplings tjänsten. Här följer några snabba tips om att fånga och filtrera nätverks spår.

Du kan använda valfritt övervaknings verktyg. I den här artikeln använde vi Microsoft Message Analyzer.

Följande exempel är speciella för Message Analyzer, men principerna kan tillämpas på alla analys verktyg.

### <a name="take-a-capture-of-connector-traffic"></a>Ta en bild av kopplings trafik

Utför följande steg för inledande fel sökning:

1. Stoppa tjänsten Azure AD-programproxy Connector från Services. msc.

   ![Azure AD-programproxy Connector-tjänsten i Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Kör Message Analyzer som administratör.
1. Välj **Starta lokal spårning**.
1. Starta tjänsten Azure AD-programproxy Connector.
1. Stoppa nätverks avbildningen.

   ![Skärm bild som visar knappen Stoppa nätverks insamling](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Kontrol lera om anslutnings trafiken kringgår utgående proxyservrar

Om du har konfigurerat din Application Proxy-anslutning för att kringgå proxyservrarna och ansluta direkt till Application Proxy-tjänsten, vill du söka i nätverks avbildningen efter misslyckade TCP-anslutnings försök.

Använd Message Analyzer-filtret för att identifiera de här försöken. Ange `property.TCPSynRetransmit` i rutan Filter och välj **Använd**.

Ett SYN paket är det första paket som skickas för att upprätta en TCP-anslutning. Om det här paketet inte returnerar ett svar görs ett nytt försök med tillståndet. Du kan använda föregående filter för att se alla SYNs som har skickats om. Sedan kan du kontrol lera om dessa SYNs motsvarar den kopplings-relaterade trafiken.

Om du förväntar dig att anslutningen ska göra direkta anslutningar till Azure-tjänsterna, är SynRetransmit svar på port 443 en indikation på att du har problem med nätverks-eller brand väggen.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Kontrol lera om anslutnings trafiken använder utgående proxyservrar

Om du har konfigurerat din Application Proxy Connector-trafik för att gå igenom proxyservrarna vill du söka efter misslyckade HTTPS-anslutningar till proxyservern.

Om du vill filtrera nätverks avbildningen för dessa anslutnings försök anger du `(https.Request or https.Response) and tcp.port==8080` i Message Analyzer-filtret och ersätter 8080 med din proxy service-port. Välj **tillämpa** för att se filter resultaten.

Föregående filter visar bara HTTPs-begärandena och svar på/från proxyservern. Du letar efter de CONNECT-begäranden som visar kommunikationen med proxyservern. När det är klart får du ett HTTP-svar (200).

Om du ser andra svars koder, till exempel 407 eller 502, innebär det att proxyn kräver autentisering eller inte tillåter trafik av någon annan anledning. Nu ska du delta i support teamet för proxyservern.

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
* Om du har problem med anslutnings problem kan du ställa din fråga på [sidan Microsoft Q&en fråga för att Azure Active Directory](https://docs.microsoft.com/answers/topics/azure-active-directory.html) eller skapa en biljett med vårt support team.
