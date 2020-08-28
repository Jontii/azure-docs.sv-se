---
title: Azure Notification Hubs Rich push
description: Lär dig hur du skickar omfattande push-meddelanden till en iOS-app från Azure. Kod exempel som skrivits i mål-C och C#.
documentationcenter: ios
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 590304df-c0a4-46c5-8ef5-6a6486bb3340
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d47974554534a0614eb98b473c1e5539ff4d9aa
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018016"
---
# <a name="azure-notification-hubs-rich-push"></a>Azure Notification Hubs Rich push

## <a name="overview"></a>Översikt

För att kunna engagera användare med omedelbar innehålls rik information kan ett program behöva push-överföra mer än vanligt text. Dessa meddelanden marknadsför användar interaktioner och visar innehåll som webb adresser, ljud, bilder/kuponger med mera. Den här själv studie Kursen bygger vidare på avsnittet [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) och visar hur du skickar push-meddelanden som inkluderar nytto laster (till exempel bild).

Den här självstudien är kompatibel med iOS 7 & 8.

  ![Tre skärm bilder: en app-skärm med knappen Skicka push, en Start skärm på en enhet och en Windows-logotyp med en tillbaka-knapp.][IOS1]

På hög nivå:

1. Server delen för appen:
   * Lagrar den omfattande nytto lasten (i det här fallet avbildning) i backend-databasen/lokal lagring
   * Skickar ID för det här omfattande meddelandet till enheten
2. App på enheten:
   * Kontaktar Server delen som begär den omfattande nytto lasten med det ID som tas emot
   * Skickar användar meddelanden på enheten när data hämtningen är klar och visar nytto lasten direkt när användarna trycker för att läsa mer

## <a name="webapi-project"></a>WebAPI-projekt

1. Öppna det **AppBackend** -projekt som du skapade i självstudien [meddela användare](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) i Visual Studio.
2. Hämta en avbildning som du vill meddela användare med och Lägg den i en **img** -mapp i projekt katalogen.
3. Klicka på **Visa alla filer** i Solution Explorer och högerklicka på mappen som ska tas med **i projektet**.
4. När du har valt avbildningen ändrar du dess build-åtgärd i Fönstret Egenskaper till **inbäddad resurs**.

    ![Skärm bild av Solution Explorer. Avbildnings filen väljs, och i rutan Egenskaper visas inbäddad resurs som bygg åtgärd.][IOS2]
5. I `Notifications.cs` lägger du till följande using-instruktion:

    ```csharp
    using System.Reflection;
    ```
