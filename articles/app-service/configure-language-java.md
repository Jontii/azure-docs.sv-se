---
title: Konfigurera Windows Java-appar
description: Lär dig hur du konfigurerar Java-appar så att de körs på virtuella Windows-instanser i Azure App Service. Den här artikeln visar de vanligaste konfigurations åtgärderna.
keywords: Azure App Service, Web App, Windows, oss, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5d94da91428da2270e0f690df4dcd43ae43d8597
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961660"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Konfigurera en Java-app för Azure App Service

::: zone pivot="platform-windows"  

Med Azure App Service kan Java-utvecklare snabbt bygga, distribuera och skala sina Tomcat-webbprogram på en fullständigt hanterad Windows-baserad tjänst. Distribuera program med maven-plugin-program från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder i App Service. Om du aldrig har använt Azure App Service bör du läsa igenom Java- [snabb](quickstart-java.md) starten först. Allmänna frågor om att använda App Service som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Azure Web App-plugin-programmet för maven för](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) att distribuera dina War-filer. Distribution med populära IDE: er stöds också med [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) eller [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Annars beror distributions metoden på din Arkiv typ:

- Om du vill distribuera. War-filer till Tomcat använder du `/api/wardeploy/` slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#deploy-war-file).
- Om du vill distribuera. jar-filer till Java SE använder du `/api/zipdeploy/` slut punkten för kudu-webbplatsen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#rest).

Distribuera inte. War eller. jar med FTP. FTP-verktyget är utformat för att ladda upp start skript, beroenden eller andra runtime-filer. Det är inte det bästa valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och Felsök appar

Prestanda rapporter, trafik visualiseringar och hälso checkups är tillgängliga för varje app via Azure Portal. Mer information finns i [Översikt över Azure App Service-diagnostik](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Använd flyg inspelning

Alla Java-körningar på App Service med Azul-JVMs levereras med Zulu Flight-inspelare. Du kan använda detta för att registrera händelser för JVM, system och Java-nivå för att övervaka beteendet och felsöka problem i Java-programmen.

Om du vill ha en tidsad inspelning behöver du ett PID (process-ID) för Java-programmet. Du hittar PID genom att öppna en webbläsare till webbappens SCM-webbplats på https://<ditt webbplats namn>. scm.azurewebsites.net/ProcessExplorer/. Den här sidan visar de processer som körs i din webbapp. Hitta processen med namnet java i tabellen och kopiera motsvarande PID (process-ID).

Öppna sedan **fel söknings konsolen** i det övre verktygsfältet på SCM-platsen och kör följande kommando. Ersätt `<pid>` med det process-ID som du kopierade tidigare. Det här kommandot startar en 30 sekunders profilering av ditt Java-program och genererar en fil med namnet `timed_recording_example.jfr` i `D:\home` katalogen.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Mer information finns i [kommando referensen för Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analysera `.jfr` filer

Använd [FTPS](deploy-ftp.md) för att ladda ned jfr-filen till den lokala datorn. Om du vill analysera JFR-filen laddar du ned och installerar [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Anvisningar om hur du Zulu uppdrags kontroll finns i [Azul-dokumentationen](https://docs.azul.com/zmc/) och i [installations anvisningarna](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns i [Stream-loggar i Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Loggning av app

Aktivera [program loggning](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Loggning till den lokala App Service fil Systems instansen är inaktive rad 12 timmar efter att den har kon figurer ATS Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */LogFiles/Application/* .

Om ditt program använder [logback](https://logback.qos.ch/) eller [log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra spårningarna för granskning till Azure Application insikter med hjälp av konfigurations anvisningar för loggnings ramverk i [utforska Java trace-loggar i Application Insights](../azure-monitor/app/java-trace-logs.md).


## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service stöder anpassning och anpassning av rutorna i Azure Portal och CLI. Läs följande artiklar för konfiguration av icke-Java-en webbapp:

- [Konfigurera appinställningar](configure-common.md#configure-app-settings)
- [Konfigurera en anpassad domän](app-service-web-tutorial-custom-domain.md)
- [Konfigurera TLS-bindningar](configure-ssl-bindings.md)
- [Lägg till en CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurera kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Ange Java Runtime-alternativ

Om du vill ange allokerat minne eller andra alternativ för JVM-körning skapar du en [app-inställning](configure-common.md#configure-app-settings) med namnet `JAVA_OPTS` med alternativen. App Service skickar den här inställningen som en miljö variabel till Java-körningsmiljön när den startas.

I Azure Portal, under **program inställningar** för webbappen, skapar du en ny app-inställning med namnet `JAVA_OPTS` som innehåller de ytterligare inställningarna, till exempel `-Xms512m -Xmx1204m` .

Om du vill konfigurera appens inställning från maven-plugin-programmet lägger du till inställningar/värde-Taggar i Azure plugin-avsnittet. I följande exempel anges en angiven minsta och högsta Java-Heap-storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med en distributions plats i sin App Service plan kan använda följande alternativ:

- B1 och S1-instanser: `-Xms1024m -Xmx1024m`
- B2-och S2-instanser: `-Xms3072m -Xmx3072m`
- B3 och S3-instanser: `-Xms6144m -Xmx6144m`

När du finjusterar inställningarna för programheap granskar du App Service plan information och tar hänsyn till flera program och distributions fack måste hitta den optimala allokeringen av minnet.

### <a name="turn-on-web-sockets"></a>Aktivera webb-Sockets

Aktivera stöd för Web Sockets i Azure Portal i **program inställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera WebSocket-stöd med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standard tecken kodning

Skapa en ny app-inställning med namnet med värde under **program inställningar** för webbappen i Azure Portal `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Du kan också konfigurera appens inställning med hjälp av App Service maven-plugin-programmet. Lägg till inställnings namn och värde Taggar i plugin-konfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera JSP-filerna innan du distribuerar till App Service. Du kan använda [plugin-programmet maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache sling eller med hjälp av den här [Ant build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service har samma uppsättning [rekommenderade säkerhets metoder](../security/fundamentals/paas-applications-using-app-services.md) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (enkel autentisering)

Konfigurera app-autentisering i Azure Portal med alternativet **autentisering och auktorisering** . Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](configure-authentication-provider-aad.md) och relaterade artiklar för andra identitets leverantörer. Om du behöver aktivera flera inloggnings leverantörer följer du anvisningarna i artikeln [anpassa App Service-autentisering](app-service-authentication-how-to.md) .

#### <a name="tomcat"></a>Tomcat

Ditt Tomcat-program kan komma åt användarens anspråk direkt från servlet genom att omvandla huvudobjektet till ett kart objekt. Kart-objektet mappar varje anspråks typ till en samling av anspråken för den typen. I koden nedan `request` är en instans av `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du kontrol lera `Map` objektet för ett enskilt anspråk. Följande kodfragment itererar till exempel igenom alla anspråks typer och skriver ut innehållet i varje samling.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Använd sökvägen för att logga ut användare `/.auth/ext/logout` . Om du vill utföra andra åtgärder kan du läsa dokumentationen om [App Service autentisering och auktorisering](./app-service-authentication-how-to.md). Det finns också en officiell dokumentation om Tomcat [HttpServletRequest-gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servlet-metoder har också dehydratiseras baserat på din App Service konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen skapar du en program inställning med namnet `WEBSITE_AUTH_SKIP_PRINCIPAL` med värdet `1` . Om du vill inaktivera alla servlet-filter som har lagts till av App Service skapar du en inställning `WEBSITE_SKIP_FILTERS` med namnet med värdet `1` .

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ anvisningarna i avsnittet [skydda ett anpassat DNS-namn med en TLS-bindning i Azure App Service](configure-ssl-bindings.md) för att ladda upp ett befintligt TLS/SSL-certifikat och binda det till ditt programs domän namn. Som standard tillåter programmet fortfarande HTTP-anslutningar – Följ de specifika stegen i självstudien för att genomdriva SSL och TLS.

### <a name="use-keyvault-references"></a>Använda nyckel Valvs referenser

Azure-nyckels [valvet](../key-vault/general/overview.md) tillhandahåller centraliserad hemlig hantering med åtkomst principer och gransknings historik. Du kan lagra hemligheter (till exempel lösen ord eller anslutnings strängar) i ett nyckel valv och komma åt dessa hemligheter i ditt program via miljövariabler.

Börja med att följa anvisningarna för [att ge appen åtkomst till Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [skapa en nyckel valv referens till din hemlighet i en program inställning](app-service-key-vault-references.md#reference-syntax). Du kan kontrol lera att referensen matchar hemligheten genom att skriva ut miljövariabeln och fjärrans luta till App Service terminalen.

Om du vill mata in dessa hemligheter i din fjäder-eller Tomcat-konfigurationsfil använder du miljövariabeln insprutning-syntax ( `${MY_ENV_VAR}` ). För våren-konfigurationsfiler läser du den här dokumentationen om de [externa konfigurationerna](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribuerats på Azure App Service i Linux med NewRelic-och AppDynamics-plattformarna för program prestanda övervakning (APM).

### <a name="configure-new-relic"></a>Konfigurera nya Relic

1. Skapa ett nytt Relic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-java-x.x.x.zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. Använd [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog- */Home/site/wwwroot/APM*.
5. Överför de packade nya Relic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om din app använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen, fil namnet ser ut ungefär som *AppServerAgent-x.x.x.xxxxx.zip*
3. Använd [kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog- */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om du använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.

>  Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS` lägger du till `-javaagent:/...` alternativet i slutet av det aktuella värdet.

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databas anslutningar. Du måste fylla i plats hållarna med den valda databasens driv rutins klass namn och JAR-fil. Det angivna är en tabell med klass namn och hämtning av driv rutiner för vanliga databaser.

| Databas   | Klass namn för driv rutin                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Hämta](https://dev.mysql.com/downloads/connector/j/) (Välj plattform oberoende) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Om du vill konfigurera Tomcat för att använda Java Database Connectivity (JDBC) eller Java-persistence API (JPA), måste du först anpassa den `CATALINA_OPTS` miljö variabel som läses in av Tomcat vid start. Ange dessa värden via en app-inställning i [maven-plugin-programmet för App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan Inställningar för **konfigurations**  >  **program** i Azure Portal.

Ta sedan reda på om data källan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Data källor på program nivå

1. Skapa en *context.xml* -fil i projektets *meta-inf/* katalog. Skapa *meta-inf/-* katalogen om den inte finns.

2. I *context.xml*lägger du till ett- `Context` element för att länka data källan till en JNDI-adress. Ersätt `driverClassName` plats hållaren med driv Rutinens klass namn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera programmets *web.xml* att använda data källan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Slutför konfiguration

Slutligen kommer vi att placera driv rutins jar v7 i Tomcat-classpath och starta om App Service. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Konfigurera Tomcat

Om du vill redigera Tomcat `server.xml` eller andra konfigurationsfiler bör du först anteckna din tomcat-huvud version i portalen.

1. Hitta Tomcat-hemkatalogen för din version genom att köra `env` kommandot. Sök efter den miljö variabel som börjar med `AZURE_TOMCAT` och matchar din huvud version. Till exempel `AZURE_TOMCAT85_HOME` pekar till Tomcat-katalogen för Tomcat 8,5.
1. När du har identifierat arbets katalogen Tomcat för din version, kan du kopiera konfigurations katalogen till `D:\home` . Om till exempel `AZURE_TOMCAT85_HOME` har fått värdet `D:\Program Files (x86)\apache-tomcat-8.5.37` är den nya sökvägen till den kopierade katalogen `D:\home\apache-tomcat-8.5.37` .

Slutligen startar du om din App Service. Dina distributioner bör gå till `D:\home\site\wwwroot\webapps` precis som tidigare.

## <a name="configure-java-se"></a>Konfigurera Java SE

När du kör en. JAR Application på Java SE i Windows `server.port` skickas som ett kommando rads alternativ när programmet startas. Du kan manuellt matcha HTTP-porten från miljö variabeln `HTTP_PLATFORM_PORT` . Värdet för den här miljövariabeln är den HTTP-port som programmet ska lyssna på. 

## <a name="java-runtime-statement-of-support"></a>Stöd för Java Runtime-sats

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures Java Development Kit (JDK) som stöds är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul system](https://www.azul.com/).

Huvud versions uppdateringar kommer att tillhandahållas via nya körnings alternativ i Azure App Service för Windows. Kunder uppdaterar till dessa nyare versioner av Java genom att konfigurera sina App Service-distributioner och ansvarar för att testa och se till att den viktiga uppdateringen uppfyller deras behov.

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år. Mer information om java i Azure finns i [det här support dokumentet](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/vuln-metrics/cvss).

Tomcat 8,0 har uppnått [livs längd (EOL) från och med 30 September 2018](https://tomcat.apache.org/tomcat-80-eol.html). Även om körningen fortfarande är tillgänglig på Azure App Service kommer Azure inte att tillämpa säkerhets uppdateringar på Tomcat 8,0. Migrera om möjligt dina program till Tomcat 8,5 eller 9,0. Både Tomcat 8,5 och 9,0 finns på Azure App Service. Mer information finns på den [officiella Tomcat-webbplatsen](https://tomcat.apache.org/whichversion.html) . 

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.

### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned Production Edition of Azul Zulu Enterprise JDK för lokal utveckling från [Azuls nedladdnings plats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Utvecklings support

Produkt support för det [Azure-Azul Zulu-JDK som stöds](https://www.azul.com/downloads/azure-only/zulu/) är tillgängligt via Microsoft när du utvecklar för azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal för Azure.

### <a name="runtime-support"></a>Stöd för körning

Utvecklare kan [öppna ett problem](../azure-portal/supportability/how-to-create-azure-support-request.md) med Azul Zulu-JDKs via Azure-support om de har ett [kvalificerat support](https://azure.microsoft.com/support/plans/)avtal.

## <a name="next-steps"></a>Nästa steg

Det här avsnittet innehåller Java Runtime-satsen för stöd för Azure App Service i Windows.

- Om du vill veta mer om att vara värd för webb program med Azure App Service se [Översikt över App Service](overview.md).
- Information om Java på Azure-utveckling finns i [Azure för java dev Center](/java/azure/?view=azure-java-stable).

::: zone-end

::: zone pivot="platform-linux"

Med Azure App Service i Linux kan Java-utvecklare snabbt bygga, distribuera och skala sina Tomcat, eller Java Standard Edition (SE) paketerade webb program på en fullständigt hanterad Linux-baserad tjänst. Distribuera program med maven-plugin-program från kommando raden eller i redigerings program som IntelliJ, Sol förmörkelse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer du [Java-snabb](quickstart-java.md)starten.

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [maven-plugin-programmet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) för att distribuera både jar-och. War-filer. Distribution med populära IDE: er stöds också med [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) eller [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Annars beror distributions metoden på din Arkiv typ:

- Om du vill distribuera. War-filer till Tomcat använder du `/api/wardeploy/` slut punkten för att publicera Arkiv filen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#deploy-war-file).
- Om du vill distribuera. jar-filer på Java SE-avbildningarna använder du `/api/zipdeploy/` slut punkten för kudu-webbplatsen. Mer information om det här API: et finns i [den här dokumentationen](./deploy-zip.md#rest).

Distribuera inte. War eller. jar med FTP. FTP-verktyget är utformat för att ladda upp start skript, beroenden eller andra runtime-filer. Det är inte det bästa valet för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och Felsök appar

Prestanda rapporter, trafik visualiseringar och hälso checkups är tillgängliga för varje app via Azure Portal. Mer information finns i [Översikt över Azure App Service-diagnostik](overview-diagnostics.md).

### <a name="ssh-console-access"></a>Åtkomst till SSH-konsol

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Mer information finns i [Stream-loggar i Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Loggning av app

Aktivera [program loggning](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via Azure Portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service att skriva programmets standard-och standard konsol fel strömmar till det lokala fil systemet eller Azure-Blob Storage. Loggning till den lokala App Service fil Systems instansen är inaktive rad 12 timmar efter att den har kon figurer ATS Om du behöver kvarhållare kan du konfigurera programmet att skriva utdata till en Blob Storage-behållare. Du hittar dina program loggar för Java och Tomcat i katalogen */Home/LogFiles/Application/* .

>[!NOTE]
>Loggning av det lokala App Service fil systemet har inaktiverats efter 12 timmar gäller endast Windows-baserade App Services. Azure Blob Storage-loggning för Linux-baserade App Services kan bara konfigureras med [Azure Monitor (för hands version)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

Om ditt program använder [logback](https://logback.qos.ch/) eller [log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra spårningarna för granskning till Azure Application insikter med hjälp av konfigurations anvisningar för loggnings ramverk i [utforska Java trace-loggar i Application Insights](../azure-monitor/app/java-trace-logs.md).

### <a name="troubleshooting-tools"></a>Fel söknings verktyg

De inbyggda Java-avbildningarna baseras på operativ systemet [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Använd `apk` Package Manager för att installera eventuella fel söknings verktyg eller kommandon.

### <a name="flight-recorder"></a>Flyg inspelning

Alla Linux Java-avbildningar på App Service har Zulu Flight-brännare installerad så att du enkelt kan ansluta till JVM och starta en profilerings inspelning eller generera en heap-dump.

#### <a name="timed-recording"></a>Inspelnings tid

Kom igång genom att använda SSH i App Service och köra `jcmd` kommandot för att se en lista över alla Java-processer som körs. Förutom jcmd bör du se ditt Java-program med ett process-ID-nummer (PID).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30-sekunders inspelning av JVM. Detta kommer att profilera JVM och skapa en JFR-fil med namnet *jfr_example. jfr* i arbets katalogen. (Ersätt 116 med PID för din Java-app.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Under det 30 andra intervallet kan du verifiera att inspelningen sker genom att köra `jcmd 116 JFR.check` . Då visas alla inspelningar för den aktuella Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig inspelning

Du kan använda Zulu Flight-brännare för att kontinuerligt profilera ditt Java-program med minimal påverkan på körnings prestanda ([källa](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Det gör du genom att köra följande Azure CLI-kommando för att skapa en app-inställning med namnet JAVA_OPTS med den nödvändiga konfigurationen. Innehållet i inställningen för JAVA_OPTS appen skickas till `java` kommandot när appen startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

När inspelningen har startats kan du när som helst dumpa de aktuella inspelnings data med hjälp av `JFR.dump` kommandot.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Mer information finns i [kommando referensen för Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analysera inspelningar

Använd [FTPS](deploy-ftp.md) för att ladda ned jfr-filen till den lokala datorn. Om du vill analysera JFR-filen laddar du ned och installerar [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Anvisningar om hur du Zulu uppdrags kontroll finns i [Azul-dokumentationen](https://docs.azul.com/zmc/) och i [installations anvisningarna](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder direkt justering och anpassning genom Azure Portal och CLI. Läs följande artiklar för konfiguration av icke-Java-en webbapp:

- [Konfigurera appinställningar](configure-common.md#configure-app-settings)
- [Konfigurera en anpassad domän](app-service-web-tutorial-custom-domain.md)
- [Konfigurera SSL-bindningar](configure-ssl-bindings.md)
- [Lägg till en CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurera kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ange Java Runtime-alternativ

Om du vill ange allokerat minne eller andra alternativ för JVM körning i båda miljöerna Tomcat och Java SE skapar du en [app-inställning](configure-common.md#configure-app-settings) med namnet `JAVA_OPTS` med alternativen. App Service Linux skickar den här inställningen som en miljö variabel till Java-körningsmiljön när den startas.

I Azure Portal, under **program inställningar** för webbappen, skapar du en ny app-inställning med namnet `JAVA_OPTS` som innehåller de ytterligare inställningarna, till exempel `-Xms512m -Xmx1204m` .

Om du vill konfigurera appens inställning från maven-plugin-programmet lägger du till inställningar/värde-Taggar i Azure plugin-avsnittet. I följande exempel anges en angiven minsta och högsta Java-Heap-storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med en distributions plats i sin App Service plan kan använda följande alternativ:

- B1 och S1-instanser: `-Xms1024m -Xmx1024m`
- B2-och S2-instanser: `-Xms3072m -Xmx3072m`
- B3 och S3-instanser: `-Xms6144m -Xmx6144m`

När du finjusterar inställningarna för programheap granskar du App Service plan information och tar hänsyn till flera program och distributions fack måste hitta den optimala allokeringen av minnet.

Om du distribuerar ett JAR-program ska det heta *app. jar* så att den inbyggda avbildningen kan identifiera din app korrekt. (Plugin-programmet maven byter namn på det automatiskt.) Om du inte vill byta namn på JAR till *app. jar*kan du ladda upp ett shell-skript med kommandot för att köra din jar. Klistra sedan in den fullständiga sökvägen till skriptet i textrutan [Startfil](faq-app-service-linux.md#built-in-images) i konfigurationsavsnittet i portalen. Startskriptet körs inte från den katalog där den placeras. Använd därför alltid absoluta sökvägar för att referera till filer i startskriptet (till exempel: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Aktivera webb-Sockets

Aktivera stöd för Web Sockets i Azure Portal i **program inställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera WebSocket-stöd med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standard tecken kodning

Skapa en ny app-inställning med namnet med värde under **program inställningar** för webbappen i Azure Portal `JAVA_OPTS` `-Dfile.encoding=UTF-8` .

Du kan också konfigurera appens inställning med hjälp av App Service maven-plugin-programmet. Lägg till inställnings namn och värde Taggar i plugin-konfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Justera tids gräns för start

Om Java-programmet är särskilt stort bör du öka tids gränsen för start. Om du vill göra det skapar du en program inställning `WEBSITES_CONTAINER_START_TIME_LIMIT` och anger den till det antal sekunder som App Service ska vänta innan timeout. Det maximala värdet är `1800` sekunder.

### <a name="pre-compile-jsp-files"></a>Kompilera JSP-filer i förväg

För att förbättra prestanda för Tomcat-program kan du kompilera JSP-filerna innan du distribuerar till App Service. Du kan använda [plugin-programmet maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) som tillhandahålls av Apache sling eller med hjälp av den här [Ant build-filen](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service för Linux har samma uppsättning [rekommenderade säkerhets metoder](../security/fundamentals/paas-applications-using-app-services.md) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (enkel autentisering)

Konfigurera app-autentisering i Azure Portal med alternativet **autentisering och auktorisering** . Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure Portal konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera din app service app för att använda Azure Active Directory inloggning](configure-authentication-provider-aad.md) och relaterade artiklar för andra identitets leverantörer. Om du behöver aktivera flera inloggnings leverantörer följer du anvisningarna i artikeln [anpassa App Service-autentisering](app-service-authentication-how-to.md) .

#### <a name="tomcat"></a>Tomcat

Ditt Tomcat-program kan komma åt användarens anspråk direkt från servlet genom att omvandla huvudobjektet till ett kart objekt. Kart-objektet mappar varje anspråks typ till en samling av anspråken för den typen. I koden nedan `request` är en instans av `HttpServletRequest` .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du kontrol lera `Map` objektet för ett enskilt anspråk. Följande kodfragment itererar till exempel igenom alla anspråks typer och skriver ut innehållet i varje samling.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Använd sökvägen för att logga ut användare `/.auth/ext/logout` . Om du vill utföra andra åtgärder kan du läsa dokumentationen om [App Service autentisering och auktorisering](./app-service-authentication-how-to.md). Det finns också en officiell dokumentation om Tomcat [HttpServletRequest-gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servlet-metoder har också dehydratiseras baserat på din App Service konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen skapar du en program inställning med namnet `WEBSITE_AUTH_SKIP_PRINCIPAL` med värdet `1` . Om du vill inaktivera alla servlet-filter som har lagts till av App Service skapar du en inställning `WEBSITE_SKIP_FILTERS` med namnet med värdet `1` .

#### <a name="spring-boot"></a>Spring Boot

Förstarts utvecklare kan använda [Start Start programmet Azure Active Directory våren](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) för att skydda program med hjälp av välbekanta säkerhets anteckningar och API: er. Se till att öka storleken på den maximala sidhuvuden i *program. Properties* -filen. Vi föreslår ett värde av `16384` .

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ anvisningarna i avsnittet [skydda ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md) för att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domän namn. Som standard tillåter programmet fortfarande HTTP-anslutningar – Följ de specifika stegen i självstudien för att genomdriva SSL och TLS.

### <a name="use-keyvault-references"></a>Använda nyckel Valvs referenser

Azure-nyckels [valvet](../key-vault/general/overview.md) tillhandahåller centraliserad hemlig hantering med åtkomst principer och gransknings historik. Du kan lagra hemligheter (till exempel lösen ord eller anslutnings strängar) i ett nyckel valv och komma åt dessa hemligheter i ditt program via miljövariabler.

Börja med att följa anvisningarna för [att ge appen åtkomst till Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [skapa en nyckel valv referens till din hemlighet i en program inställning](app-service-key-vault-references.md#reference-syntax). Du kan kontrol lera att referensen matchar hemligheten genom att skriva ut miljövariabeln och fjärrans luta till App Service terminalen.

Om du vill mata in dessa hemligheter i din fjäder-eller Tomcat-konfigurationsfil använder du miljövariabeln insprutning-syntax ( `${MY_ENV_VAR}` ). För våren-konfigurationsfiler läser du den här dokumentationen om de [externa konfigurationerna](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Använda Java-nyckel arkivet

Som standard kommer alla offentliga eller privata certifikat som [laddats upp till App Service Linux](configure-ssl-certificate.md) att läsas in i respektive Java-nyckel Arkiv när behållaren startar. När du har laddat upp certifikatet måste du starta om App Service för att det ska kunna läsas in i Java-nyckel arkivet. Offentliga certifikat läses in i nyckel arkivet vid `$JAVA_HOME/jre/lib/security/cacerts` , och privata certifikat lagras i `$JAVA_HOME/lib/security/client.jks` .

Ytterligare konfiguration kan vara nödvändig för att kryptera JDBC-anslutningen med certifikat i Java-nyckel arkivet. Se dokumentationen för din valda JDBC-drivrutin.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Initiera Java-nyckel arkivet

Om du vill initiera `import java.security.KeyStore` objektet läser du in nyckel lagrings filen med lösen ordet. Standard lösen ordet för båda nyckel arkiven är "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Läs in nyckel lagret manuellt

Du kan läsa in certifikat manuellt till nyckel lagret. Skapa en app-inställning, `SKIP_JAVA_KEYSTORE_LOAD` med värdet `1` att inaktivera App Service från att läsa in certifikaten i nyckel arkivet automatiskt. Alla offentliga certifikat som laddats upp till App Service via Azure Portal lagras under `/var/ssl/certs/` . Privata certifikat lagras under `/var/ssl/private/` .

Du kan interagera eller felsöka Java-nyckel verktyget genom att [öppna en SSH-anslutning](configure-linux-open-ssh-session.md) till app service och köra kommandot `keytool` . En lista över kommandon finns i [dokumentationen för nyckel verktyget](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) . Mer information om API: t för nyckel Arkiv finns i [den officiella dokumentationen](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribuerats på Azure App Service i Linux med NewRelic-och AppDynamics-plattformarna för program prestanda övervakning (APM).

### <a name="configure-new-relic"></a>Konfigurera nya Relic

1. Skapa ett NewRelic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Hämta Java-agenten från NewRelic kommer den att ha ett fil namn som liknar *newrelic-java-x.x.x.zip*.
3. Kopiera licens nyckeln, du behöver den för att konfigurera agenten senare.
4. [Använda SSH i App Service-instansen](configure-linux-open-ssh-session.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
5. Överför de uppackade NewRelic Java agent-filerna till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/newrelic*.
6. Ändra YAML-filen på */Home/site/wwwroot/APM/newrelic/newrelic.yml* och ersätt licens värdet för plats hållaren med din egen licens nyckel.
7. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om din app använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Hämta Java-agenten från AppDynamics-webbplatsen, fil namnet ser ut ungefär som *AppServerAgent-x.x.x.xxxxx.zip*
3. [Använda SSH i App Service-instansen](configure-linux-open-ssh-session.md) och skapa en ny katalog */Home/site/wwwroot/APM*.
4. Överför Java-agentens filer till en katalog under */Home/site/wwwroot/APM*. Filerna för din agent ska vara i */Home/site/wwwroot/APM/AppDynamics*.
5. I Azure Portal bläddrar du till ditt program i App Service och skapar en ny program inställning.
    - Om du använder **Java se**skapar du en miljö variabel med namnet `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.
    - Om du använder **Tomcat**skapar du en miljö variabel med namnet `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt App Service namn.

    > [!NOTE]
    > Om du redan har en miljö variabel för `JAVA_OPTS` eller `CATALINA_OPTS` lägger du till `-javaagent:/...` alternativet i slutet av det aktuella värdet.
    
## <a name="configure-jar-applications"></a>Konfigurera JAR-program

### <a name="starting-jar-apps"></a>Starta JAR-appar

Som standard förväntar App Service att JAR-programmet ska ha namnet *app. jar*. Om det har det här namnet kommer det att köras automatiskt. För maven-användare kan du ange JAR-namnet genom `<finalName>app</finalName>` att inkludera i `<build>` avsnittet i *pom.xml*. [Du kan göra samma sak i Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) genom att ange `archiveFileName` egenskapen.

Om du vill använda ett annat namn för din JAR måste du också ange [Start kommandot](faq-app-service-linux.md#built-in-images) som kör jar-filen. Till exempel `java -jar my-jar-app.jar`. Du kan ställa in värdet för ditt Start kommando i portalen, under konfiguration > allmänna inställningar eller med en program inställning med namnet `STARTUP_COMMAND` .

### <a name="server-port"></a>Server Port

App Service Linux dirigerar inkommande begär anden till port 80, så att ditt program även lyssnar på port 80. Du kan göra detta i programmets konfiguration (t *. ex. vår app. Properties* -fil) eller i ditt Start kommando (till exempel `java -jar spring-app.jar --server.port=80` ). Se följande dokumentation för vanliga Java-ramverk:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play-ramverk](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla databas anslutningar. Du måste fylla i plats hållarna med den valda databasens driv rutins klass namn och JAR-fil. Det angivna är en tabell med klass namn och hämtning av driv rutiner för vanliga databaser.

| Databas   | Klass namn för driv rutin                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Hämta](https://dev.mysql.com/downloads/connector/j/) (Välj plattform oberoende) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Om du vill konfigurera Tomcat för att använda Java Database Connectivity (JDBC) eller Java-persistence API (JPA), måste du först anpassa den `CATALINA_OPTS` miljö variabel som läses in av Tomcat vid start. Ange dessa värden via en app-inställning i [maven-plugin-programmet för App Service](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan Inställningar för **konfigurations**  >  **program** i Azure Portal.

Ta sedan reda på om data källan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat-servlet.

#### <a name="application-level-data-sources"></a>Data källor på program nivå

1. Skapa en *context.xml* -fil i projektets *meta-inf/* katalog. Skapa *meta-inf/-* katalogen om den inte finns.

2. I *context.xml*lägger du till ett- `Context` element för att länka data källan till en JNDI-adress. Ersätt `driverClassName` plats hållaren med driv Rutinens klass namn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera programmets *web.xml* att använda data källan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på server nivå

Om du lägger till en delad data källa på server nivå krävs det att du redigerar Tomcat server.xml. Börja med att ladda upp ett [Start skript](faq-app-service-linux.md#built-in-images) och ange sökvägen till skriptet i **konfigurations**  >  **Start kommandot**. Du kan ladda upp start skriptet med [FTP](deploy-ftp.md).

Start skriptet gör en XSL- [transformering](https://www.w3schools.com/xml/xsl_intro.asp) till server.xml-filen och utdata från den resulterande XML-filen till `/usr/local/tomcat/conf/server.xml` . Start skriptet bör installera libxslt via APK. XSL-filen och start skriptet kan överföras via FTP. Nedan visas ett exempel på ett start skript.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Ett exempel på en XSL-fil anges nedan. Exempel-XSL-filen lägger till en ny anslutnings-nod till Tomcat-server.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Slutför konfiguration

Till sist placerar du driv rutins jar v7 i Tomcat-classpath och startar om App Service.

1. Se till att JDBC-drivrutinsfiler är tillgängliga för Tomcat-classloader genom att placera dem i */Home/Tomcat/lib* -katalogen. (Skapa den här katalogen om den inte redan finns.) Utför följande steg för att överföra filerna till din App Service-instans:

    1. I [Cloud Shell](https://shell.azure.com)installerar du webapp-tillägget:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från det lokala systemet till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Anslut till den lokala tunnel porten med din SFTP-klient och överför filerna till mappen */Home/Tomcat/lib* .

    Alternativt kan du använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [anvisningarna för att hämta dina FTP-autentiseringsuppgifter](deploy-configure-credentials.md).

2. Om du har skapat en data källa på server nivå startar du om App Service Linux-programmet. Tomcat kommer att återställas `CATALINA_BASE` till `/home/tomcat` och använda den uppdaterade konfigurationen.

### <a name="spring-boot"></a>Spring Boot

För att ansluta till data källor i Start program för våren rekommenderar vi att du skapar anslutnings strängar och infogar dem i *appens. egenskaps* fil.

1. I avsnittet "konfiguration" på sidan App Service anger du ett namn för strängen, klistrar in JDBC-anslutningssträngen i fältet värde och anger typen anpassad. Du kan också ange den här anslutnings strängen som plats inställning.

    Den här anslutnings strängen är tillgänglig för vårt program som en miljö variabel med namnet `CUSTOMCONNSTR_<your-string-name>` . Den anslutnings sträng som vi skapade ovan får exempelvis namnet `CUSTOMCONNSTR_exampledb` .

2. I din *app. Properties* -fil refererar du till den här anslutnings strängen med miljö variabel namnet. I vårt exempel skulle vi använda följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Mer information om det här ämnet finns i [vår Boot-dokumentation om data åtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) och [externa konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) .

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Använda Redis som en session-cache med Tomcat

Du kan konfigurera Tomcat till att använda ett externt sessionsobjekt, till exempel [Azure cache för Redis](../azure-cache-for-redis/index.yml). På så sätt kan du bevara sessionstillstånd för användare (till exempel shopping vagns data) när en användare överförs till en annan instans av appen, till exempel när automatisk skalning, omstart eller redundans inträffar.

Om du vill använda Tomcat med Redis måste du konfigurera appen så att den använder en [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) -implementering. I följande steg förklaras den här processen med hjälp av [pivoting session Manager: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) som ett exempel.

1. Öppna en bash-Terminal och Använd `<variable>=<value>` för att ange var och en av följande miljövariabler.

    | Variabel                 | Värde                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Namnet på den resurs grupp som innehåller App Service-instansen.       |
    | WEBAPP_NAME              | Namnet på App Service-instansen.                                     |
    | WEBAPP_PLAN_NAME         | Namnet på din App Service plan.                                         |
    | REGION                   | Namnet på den region där din app finns.                           |
    | REDIS_CACHE_NAME         | Namnet på Azure-cachen för Redis-instansen.                           |
    | REDIS_PORT               | SSL-porten som Redis-cachen lyssnar på.                             |
    | REDIS_PASSWORD           | Den primära åtkomst nyckeln för din instans.                                  |
    | REDIS_SESSION_KEY_PREFIX | Ett värde som du anger för att identifiera sessionsnycklar som kommer från din app. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Du hittar information om namn, port och åtkomst nycklar på Azure Portal genom att titta i avsnitten **Egenskaper** eller **åtkomst nycklar** i tjänst instansen.

2. Skapa eller uppdatera appens *källa/huvud-/webapp-eller meta-inf/context.xml- * fil med följande innehåll:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Den här filen anger och konfigurerar sessionshanteraren för din app. Den använder de miljövariabler som du angav i föregående steg för att hålla din konto information ut från källfilerna.

3. Använd FTP för att ladda upp sessionshanteraren i App Service-instansen och placera den i */Home/Tomcat/lib* -katalogen. Mer information finns i [distribuera din app för att Azure App Service att använda FTP/S](./deploy-ftp.md).

4. Inaktivera [cookien för session-tillhörighet](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) för App Service-instansen. Du kan göra detta från Azure Portal genom att **navigera till din**app och sedan ställa in **konfigurations > allmänna inställningar > arr-tillhörighet** . Alternativt kan du använda följande kommando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Som standard använder App Service session tillhörighets-cookies för att se till att klient begär Anden med befintliga sessioner dirigeras till samma instans av programmet. Detta standard beteende kräver ingen konfiguration, men den kan inte bevara sessionstillstånd när din App-instans startas om eller när trafik dirigeras om till en annan instans. När du [inaktiverar den befintliga](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfigurationen för arr-tillhörighet för att stänga av sessionen cookie-baserad routning, tillåter du att det konfigurerade sessionstillståndet kan köras utan störningar.

5. Navigera till avsnittet **Egenskaper** i App Service-instansen och hitta **ytterligare utgående IP-adresser**. Detta representerar alla möjliga utgående IP-adresser för din app. Kopiera dessa för användning i nästa steg.

6. För varje IP-adress skapar du en brand Väggs regel i Azure-cachen för Redis-instansen. Du kan göra detta på Azure Portal från **brand Väggs** avsnittet i Redis-instansen. Ange ett unikt namn för varje regel och ange **Start-IP-adress** och **slut-IP-adress** värden till samma IP-adress.

7. Navigera till avsnittet **Avancerade inställningar** i Redis-instansen och ange **Tillåt endast åtkomst via SSL** till **Nej**. Detta gör att App Service-instansen kan kommunicera med din Redis-cache via Azure-infrastrukturen.

8. Uppdatera `azure-webapp-maven-plugin` konfigurationen i appens *pom.xml* -fil för att referera till din Redis-konto information. I den här filen används de miljövariabler som du angav tidigare för att behålla din konto information från källfilerna.

    Vid behov ändrar du `1.9.1` till den aktuella versionen av [Maven-pluginet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Återskapa och distribuera om din app.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Din app kommer nu att använda Redis-cache för hantering av sessioner.

Ett exempel som du kan använda för att testa dessa instruktioner finns i avsnittet om [skalning-tillstånds känsligt-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) på GitHub.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Azure-Zulu-JDK som stöds i dina behållare, se till att hämta och använda de färdiga avbildningarna enligt dokumentationen från [Azul-Zulu Enterprise för Azures hämtnings sida](https://www.azul.com/downloads/azure-only/zulu/) eller Använd `Dockerfile` exemplen från [Microsoft Java GitHub lagrings platsen](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Support specifikation

### <a name="runtime-availability"></a>Tillgänglighet för körning

App Service för Linux stöder två körningar för hanterad värd för Java-webb program:

- [Tomcat servlet-behållaren](https://tomcat.apache.org/) för att köra program som paketerade som Web Archive (War)-filer. Versioner som stöds är 8,5 och 9,0.
- Java SE körnings miljö för att köra program som paketeras som filer i Java Archive (JAR). Versioner som stöds är Java 8 och 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azul Zulu Enterprise builds of OpenJDK är en kostnads färdig distribution med flera plattformar av OpenJDK för Azure och Azure Stack som backas upp av Microsoft-och Azul-system. De innehåller alla komponenter för att skapa och köra Java SE-program. Du kan installera JDK från [Java JDK-installationen](https://aka.ms/azure-jdks).

JDKs som stöds korrigeras automatiskt en gång i kvartalet i januari, april, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Korrigeringar och korrigeringar för viktiga säkerhets problem kommer att släppas så snart de blir tillgängliga från Azul system. En "större" sårbarhet definieras av en baspoäng på 9,0 eller högre på [NIST vanliga sårbarhets bedömnings system, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds dras tillbaka kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om förvarning minst sex månader innan körningen dras tillbaka.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

Besök [Azure för Java Developer](/java/azure/) Center för att hitta Azures snabb starter, självstudier och Java-referens dokumentation.

Allmänna frågor om hur du använder App Service för Linux som inte är särskilt för Java-utveckling besvaras i [vanliga frågor och svar om App Service Linux](faq-app-service-linux.md).

::: zone-end