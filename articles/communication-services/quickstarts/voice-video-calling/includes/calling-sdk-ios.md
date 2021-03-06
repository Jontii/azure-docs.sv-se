---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: a8cfdc76694d52acee70cde0e3f1697cd8129d06
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97691948"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- A `User Access Token` för att aktivera anrops klienten. Mer information om [hur du hämtar en `User Access Token` ](../../access-tokens.md)
- Valfritt: Slutför snabb starten för att [komma igång med att lägga till samtal till ditt program](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurera

### <a name="creating-the-xcode-project"></a>Skapa Xcode-projektet

I Xcode skapar du ett nytt iOS-projekt och väljer app-mallen för **enskild vy** . I den här snabb starten används [SwiftUI-ramverket](https://developer.apple.com/xcode/swiftui/), så du bör ange **språket** till **Swift** och **användar gränssnittet** för **SwiftUI**. Du kommer inte att skapa enhets test eller UI-tester under den här snabb starten. Avmarkera **ta med enhets test** och avmarkera **Inkludera UI-tester**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Skärm bild som visar fönstret Skapa nytt nytt projekt i Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installera paketet och beroenden med CocoaPods

1. Skapa en Podfile för ditt program, så här:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.5'
     pod 'AzureCommunication', '~> 1.0.0-beta.5'
     pod 'AzureCore', '~> 1.0.0-beta.5'
   end
   ```

2. Kör `pod install`.
3. Öppna `.xcworkspace` med Xcode.

### <a name="request-access-to-the-microphone"></a>Begär åtkomst till mikrofonen

För att få åtkomst till enhetens mikrofon måste du uppdatera appens informations egenskaps lista med en `NSMicrophoneUsageDescription` . Du anger det associerade värdet till en `string` som ska ingå i dialog rutan som systemet använder för att begära åtkomstbegäran från användaren.

Högerklicka på `Info.plist` posten för projekt trädet och välj **öppna som**  >  **källkod**. Lägg till följande rader i avsnittet på den översta nivån `<dict>` och spara sedan filen.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Öppna projektets **ContentView. SWIFT** -fil och Lägg till en `import` deklaration överst i filen för att importera `AzureCommunicationCalling library` . Dessutom `AVFoundation` behöver vi detta för att få en begäran om ljud behörighet i koden.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar klient bibliotek för iOS.


| Namn                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient är den huvudsakliga start punkten för det anropande klient biblioteket.|
| ACSCallAgent | ACSCallAgent används för att starta och hantera samtal. |
| CommunicationUserCredential | CommunicationUserCredential används som token-autentiseringsuppgifter för att instansiera CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier används för att representera identiteten för användaren som kan vara något av följande: CommunicationUser/telefonnummer/CallingApplication. |

> [!NOTE]
> När du implementerar händelse ombud måste programmet innehålla en stark referens till de objekt som kräver händelse prenumerationer. Till exempel, när ett `ACSRemoteParticipant` objekt returneras när du anropar `call.addParticipant` -metoden och programmet anger att ombudet ska lyssna på `ACSRemoteParticipantDelegate` , måste programmet ha en stark referens till `ACSRemoteParticipant` objektet. Annars, om det här objektet samlas in, genererar delegaten ett allvarligt undantag när anrops-SDK: n försöker anropa objektet.

## <a name="initialize-the-acscallagent"></a>Initiera ACSCallAgent

Om du vill skapa en `ACSCallAgent` instans från `ACSCallClient` måste du använda `callClient.createCallAgent` metoden som asynkront returnerar ett `ACSCallAgent` objekt när den har initierats

För att skapa en samtals klient måste du skicka ett `CommunicationUserCredential` objekt.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Skicka CommunicationUserCredential-objekt som skapats ovan till ACSCallClient och ange visnings namnet.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()
options.displayName = "ACS iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions,
    completionHandler: { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Placera ett utgående samtal

Om du vill skapa och starta ett samtal måste du anropa ett av API: erna på `ACSCallAgent` och tillhandahålla kommunikations tjänst identiteten för en användare som du har etablerad med klient biblioteket för hantering av kommunikations tjänster.

Skapande av anrop och start är synkront. Du får en samtals instans som gör att du kan prenumerera på alla händelser på samtalet.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Placera ett 1:1-anrop till en användare eller 1: n Anrop till användare och PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Placera ett 1: n-anrop med användare och PSTN
För att kunna ringa till PSTN måste du ange telefonnummer som har hämtats med kommunikations tjänster
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Placera ett 1:1-samtal med video
Om du vill hämta en instans av enhets hanteraren kan du läsa [här](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Anslut till ett grupp anrop
För att kunna ansluta till ett samtal måste du anropa ett av API: erna på *CallAgent*

```swift

let groupCallContext = GroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: JoinCallOptions())

```

### <a name="accept-an-incoming-call"></a>Acceptera ett inkommande samtal
Om du vill acceptera ett anrop anropar du metoden accept på ett Call-objekt.
Ange ett ombud för CallAgent 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions,
                          completionHandler: { (error) in
                           if error == nil {
                               print("Incoming call accepted")
                           } else {
                               print("Failed to accept incoming call")
                           }
                       })
} else {
   print("No incoming call found to accept")
}
```

## <a name="push-notification"></a>Push-meddelande

Mobilt push-meddelande är det popup-meddelande du får i den mobila enheten. För att anropa, kommer vi att fokusera på VoIP (Voice-of-Internet Protocol) push-meddelanden. Vi kommer att erbjuda dig de funktioner som du kan registrera för push-meddelanden, för att hantera push-meddelanden och avregistrera push-meddelanden.

### <a name="prerequisite"></a>Förutsättning

- Steg 1: Xcode-> signering & funktioner – > Lägg till kapacitet-> "push-meddelanden"
- Steg 2: Xcode-> signering & funktioner – > Lägg till kapacitet-> bakgrunds lägen
- Steg 3: "bakgrunds lägen" – > väljer "Voice över IP" och "fjärraviseringar"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Skärm bild som visar hur du lägger till funktioner i Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Registrera dig för push-meddelanden

För att registrera för push-meddelanden anropar du registerPushNotification () på en *CallAgent* -instans med en token för enhets registrering.

Registrera dig för push-meddelanden måste anropas efter lyckad initiering. När `callAgent` objektet förstörs, `logout` anropas då automatiskt avregistrera push-meddelanden.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken,
                completionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Hantering av push-meddelanden
För att ta emot push-meddelanden för inkommande samtal anropar du *handlePushNotification ()* på en *CallAgent* -instans med en ord lista.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(payload: dictionaryPayload, completionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Avregistrera push-meddelanden

Program kan avregistrera push-meddelanden när som helst. Anropa bara `unRegisterPushNotification` metoden på *CallAgent*.
> [!NOTE]
> Program avregistreras inte automatiskt från push-meddelanden vid utloggning.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Medel anrops åtgärder

Du kan utföra olika åtgärder under ett anrop för att hantera inställningar som rör video och ljud.

### <a name="mute-and-unmute"></a>Ljud av och på

Om du vill stänga av eller stänga av den lokala slut punkten kan du använda de `mute` `unmute` asynkrona API: erna:

```swift
call!.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Asynkron Lokal avljud

```swift
call!.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Starta och stoppa sändning av lokal video

Om du vill börja skicka en lokal video till andra deltagare i samtalet använder du `startVideo` API och pass `localVideoStream` med `camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

När du börjar skicka video `ACSLocalVideoStream` läggs instansen till i `localVideoStreams` samlingen på en anrops instans:

```swift

call.localVideoStreams[0]

```

Asynkron Om du vill stoppa den lokala videon skickar du `localVideoStream` tillbaka den från anropet av `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Hantering av fjärranslutna deltagare

Alla fjärranslutna deltagare representeras av `ACSRemoteParticipant` typen och är tillgängliga via `remoteParticipants` samlingen på en anrops instans:

### <a name="list-participants-in-a-call"></a>Lista deltagare i ett samtal

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Egenskaper för fjärran sluten part

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Lägg till en deltagare i ett samtal

Om du vill lägga till en deltagare till ett samtal (antingen en användare eller ett telefonnummer) kan du anropa `addParticipant` . En fjärran sluten instans kan returneras synkront.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Ta bort en deltagare från ett samtal
Om du vill ta bort en deltagare från ett samtal (antingen en användare eller ett telefonnummer) kan du anropa  `removeParticipant` API: et. Detta kommer att lösa asynkront.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Återge video strömmar för fjärranslutna deltagare

Fjärranslutna deltagare kan starta video-eller skärm delning under ett samtal.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Hantera data strömmar för fjärran sluten video/skärm delning

För att visa en lista över strömmar av fjärranslutna deltagare, inspektera `videoStreams` samlingarna:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Egenskaper för fjärran sluten video ström

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Rendera fjärran sluten deltagare

Starta åter givning av fjärranslutna deltagare:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Metoder och egenskaper för video åter givning

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Enhetshantering

`DeviceManager` gör att du kan räkna upp lokala enheter som kan användas i ett anrop för att överföra ljud-och video strömmar. Du kan också begära behörighet från en användare för att få åtkomst till mikrofonen/kameran. Du har åtkomst till `deviceManager` `callClient` objektet:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Räkna upp lokala enheter

Du kan använda uppräknings metoder på Enhetshanteraren för att komma åt lokala enheter. Uppräkning är en synkron åtgärd.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Ange standard mikrofon/talare

Med enhets hanteraren kan du ange en standardenhet som ska användas när ett samtal startas. Om inga stack-standardvärden har angetts, kommer kommunikations tjänsterna att återgå till standardinställningarna för operativ systemet.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Lokal kamera för hands version

Du kan använda `ACSRenderer` för att börja rendera en ström från den lokala kameran. Den här data strömmen skickas inte till andra deltagare. Det är en lokal förhands gransknings matning. Detta är en asynkron åtgärd.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Egenskaper för för hands version av lokal kamera

Åter givningen har en uppsättning egenskaper och metoder som gör att du kan styra åter givningen:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Händelse modell

Du kan prenumerera på de flesta egenskaper och samlingar för att få ett meddelande när värdena ändras.

### <a name="properties"></a>Egenskaper
Så här prenumererar du på `property changed` händelser:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Samlingar
Så här prenumererar du på `collection updated` händelser:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