6. Uppdatera hela `Notifications` klassen med följande kod. Se till att ersätta plats hållarna med autentiseringsuppgifterna för Notification Hub och bild fil namnet.

    ```csharp
    public class Notification {
        public int Id { get; set; }
        // Initial notification message to display to users
        public string Message { get; set; }
        // Type of rich payload (developer-defined)
        public string RichType { get; set; }
        public string Payload { get; set; }
        public bool Read { get; set; }
    }

    public class Notifications {
        public static Notifications Instance = new Notifications();

        private List<Notification> notifications = new List<Notification>();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            // Placeholders: replace with the connection string (with full access) for your notification hub and the hub name from the Azure Classics Portal
            Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}",  "{hub name}");
        }

        public Notification CreateNotification(string message, string richType, string payload) {
            var notification = new Notification() {
                Id = notifications.Count,
                Message = message,
                RichType = richType,
                Payload = payload,
                Read = false
            };

            notifications.Add(notification);

            return notification;
        }

        public Stream ReadImage(int id) {
            var assembly = Assembly.GetExecutingAssembly();
            // Placeholder: image file name (for example, logo.png).
            return assembly.GetManifestResourceStream("AppBackend.img.{logo.png}");
        }
    }
    ```

   > [!NOTE]
   > valfritt Se [hur du kan bädda in och få åtkomst till resurser med hjälp av Visual C#](https://support.microsoft.com/kb/319292) för mer information om hur du lägger till och hämtar projekt resurser.

7. I `NotificationsController.cs` omdefinierar du NotificationsController med följande kodfragment. Detta skickar ett inledande tyst meddelande-ID till enheten och gör det möjligt att hämta avbildningen på klient sidan:

    ```csharp
    // Return http response with image binary
    public HttpResponseMessage Get(int id) {
        var stream = Notifications.Instance.ReadImage(id);

        var result = new HttpResponseMessage(HttpStatusCode.OK);
        result.Content = new StreamContent(stream);
        // Switch in your image extension for "png"
        result.Content.Headers.ContentType = new System.Net.Http.Headers.MediaTypeHeaderValue("image/{png}");

        return result;
    }

    // Create rich notification and send initial silent notification (containing id) to client
    public async Task<HttpResponseMessage> Post() {
        // Replace the placeholder with image file name
        var richNotificationInTheBackend = Notifications.Instance.CreateNotification("Check this image out!", "img",  "{logo.png}");

        var usernameTag = "username:" + HttpContext.Current.User.Identity.Name;

        // Silent notification with content available
        var aboutUser = "{\"aps\": {\"content-available\": 1, \"sound\":\"\"}, \"richId\": \"" + richNotificationInTheBackend.Id.ToString() + "\",  \"richMessage\": \"" + richNotificationInTheBackend.Message + "\", \"richType\": \"" + richNotificationInTheBackend.RichType + "\"}";

        // Send notification to apns
        await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(aboutUser, usernameTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```
8. Nu kommer vi att omdistribuera den här appen till en Azure-webbplats för att göra den tillgänglig från alla enheter. Högerklicka på **AppBackend**-projektet och välj **Publicera**.
9. Välj Azure-webbplats som publicerings mål. Logga in med ditt Azure-konto och välj en befintlig eller ny webbplats och anteckna URL-egenskapen för **målet** på fliken **anslutning** . Vi kommer att referera till denna URL som *backend-slutpunkt* senare i den här självstudien. Klicka på **Publicera**.

## <a name="modify-the-ios-project"></a>Ändra iOS-projektet

Nu när du har ändrat appens Server del för att skicka bara *ID: t* för ett meddelande, ändrar du din iOS-app så att den hanterar detta ID och hämtar det avancerade meddelandet från Server delen.

1. Öppna ditt iOS-projekt och aktivera fjärraviseringar genom att gå till huvudappens mål i avsnittet **mål** .
2. Klicka på **funktioner**, aktivera **bakgrunds lägen**och markera kryss rutan **fjärraviseringar** .

    ![Skärm bild av iOS-projektet som visar funktions skärmen. Bakgrunds lägen är aktiverat och kryss rutan fjärraviseringar är markerad.][IOS3]
3. Öppna `Main.storyboard` och kontrol lera att du har en Visa kontrollant (kallas start visare i den här självstudien) från guiden [meddela användaren](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) .
4. Lägg till en **navigerings kontroll** på din storyboard och kontrol lera att du använder den för att **dra den till** en trädvy. Kontrol lera att den **initiala View-kontrollanten** i attribut kontroll bara är markerad för navigerings kontroll enheten.
5. Lägg till en **View-kontrollant** i storyboard och Lägg till en **bildvy**. Det här är sidan användare ser när de väljer att lära sig mer genom att klicka på meddelandet. Din storyboard bör se ut så här:

    ![Skärm bild av en storyboard. Tre appar visas: en vy för navigering, en start sida och en bildvy.][IOS4]
6. Klicka på **hem visnings styrenheten** på storyboard och kontrol lera att den har **HomeViewController** som dess **anpassade klass** och **storyboard-ID** under identitets kontrollen.
7. Gör samma sak för bildview-kontrollanten som **imageViewController**.
8. Skapa sedan en ny View Controller-klass med namnet **imageViewController** för att hantera det användar gränssnitt som du nyss skapade.
9. I **imageViewController. h**lägger du till följande i kontrollantens gränssnitts deklarationer. Kontrol lera att du drar från vyn storyboard bild till dessa egenskaper för att länka två:

    ```objc
    @property (weak, nonatomic) IBOutlet UIImageView *myImage;
    @property (strong) UIImage* imagePayload;
    ```
10. I `imageViewController.m` lägger du till följande i slutet av `viewDidload` :

    ```objc
    // Display the UI Image in UI Image View
    [self.myImage setImage:self.imagePayload];
    ```
11. I `AppDelegate.m` importerar du den avbildnings styrenhet som du skapade:

    ```objc
    #import "imageViewController.h"
    ```
12. Lägg till ett gränssnitts avsnitt med följande deklaration:

    ```objc
    @interface AppDelegate ()

    @property UIImage* imagePayload;
    @property NSDictionary* userInfo;
    @property BOOL iOS8;

    // Obtain content from backend with notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion;

    // Redirect to Image View Controller after notification interaction
    - (void)redirectToImageViewWithImage: (UIImage *)img;

    @end
    ```
13. I `AppDelegate` kontrollerar du att din App registrerar sig för meddelanden om tyst meddelande i `application: didFinishLaunchingWithOptions` :

    ```objc
    // Software version
    self.iOS8 = [[UIApplication sharedApplication] respondsToSelector:@selector(registerUserNotificationSettings:)] && [[UIApplication sharedApplication] respondsToSelector:@selector(registerForRemoteNotifications)];

    // Register for remote notifications for iOS8 and previous versions
    if (self.iOS8) {
        NSLog(@"This device is running with iOS8.");

        // Action
        UIMutableUserNotificationAction *richPushAction = [[UIMutableUserNotificationAction alloc] init];
        richPushAction.identifier = @"richPushMore";
        richPushAction.activationMode = UIUserNotificationActivationModeForeground;
        richPushAction.authenticationRequired = NO;
        richPushAction.title = @"More";

        // Notification category
        UIMutableUserNotificationCategory* richPushCategory = [[UIMutableUserNotificationCategory alloc] init];
        richPushCategory.identifier = @"richPush";
        [richPushCategory setActions:@[richPushAction] forContext:UIUserNotificationActionContextDefault];

        // Notification categories
        NSSet* richPushCategories = [NSSet setWithObjects:richPushCategory, nil];

        UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert |
                                                UIUserNotificationTypeBadge
                                                                                    categories:richPushCategories];

        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];

    }
    else {
        // Previous iOS versions
        NSLog(@"This device is running with iOS7 or earlier versions.");

        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    }

    return YES;
    ```

14. Ersätt följande implementering för `application:didRegisterForRemoteNotificationsWithDeviceToken` att ta ändringar i storyboard-gränssnittet i kontot:

    ```objc
    // Access navigation controller which is at the root of window
    UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
    // Get home view controller from stack on navigation controller
    homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
    hvc.deviceToken = deviceToken;
    ```
15. Lägg sedan till följande metoder för `AppDelegate.m` att hämta avbildningen från din slut punkt och skicka ett lokalt meddelande när hämtningen är klar. Se till att ersätta plats hållaren `{backend endpoint}` med Server dels slut punkten:

    ```objc
    NSString *const GetNotificationEndpoint = @"{backend endpoint}/api/notifications";

    // Helper: retrieve notification content from backend with rich notification id
    - (void)retrieveRichImageWithId:(int)richId completion: (void(^)(NSError*)) completion {
        UINavigationController *nc = (UINavigationController *)self.window.rootViewController;
        homeViewController *hvc = (homeViewController *)[nc.viewControllers objectAtIndex:0];
        NSString* authenticationHeader = hvc.registerClient.authenticationHeader;
        // Check if authenticated
        if (!authenticationHeader) return;

        NSURLSession* session = [NSURLSession
                                sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                delegate:nil
                                delegateQueue:nil];

        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, richId]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"GET"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {

            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200) {
                // From NSData to UIImage
                self.imagePayload = [UIImage imageWithData:data];

                completion(nil);
            }
            else {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(error);
                else {
                    completion([NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    // Handle silent push notifications when id is sent from backend
    - (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult result))handler {
        self.userInfo = userInfo;
        int richId = [[self.userInfo objectForKey:@"richId"] intValue];
        NSString* richType = [self.userInfo objectForKey:@"richType"];

        // Retrieve image data
        if ([richType isEqualToString:@"img"]) {  
            [self retrieveRichImageWithId:richId completion:^(NSError* error) {
                if (!error){
                    // Send local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];

                    // "5" is arbitrary here to give you enough time to quit out of the app and receive push notifications
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:5];
                    localNotification.userInfo = self.userInfo;
                    localNotification.alertBody = [self.userInfo objectForKey:@"richMessage"];
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];

                    // iOS8 categories
                    if (self.iOS8) {
                        localNotification.category = @"richPush";
                    }

                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];

                    handler(UIBackgroundFetchResultNewData);
                }
                else{
                    handler(UIBackgroundFetchResultFailed);
                }
            }];
        }
        // Add "else if" here to handle more types of rich content such as url, sound files, etc.
    }
    ```
16. Hantera det lokala meddelandet ovan genom att öppna bildvyns kontrollant i `AppDelegate.m` med följande metoder:

    ```objc
    // Helper: redirect users to image view controller
    - (void)redirectToImageViewWithImage: (UIImage *)img {
        UINavigationController *navigationController = (UINavigationController*) self.window.rootViewController;
        UIStoryboard *mainStoryboard = [UIStoryboard storyboardWithName:@"Main" bundle: nil];
        imageViewController *imgViewController = [mainStoryboard instantiateViewControllerWithIdentifier: @"imageViewController"];
        // Pass data/image to image view controller
        imgViewController.imagePayload = img;

        // Redirect
        [navigationController pushViewController:imgViewController animated:YES];
    }

    // Handle local notification sent above in didReceiveRemoteNotification
    - (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {
        if (application.applicationState == UIApplicationStateActive) {
            // Show in-app alert with an extra "more" button
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:notification.alertBody delegate:self cancelButtonTitle:@"OK" otherButtonTitles:@"More", nil];
            [alert show];
        }
        // App becomes active from user's tap on notification
        else {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
    }

    // Handle buttons in in-app alerts and redirect with data/image
    - (void)alertView:(UIAlertView *)alertView clickedButtonAtIndex:(NSInteger)buttonIndex {
        // Handle "more" button
        if (buttonIndex == 1)
        {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        // Add "else if" here to handle more buttons
    }

    // Handle notification setting actions in iOS8
    - (void)application:(UIApplication *)application handleActionWithIdentifier:(NSString *)identifier forLocalNotification:(UILocalNotification *)notification completionHandler:(void (^)())completionHandler {
        // Handle richPush related buttons
        if ([identifier isEqualToString:@"richPushMore"]) {
            [self redirectToImageViewWithImage:self.imagePayload];
        }
        completionHandler();
    }
    ```

## <a name="run-the-application"></a>Kör programmet

1. I XCode kör du appen på en fysisk iOS-enhet (push-meddelanden fungerar inte i simulatorn).
2. I iOS-appens användar gränssnitt anger du ett användar namn och lösen ord för samma värde för autentisering och klickar på **Logga in**.
3. Klicka på **skicka push** och du bör se en avisering i appen. Om du klickar på **mer**kommer du till den avbildning som du valde att ta med i appens Server del.
4. Du kan också klicka på **skicka push** och direkt trycka på enhetens Start knapp. Om en stund så får du ett push-meddelande. Om du knackar på den eller klickar på mer kommer du till din app och innehållet i innehålls sidan.

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-1.png
[IOS2]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-2.png
[IOS3]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-3.png
[IOS4]: ./media/notification-hubs-aspnet-backend-ios-rich-push/rich-push-ios-4.png
