
# README for Git

This guide will walk you through the installation and configuration of the WebView component for integrating KinesteX workouts into your app.

## Configuration

#### AndroidManifest.xml

Add the following permissions for camera and microphone usage:

```xml
<!-- Add this line inside the <manifest> tag -->
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.VIDEO_CAPTURE" />

```

#### Info.plist

Add the following keys for camera and microphone usage:

```xml
<key>NSCameraUsageDescription</key>
<string>Camera access is required for video streaming.</string>
<key>NSMicrophoneUsageDescription</key>
<string>Microphone access is required for video streaming.</string>
```

### Available categories and sub categories to sort workouts: 

The empty spaces in urls are formatted like this %20 so if you want to sort the workout based on the sub_category copy the sub _category as specified below

| **Category** | **Sub-category** |
| --- | --- |
| **Fitness** | Stay%20Fit, Stretching, Cardio |
| **Rehabilitation** | Back%20Relief, Knee%20Therapy, Neck%20Relief |

## Available parameters:
```jsx
userId = "123abcd"; // Replace this with the actual user ID from your data source
age = 25; // Replace this with the actual age from your data source
gender = "male"; // Replace this with the actual gender from your data source
weight = 70; // Replace this with the actual weight from your data source
sub_category = "Stay%20Fit"; //The spaces in url values have to have "%20" in them (You can pass multiple sub categories, 
//sub_category = 'Stay%20Fit,Knee%20Therapy,Cardio' (They have to be separated by a comma without a space) 
category = "Fitness"; // You can only have one category
goals = "Weight Management"; // Replace this with the actual goal (COMING SOON)
// multiple goals ex:  goals = 'Weight Management, Mental Health'
```
### Handling responses from KinesteX:
Currently supported communication is via web postMessages. You can add a listener to the webview events. See specifications for your language below, but generally we let you know when user completes following events:

  ```jsx
    //finished the workout and now redirected to the all workouts section
    if (message.type === "finished_workout") {
      console.log("Received data:", message.data);
    /*
    Format of the Received data:
    {
    date = "2023-06-09T17:34:49.426Z";
    totalCalories = "0.96";
    totalRepeats = 3;
    totalSeconds = 18;
    userId = 123abcd;
    workout = "Fitness Lite";
    }
    */
    
    }
   
    if (message.type === "exitApp"){
      //clicked on exit, so handle the exit flow by removing WebView
      
    }
       if (message.type === "error_occured") {
       // saved workout data in case of an error that causes ui freeze
      console.log("Received data:", message.data);
     
    }
    if (message.type === "exercise_completed") {
      // (Optional)
      // saved exercise data in case you want to cache the data of each exercise
      console.log("Received data:", message.data);  
      /*
      Format:
      {
      exercise: "Overhead Arms Raise";
      repeats: 20;
      timeSpent: 30;
      calories: "5.0";
      }
      */

    }
```

------------------
## Usage React Native
### Installation

Install `react-native-webview`:

```bash
npm install react-native-webview
```

### App.tsx or App.js

Import the WebView component and use it in your app:

```jsx
import WebView from 'react-native-webview';

// ...
 return (
    <SafeAreaView style={styles.container}>
      {!showWebView && (
        <Button title="Open WebView" onPress={toggleWebView} />
        
      )}
      {showWebView && (
        <WebView
          source={{ uri: `https://kineste-x-w.vercel.app?id=${userId}` }}
          style={styles.webView}
          allowsFullscreenVideo={true}
          mediaPlaybackRequiresUserAction={false}
          onMessage={handleMessage}
          javaScriptEnabled={true}
          originWhitelist={['*']}
          mixedContentMode="always"
          debuggingEnabled={true}
          allowFileAccessFromFileURLs={true}
          allowUniversalAccessFromFileURLs={true}
          allowsInlineMediaPlayback={true}
          geolocationEnabled={true}
        />
      )}
    </SafeAreaView>
  );
// ...
```

To include additional data, such as user ID, age, gender, and weight, update the `uri` prop as shown below:
```jsx
const userId = '123abcd'; // Replace this with the actual user ID from your data source
const age = 25; // Replace this with the actual age from your data source
const gender = 'male'; // Replace this with the actual gender from your data source
const weight = 70; // Replace this with the actual weight from your data source
const sub_category = 'Stay%20Fit'; //The spaces in url values have to have "%20" in them (You can pass multiple sub categories, 
//ex: sub_category = 'Stay%20Fit,Knee%20Therapy,Cardio' (They have to be separated by a comma without a space) 
const category = 'Fitness'; // You can only have one category
const goals = 'Weight Management'; // Replace this with the actual goal (COMING SOON)
// multiple goals ex:  goals = 'Weight Management, Mental Health'

