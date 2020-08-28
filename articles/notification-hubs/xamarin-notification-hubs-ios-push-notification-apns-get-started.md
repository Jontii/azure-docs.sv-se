---
title: Skicka push-meddelanden till Xamarin med Azure Notification Hubs | Microsoft Docs
description: I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en Xamarin-iOS-app.
services: notification-hubs
keywords: push-meddelanden för ios, push-meddelanden, push-aviseringar, push-avisering
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 165d6c2578ba9ec0c939e4f1c1eaa497c9dff24d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998262"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>Självstudie: skicka push-meddelanden till Xamarin. iOS-appar med hjälp av Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt

I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app. Du skapar en tom Xamarin. iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen. Den färdiga koden finns tillgänglig i exemplet [NotificationHubs-app][GitHub].

I den här självstudiekursen får du skapa/uppdatera kod för att utföra följande uppgifter:

> [!div class="checklist"]
> * Generera filen för begäran om certifikatsignering
> * Registrera din app för push-meddelanden
> * Skapa en etableringsprofil för appen
> * Konfigurera din meddelandehubb för att skicka push-meddelanden till iOS
> * Skicka test-push-meddelanden

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Den senaste versionen av [Xcode][Install Xcode]
* En enhet som är kompatibel med iOS 10 (eller senare version)
* Medlemskap i [Apple Developer Program](https://developer.apple.com/programs/).
* [Visual Studio för Mac]
  
  > [!NOTE]
  > På grund av konfigurationskrav för iOS-pushmeddelanden måste du distribuera och testa exempelappen på en fysisk iOS-enhet (iPhone eller iPad) i stället för i simulatorn.

Du måste slutföra den här självstudiekursen innan du påbörjar någon annan kurs om Notification Hubs för Xamarin.iOS-appar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>Anslut appen till meddelandehubben

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

1. I Visual Studio skapar du ett nytt iOS-projekt och väljer mallen **Single View App** (App med enkel vy) och klickar på **Next** (Nästa)

     ![Välj Studio – Välj apptyp][31]

2. Ange appnamnet och organisations-ID, och klicka sedan på **Next** (Nästa) och **Create** (Skapa)

3. I vyn Solution (Lösning) dubbelklickar du på *Info.plist* och under **Identity** (Identitet) ser du till att paket-ID:t matchar det som används när du skapar etableringsprofilen. Under **Signing** (Signering) kontrollerar du att ditt Developer-konto är markerat under **Team**, att "Automatically manage signing" (Hantera signering automatiskt) är markerat samt att signeringscertifikatet och etableringsprofilen väljs automatiskt.

    ![Visual Studio – iOS-appkonfiguration][32]

4. I vyn lösning dubbelklickar du på `Entitlements.plist` och kontrollerar att **Aktivera push-meddelanden** är markerat.

    ![Visual Studio – konfigurera iOS-berättiganden][33]

5. Lägg till Azure Messaging-paketet. I vyn lösning högerklickar du på projektet och väljer **Lägg till**  >  **Lägg till NuGet-paket**. Sök efter **Xamarin.Azure.NotificationHubs.iOS** och lägg till paketet i projektet.

6. Lägg till en ny fil i klassen och ge den namnet `Constants.cs` Lägg till följande variabler och ersätt stränglitteralplatshållarna med `hubname` och `DefaultListenSharedAccessSignature` som noterats tidigare.

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. I `AppDelegate.cs` lägger du till följande using-instruktion:

    ```csharp
    using WindowsAzure.Messaging;
    using UserNotifications
    ```

8. Deklarera en instans av `SBNotificationHub`:

    ```csharp
    private SBNotificationHub Hub { get; set; }
    ```

9. I `AppDelegate.cs` uppdaterar du `FinishedLaunching()` till att matcha följande kod:

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        if (UIDevice.CurrentDevice.CheckSystemVersion(10, 0))
        {
            UNUserNotificationCenter.Current.RequestAuthorization(UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound,
                                                                    (granted, error) => InvokeOnMainThread(UIApplication.SharedApplication.RegisterForRemoteNotifications));
        }
        else if (UIDevice.CurrentDevice.CheckSystemVersion(8, 0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes(
                    UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                    new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();
        }
        else
        {
            UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes(notificationTypes);
        }

        return true;
    }
    ```

10. I `AppDelegate.cs` åsidosätter du metoden `RegisteredForRemoteNotifications()`:

    ```csharp
    public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
    {
        Hub = new SBNotificationHub(Constants.ListenConnectionString, Constants.NotificationHubName);

        Hub.UnregisterAll (deviceToken, (error) => {
            if (error != null)
            {
                System.Diagnostics.Debug.WriteLine("Error calling Unregister: {0}", error.ToString());
                return;
            }

            NSSet tags = null; // create tags if you want
            Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                if (errorCallback != null)
                    System.Diagnostics.Debug.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
            });
        });
    }
    ```

11. I `AppDelegate.cs` åsidosätter du metoden `ReceivedRemoteNotification()`:

    ```csharp
    public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
    {
        ProcessNotification(userInfo, false);
    }
    ```

12. I `AppDelegate.cs` skapar du metoden `ProcessNotification()`:

    ```csharp
    void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
    {
        // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
        if (null != options && options.ContainsKey(new NSString("aps")))
        {
            //Get the aps dictionary
            NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;

            //Extract the alert text
            // NOTE: If you're using the simple alert by just specifying
            // "  aps:{alert:"alert msg here"}  ", this will work fine.
            // But if you're using a complex alert with Localization keys, etc.,
            // your "alert" object from the aps dictionary will be another NSDictionary.
            // Basically the JSON gets dumped right into a NSDictionary,
            // so keep that in mind.
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //If this came from the ReceivedRemoteNotification while the app was running,
            // we of course need to manually process things like the sound, badge, and alert.
            if (!fromFinishedLaunching)
            {
                //Manually show an alert
                if (!string.IsNullOrEmpty(alert))
                {
                    var myAlert = UIAlertController.Create("Notification", alert, UIAlertControllerStyle.Alert);
                    myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
                    UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Du kan välja att åsidosätta `FailedToRegisterForRemoteNotifications()` för att hantera vissa situationer, till exempel om det inte finns någon nätverksanslutning. Detta är särskilt viktigt om användaren kan starta appen i offline-läge (t.ex. flygplansläge) och du vill hantera scenarier för push-meddelanden som är specifika för din app.

13. Kör appen på enheten.

## <a name="send-test-push-notifications"></a>Skicka test-push-meddelanden

Du kan testa att ta emot meddelanden i appen med alternativet *Skicka test* i [Azure Portal]. Den skickar ett test-push-meddelande till enheten.

![Azure Portal – Skicka test][30]

Push-meddelanden skickas vanligtvis via en serverdelstjänst, till exempel Mobile Apps eller ASP.NET, med hjälp av ett kompatibelt bibliotek. Du kan också använda REST-API:et direkt för att skicka meddelanden om ett bibliotek inte är tillgängligt för din serverdel.

## <a name="next-steps"></a>Nästa steg

I de här självstudierna har du skickat meddelanden till alla iOS-enheter som är registrerade hos serverdelen. Information om hur du skickar meddelanden till specifika iOS-enheter finns i följande självstudiekurs:

> [!div class="nextstepaction"]
>[Skicka push-meddelanden till specifika enheter](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio för Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure-portalen]: https://portal.azure.com