<WebView
  source={{ uri: `https://myweb.vercel.app?userId=${userId}&age=${age}&gender=${gender}&weight=${weight}&sub_category=${sub_category}&category=$category}` }}
  // ...other WebView props
/>
```

### Handling the Exit Event

Add the following code to handle the exit event when the user clicks the exit button:

```jsx
 const handleMessage = (event: WebViewMessageEvent) => {
  try {
    const message = JSON.parse(event.nativeEvent.data);
    //finished the workout and now redirected to the all workouts section
    if (message.type === "finished_workout") {
      console.log("Received data:", message.data);
    /*
    Format of the Received data:
    {
    date = "2023-06-09T17:34:49.426Z";
    totalCalories = "0.96";
    totalRepeats = 3;
    totalSeconds = 18;
    userId = 123abcd;
    workout = "Fitness Lite";
    }
    */
    
    }
   
    if (message.type === "exitApp"){
      //clicked on exit, so handle the exit flow by removing WebView 
      toggleWebView();
    }
       if (message.type === "error_occured") {
       // saved workout data in case of an error that causes ui freeze
      console.log("Received data:", message.data);
      // close the webview 
          toggleWebView();
    }
    if (message.type === "exercise_completed") {
      // (Optional)
      // saved exercise data in case you want to cache the data of each exercise
      console.log("Received data:", message.data);  
      /*
      Format:
      {
      exercise: "Overhead Arms Raise";
      repeats: 20;
      timeSpent: 30;
      calories: "5.0";
      }
      */

    }
  } catch (e) {
    console.error("Could not parse JSON message from WebView:", e);
  }
};

// ...
 return (
    <SafeAreaView style={styles.container}>
      {!showWebView && (
        <Button title="Open WebView" onPress={toggleWebView} />
        
      )}
      {showWebView && (
        <WebView
          source={{ uri: `https://kineste-x-w.vercel.app/?userId=${userId}` }}
          style={styles.webView}
          allowsFullscreenVideo={true}
          mediaPlaybackRequiresUserAction={false}
          onMessage={handleMessage}
          javaScriptEnabled={true}
          originWhitelist={['*']}
          mixedContentMode="always"
          debuggingEnabled={true}
          allowFileAccessFromFileURLs={true}
          allowUniversalAccessFromFileURLs={true}
          allowsInlineMediaPlayback={true}
          geolocationEnabled={true}
        />
      )}
    </SafeAreaView>
  );
// ...
```
------------------
## Usage iOS native (SwiftUI)

1. Make sure to configure necessary Info.plist properties 

```xml
<key>NSCameraUsageDescription</key>
<string>Camera access is required for video streaming.</string>
<key>NSMicrophoneUsageDescription</key>
<string>Microphone access is required for video streaming.</string>
```

2. Create a WebView UIViewRepresentable to show our workout collection: 

```swift
import SwiftUI
import WebKit

struct WebView: UIViewRepresentable {
    let url: String
    @ObservedObject var viewModel: ContentViewModel

    func makeUIView(context: Context) -> WKWebView  {
        let contentController = WKUserContentController()

        let preferences = WKPreferences()
        // Make sure to enable javascript
        preferences.javaScriptEnabled = true

        let config = WKWebViewConfiguration()
        config.userContentController = contentController
        config.preferences = preferences
        // Make sure to enable inLineMediaPlayback for videos to play automatically
        config.allowsInlineMediaPlayback = true

        let webView = WKWebView(frame: .zero, configuration: config)
        webView.navigationDelegate = context.coordinator
        contentController.add(context.coordinator, name: "listener")

        if let url = URL(string: self.url) {
            webView.load(URLRequest(url: url))
        }
        return webView
    }

    func updateUIView(_ uiView: WKWebView, context: Context) {}

    func makeCoordinator() -> Coordinator {
        Coordinator(self)
    }

    class Coordinator: NSObject, WKNavigationDelegate, WKScriptMessageHandler {
        var parent: WebView

        init(_ parent: WebView) {
            self.parent = parent
        }

        func userContentController(_ userContentController: WKUserContentController, didReceive message: WKScriptMessage) {
            if message.name == "listener", let messageBody = message.body as? String {
                DispatchQueue.main.async {
                    self.parent.viewModel.message = messageBody
                }
            }
        }
    }
}

```

3. Create a class that will handle incoming data: 
```swift
import SwiftUI

class ContentViewModel: ObservableObject {
    @Published var showWebView: Bool = false
    @Published var message: String = "" {
        didSet {
            handle(message: message)
        }
    }

    func handle(message: String) {
        guard let data = message.data(using: .utf8),
              let json = try? JSONSerialization.jsonObject(with: data, options: []) as? [String: Any],
              let type = json["type"] as? String
        else {
            print("Could not parse JSON message from WebView.")
            return
        }
       

        switch type {
        case "finished_workout":
            print("----------------------------------\nWorkout finished, data received: ", "\(json["data"] ?? "")\n----------------------------------")
            
        case "error_occured":
            print("There was an error: ", json["data"] ?? "")
            
        case "exercise_completed":
            print("Exercise completed: ", json["data"] ?? "")
           
        case "exitApp":
            showWebView = false
        
        default:
            break
        }
    }
}

```
Present the WebView and setup your url query params: 
```swift
import SwiftUI

struct ContentView: View {
    @ObservedObject private var viewModel = ContentViewModel()
    var userId = "123abcd"
    var sub_category = "Cardio"
    var category = "Fitness"
    var body: some View {
        VStack {
            if viewModel.showWebView {
                WebView(url: "https://kineste-x-w.vercel.app/?userId=\(userId)&sub_category=\(sub_category)&category=\(category)", viewModel: viewModel)
               
            } else {
                Button(action: {
                    self.viewModel.showWebView.toggle()
                }) {
                    Text("Open KinesteX").foregroundColor(.white).bold().shadow(radius: 15).padding(30).background(
                        Color.green.cornerRadius(20)
                    )
                }
            }
        }
    }
}

```

------------------
## Usage React Progressive Web App:

Parameters:
```jsx
  const [showWebView, setShowWebView] = useState(false);
  const toggleWebView = () => setShowWebView(!showWebView);

 const userId = '123abcd'; // Replace this with the actual user ID from your data source
const age = 25; // Replace this with the actual age from your data source
const gender = 'male'; // Replace this with the actual gender from your data source
const weight = 70; // Replace this with the actual weight from your data source
const sub_category = 'Stay%20Fit'; //The spaces in url values have to have "%20" in them (You can pass multiple sub categories, 
//ex: sub_category = 'Stay%20Fit,Knee%20Therapy,Cardio' (They have to be separated by a comma without a space) 
const category = 'Fitness'; // You can only have one category
const goals = 'Weight Management'; // Replace this with the actual goal (COMING SOON)
// multiple goals ex:  goals = 'Weight Management, Mental Health'
```

Handling postMessages from KinesteX:
```jsx
const handleMessage = (event) => {
    try {
      if (event.data) {
        const message = JSON.parse(event.data);
  
        console.log('Received data:', message); // Log the received data
  
        if (message.type === 'finished_workout') {
          console.log('Received data:', message.data);
          /*
          Format of the Received data:
          {
            date: "2023-06-09T17:34:49.426Z",
            totalCalories: "0.96",
            totalRepeats: 3,
            totalSeconds: 18,
            userId: "123abcd",
            workout: "Fitness Lite"
          }
          */
        }
        if (message.type === 'exitApp') {
          toggleWebView();
        }
        if (message.type === 'error_occured') {
          console.log('Received data:', message.data);
          toggleWebView();
        }
        if (message.type === 'exercise_completed') {
          console.log('Received data:', message.data);
          /*
          Format:
          {
            exercise: "Overhead Arms Raise",
            repeats: 20,
            timeSpent: 30,
            calories: "5.0"
          }
          */
        }
      } else {
        console.log('Received empty message'); // Log if the message is empty
      }
    } catch (e) {
      console.error('Could not parse JSON message from WebView:', e);
    }
  };
  
  useEffect(() => {
    const handleWindowMessage = (event) => {
      handleMessage(event);
    };

    window.addEventListener('message', handleWindowMessage);

    return () => {
      window.removeEventListener('message', handleWindowMessage);
    };
  }, [toggleWebView]); // <- Add toggleWebView here

```

Displaying KinesteX:
```jsx
   {showWebView && (
        <div
          style={{
            position: 'fixed',
            top: '0',
            left: '0',
            width: '100%',
            height: '100%',
            zIndex: '0',
          }}
        >
          <iframe
            src={`https://kineste-x-w.vercel.app?userId=${userId}&age=${age}&gender=${gender}&weight=${weight}&sub_category=${sub_category}&category=${category}`}
            frameBorder="0"
            allow="camera; microphone; autoplay"
            sandbox="allow-same-origin allow-scripts"
            allowFullScreen={true}
            style={{
              width: '100%',
              height: '100%',
            }}
          ></iframe>
        </div>
      )}
```

See PWA-KinesteX for a demo code

------------------
## Usage Flutter: 

1. Add necessary permissions and libraries to AndroidManifest, Info.plist, and pubspec.yaml
```xml
AndroidManifest.xml:

<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.VIDEO_CAPTURE" />

Info.plist:

<key>NSCameraUsageDescription</key>
<string>Camera access is required for video streaming.</string>
<key>NSMicrophoneUsageDescription</key>
<string>Microphone access is required for video streaming.</string>

pubspec.yaml:

permission_handler: ^9.0.0
flutter_inappwebview: ^5.3.2
```

2. Request camera access: 
```dart
// Example usage: 
Future<void> main() async {

  WidgetsFlutterBinding.ensureInitialized();
// ensure that camera access is granted before opening the webview
 if (await Permission.camera.request() == PermissionStatus.granted) {

 runApp(MyApp());

 } else {

  print("Permission not granted");

 }

}

```


3. Specify parameters: 
```dart
String url = "";
String userId = "abc123";
String category = "Fitness";

// see other available parameters above
 
```
4. Show Webview: 

```dart

class _MyHomePageState extends State<MyHomePage> {
  String userId = "abc123";
  String category = "Fitness";
  String url = "";
  bool showWebview = false;
  InAppWebViewController? _controller;

  late InAppWebViewGroupOptions options;

  @override
  void initState() {
    super.initState();
    url = "https://kineste-x-w.vercel.app/?userId=$userId&category=$category";

    options = InAppWebViewGroupOptions(
      crossPlatform: InAppWebViewOptions(
        javaScriptEnabled: true,
        mediaPlaybackRequiresUserGesture: false,
      ),
      android: AndroidInAppWebViewOptions(
        useHybridComposition: true,
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('WebView Camera Access'),
      ),
      body: showWebview
          ? InAppWebView(
        initialOptions: options,
        initialUrlRequest: URLRequest(url: Uri.parse(url)),
        onWebViewCreated: (InAppWebViewController controller) {
          _controller = controller;

          controller.addJavaScriptHandler(
            handlerName: 'messageHandler',
            callback: (args) {
              handleMessage(args[0]);
            },
          );
        },
        onLoadStop: (InAppWebViewController controller, Uri? url) async {
          await controller.evaluateJavascript(source: """
                  window.addEventListener('message', (event) => {
                    if (event.data === 'exitApp') {
                      window.flutter_inappwebview.callHandler('exitApp');
                    } else {
                      window.flutter_inappwebview.callHandler('messageHandler', event.data);
                    }
                  });
                """);
        },
        onConsoleMessage: (controller, consoleMessage) {
          print(consoleMessage);
        },
        onProgressChanged: (InAppWebViewController controller, int progress) {
          print("WebView is loading (progress : $progress%)");
        },
       // IMPORTANT: HANDLE PERMISSION AT RUN TIME 
        androidOnPermissionRequest:
            (InAppWebViewController controller, String origin,
            List<String> resources) async {
          return PermissionRequestResponse(
              resources: resources,
              action: PermissionRequestResponseAction.GRANT);
        },
      )
          : Center(
        child: ElevatedButton(
          onPressed: () {
            setState(() {
              showWebview = true;
            });
          },
          child: Text('Start'),
        ),
      ),
    );
  }

  // handling postMessages from KinesteX. 
  void handleMessage(String message) {
    var parsedMessage = jsonDecode(message);

    print("Received data: ${parsedMessage['data']}");
    switch (parsedMessage['type']) {
      case "finished_workout":
        // called when user clicks on finish workout button. returns statistics from the exercise
        print("Received data: ${parsedMessage['data']}");
        break;

      case "exitApp":
       // when user clicks on exit button 
        print("Received data: ${parsedMessage['data']}");
        if (showWebview) {
          setState(() {
            showWebview = false;
          });
        }
        break;

      case "error_occured":
        // called when errors occur on the webpage during the workout
        print("Received data: ${parsedMessage['data']}");
        break;
      
      case "exercise_completed":
        // called when user completed the exercise and goes to the next exercise
        print("Received data: ${parsedMessage['data']}");
        break;
    }
  }
}


```
The demo app is called flutter_sdk. All code is in main.dart
------------------


All workout data will be sent when the user clicks the exit button. The data will also be stored in our database and can be made available to your app in real-time upon request (in case the user exited the flow incorrectly or something unexpected happened, we can discuss use cases)

Please note that some parts are under development and testing. Contact vladimir@kinestex.com to schedule a demo and try out a beta version of KinesteX. We would appreciate any feedback!
```
