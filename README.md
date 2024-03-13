# BeHealthy

## Version history

| Version | Release date |
| - | - |
| 1.0.1 | Jul 18th, 2022|
| 1.2.2 | Oct 13rd, 2022|
| 1.3.1 | Nov 25th, 2022|
| 1.3.4 | Dec 13rd, 2022|
| 1.3.5 | Dec 21st, 2022|
| 1.4.0 | Mar 11th, 2023|
| 1.4.1 | Mar 14th, 2023|
| 2.1.1 | Oct 4th, 2023|


## Requirements

* Minimum OS version: 9.0
* Designed for cellphone interface
* Supports only portrait orientation
* Supports only light mode


## How to install library

In the settings.gradle file of your project add maven with the url of Jitpack.io

```
    repositories {
        google()
        mavenCentral()
        jcenter()
        
        // Add this repository
        maven {
            url "https://jitpack.io"
        }
    }
```

Now with the Jitpack repository, you can deploy BeHealthy as a new dependency, in your module (app-level) Gradle file (usually app/build.gradle)

```
implementation("com.github.g-behealthy:BeHealthy-Android-Framework:1.2.3@aar"){transitive true}
```

Note: BeHealthy makes use of external libraries so it is necessary to use the transitive true flag to obtain them.


## Dependency injection

For the right functioning of the application, it is necessary to add code that enables the use of Hilt for our dependency injection, as well as to activate the use of WorkManager.

In the following points you can see the code, which is mandatory for our library.

First in your project/build.gradle add the dependencies, to use Kotlin, Hilt and Crashlytics (current recommended Hilt version 2.42)

```
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath 'com.google.firebase:firebase-crashlytics-gradle:$crashlytics_version'
    }
}
plugins {
    id 'org.jetbrains.kotlin.android' version '1.6.21' apply false
    id("com.google.dagger.hilt.android") version "$hilt_version apply false
}
```

Then, in your app/build.gradle add the plugins and dependencies

```
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'kotlin-kapt'
    id 'dagger.hilt.android.plugin'
    id 'com.google.firebase.crashlytics'
}

dependencies {
.
.
.

implementation "com.google.dagger:hilt-android:$hilt_version"
kapt "com.google.dagger:hilt-android-compiler:$hilt_version"
kapt "com.google.dagger:hilt-compiler:$hilt_version"
}
```

In the next code we see the use of @HiltAndroidApp to initialize the use of dependency injecting, it is necessary to implement Configuration.Provider and override getWorkManagerConfiguration and pass the previously injected HiltWorkerFactory, to return a new configuration.

**IMPORTANT:** If your project has this flag **minifyEnabled** set true you have to set this line in your proguard rules file

```
# Application classes that will be serialized/deserialized over Gson
-keep class com.globant.behealthylibrary.data.remote.model.** { <fields>; }
```
    

### Kotlin

```
@HiltAndroidApp
class YourClassApplication : Application(), Configuration.Provider {

    @Inject
    lateinit var workerFactory: HiltWorkerFactory
    
    @Inject
    lateinit var client: BeHealthyClient

    override fun onCreate() {
        super.onCreate()
        client.initBeHealthy()
    }

    override fun getWorkManagerConfiguration(): Configuration {
        return Configuration.Builder()
            .setWorkerFactory(workerFactory)
            .build()
    }
}
```

### Java

```
@HiltAndroidApp
public class YourClassApplication extends Application implements Configuration.Provider {

    @Inject
    HiltWorkerFactory workerFactory;

    @Inject
    BeHealthyClient client;

    @Override
    public void onCreate() {
        super.onCreate();

        client.initBeHealthy();
    }

    @NonNull
    @Override
    public Configuration getWorkManagerConfiguration() {
        return new Configuration.Builder()
                .setWorkerFactory(workerFactory)
                .build();
    }
}
```

Now in your manifest you must add the following code, before closing the </application> tag

```
<provider
            android:name="androidx.startup.InitializationProvider"
            android:authorities="${applicationId}.androidx-startup"
            android:exported="false"
            tools:node="remove" />
```   

and set this value

```
android:allowBackup="false"
```

**Important:** Enabling the use of Hilt in your application does not limit dependency injection, you can use the injection tool of your choice.

## Set Environment

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setEnvironment(BeHealthyEnvironment.STAGE)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setEnvironment(BeHealthyEnvironment.STAGE);
}
```

## Full access (Email activation)

To embed full content (login + progress + achievements) of BeHealthy framework for email activation

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  client.setActivationFlow(BeHealthyActivationFlow.EMAIL)
  client.startBeHealthy()
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  client.setActivationFlow(BeHealthyActivationFlow.EMAIL);
  client.startBeHealthy();
}
```


## Full access (SMS activation)

To embed full content (login + progress + achievements) of BeHealthy framework for SMS activation

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  client.setActivationFlow(BeHealthyActivationFlow.SMS)
  client.startBeHealthy()
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  client.setActivationFlow(BeHealthyActivationFlow.SMS);
  client.startBeHealthy();
}
```


## Core access

To embed core content (progress + achievements) of BeHealthy framework

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startProgress(token = AnyToken)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.startProgress(AnyToken);
}
```


## SDK enrollment

To enroll a new user to BeHealthy

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startEnrollment(token = AnyToken)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.startEnrollment(AnyToken);
}
```

You can verify if a user is enrolled with their token:

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.isUserEnrolled("Token")
    .whenCompleteAsync { result, throwable ->
        //result.message prints error message
        
        if result.isEnrollment {
            // present core flow
        } else {
            // present enroll flow
        }
    }
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.isUserEnrolled("TOKEN").whenComplete((result, throwable) -> {
    //result.message prints error message
  
    if result.isEnrollment {
        // present core flow
    } else {
        // present enroll flow
    }
  });
}
```


## Community configuration

Community value is granted to you by BeHealthy team:

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setCommunityId("1")
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setCommunityId("1");
}
```


## Set token

You can provide a token for backend integration, with the parameter "token" in the methods [startProgressApp](#core-access) and [startSDKEnrollmentApp](#sdk-enrollment).
 
Example: 
 
### Kotlin
 
```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startEnrollment(token = AnyToken) <- Here your token
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.startEnrollment(AnyToken);
}
```

## Refresh token
 
### Kotlin
 
```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setRefreshToken("token")
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setRefreshToken("token");
}
```

## Branding

There are 3 supported colors for BeHealthy framework: primary, secondary and tertiary, save colors with the next method.

NOTE: only send the hexadecimal color code without # symbol.

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  val yourColors = SupportColors(
                        primaryColor = '06283D',
                        secondaryColor = '1363DF',
                        tertiaryColor = '47B5FF'
                    )
  
  client.setSupportColors(colors = yourColors)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  SupportColors yourColors = new SupportColors(
                        "06283D",
                        "1363DF",
                        "47B5FF");
  client.setSupportColors(yourColors);
}
```

Additionally, it is necessary to create a new file in res/values with the name colors_override.xml and add the next values 

```
<color name="be_healthy_primary_color">#your primary color</color>
<color name="be_healthy_primary_dark">#your primary color</color>
<color name="be_healthy_secondary_color">#your secondary color</color>
<color name="be_healthy_icon_color">#your secondary color</color>
<color name="be_healthy_secondary_icon_color">#your secondary color</color>
<color name="be_healthy_tertiary_color">#your tertiary color</color>
```
![6c33b01f-86f9-416f-8a44-1943bc7c1d3f](https://user-images.githubusercontent.com/106997285/180485692-11b0a3cf-7577-4680-94e8-975bc867f675.jpeg)


## Push notifications

In order to support push notifications a Firebase project is required. Select the project, go to Project Settings > Cloud Messaging, copy server key and send it to our team.

To the class which extends from **FirebaseMessagingService** add **AndroidEntryPoint** attribute and override **onMessageReceived** and **onNewToken** methods

### Kotlin

```
@AndroidEntryPoint
class MyFirebaseListenerService: FirebaseMessagingService() {
    
    @Inject
    lateinit var client: BeHealthyClient
    
    ...
    
    override fun onMessageReceived(remoteMessage: RemoteMessage) {
        super.onMessageReceived(message)
        cliente.onMessageReceivedClient(message)
    }
    
    override fun onNewToken(token: String) {
        super.onNewToken(token)
        client.onNewTokenClient(token)
    }
}
```

### Java

```
@AndroidEntryPoint
class MyFirebaseListenerService extends FirebaseMessagingService {
    
    @Inject
    BeHealthyClient client;
    
    ...
    
    @Override
    void onMessageReceived(@NonNull RemoteMessage message) {
        ...
        super.onMessageReceived(message);
        cliente.onMessageReceivedClient(message);
    }
    
    @Override
    void onNewToken(@NonNull String token) {
        super.onNewToken(token);
        client.onNewTokenClient(token);
    }
}
```


## Enable / Disable Analytics

Enable analytics:

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.supportFirebaseAnalytics(true)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.supportFirebaseAnalytics(true);
}
```

Disable analytics:

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.supportFirebaseAnalytics(false)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.supportFirebaseAnalytics(false);
}
```


## Account deactivation

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.deactivateAccount("token")
  .whenCompleteAsync { result, throwable ->
        if result {
            // account deactivation success
        } else {
            // account deactivation failed
        }
    }
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.deactivateAccount("token")
  .whenComplete((aBoolean, throwable) -> {
    if aBoolean {
        // account deactivation success
    } else {
        // account deactivation failed
    }
  });
}
```


## Set Enrollment Email

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setEnrollmentEmail("email")
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setEnrollmentEmail("email");
}
```


## Set Program Name

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setProgramName("BeHealthy")
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setProgramName("BeHealthy");
}
```


## Set Buy a Watch CTA

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setUrlBuyWatch("https://www.behealthy.com")
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setUrlBuyWatch("https://www.behealthy.com");
}
```


## Request watch

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.shouldRequestWatch(true)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.shouldRequestWatch(true);
}
```


## Set Member ID

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setMemberId("member id")
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setMemberId("member id");
}
```


## Firebase configuration

[Create a Firebase project](https://firebase.google.com/docs/android/setup#create-firebase-project)

[Register your app with Firebase](https://firebase.google.com/docs/android/setup#register-app)

[Add a Firebase configuration file](https://firebase.google.com/docs/android/setup#add-config-file)

1.- Add the Firebase Android configuration file to your app:

  a.- Click Download google-services.json to obtain your Firebase Android config file (google-services.json).

  b.- Move your config file into the module (app-level) directory of your app.

2.- To enable Firebase products in your app, add the [google-services plugin](https://developers.google.com/android/guides/google-services-plugin) to your Gradle files.

  a.- In your root-level (project-level) Gradle file (build.gradle), add rules to include the Google Services Gradle plugin. Check that you have Google's Maven repository, as well.

<img width="698" alt="f7ed4882-9d0c-4ef6-b278-9fbc99e26900" src="https://user-images.githubusercontent.com/106997285/180486349-191552f1-ecaf-4deb-b84a-4a8e59a15c49.png">


  b.- In your module (app-level) Gradle file (usually app/build.gradle), apply the Google Services Gradle plugin

<img width="675" alt="c2132753-a95c-496f-8e3b-55efed5911ee" src="https://user-images.githubusercontent.com/106997285/180486518-92769a77-4533-4e3c-ad3a-87b6f314a765.png">


If you need more information, please check the official documentation.

https://firebase.google.com/docs/android/setup#register-app


## Health Connect configuration

To register the application to support Health Connect fill this form https://docs.google.com/forms/d/1LFjbq1MOCZySpP5eIVkoyzXTanpcGTYQH26lKcrQUJo/viewform?edit_requested=true&hl=es-419&pli=1

Information to answer:

### Please select all data types your app is requesting to READ:

- WeightRecord
- StepsRecord
- HeightRecord
- ExerciseSessionRecord


### Please provide a rationale for selected data types. If you selected 'N/A' please add N/A as your response.

**HeightRecord:** Fetch the height of the user and display that data as part of the create account flow, where the user must verify this information as well as additional information from the program. This value is used to calculate the target values for steps and heart points.

**WeightRecord:** Fetch the weight of the user and display that data as part of the create account flow, where the user must verify this information as well as additional information from the program. This value is used to calculate the target values for steps and heart points.

**StepsRecord:** Steps are used to display inside the app to the user the number of daily steps which is one of the target values we are currently handling and also to calculate the heart points

**ExerciseSessionRecord:** Exercise session is used to calculate the hearts points inside the app, the application use heart points as one of the targets

### Please select all data types your app is requesting to WRITE

N/A

### Please provide a rationale for selected data types. If you selected 'N/A' please add N/A as your response.

N/A


## Multilanguage support

Framework supports these languages:

* English
* Spanish
* Portuguese
* Arabic

## Set Language

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.setLanguage(BeHealthyLanguage.ENGLISH)
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.setLanguage(BeHealthyLanguage.ENGLISH);
}
```


## Listeners

Listeners are events which occur inside BeHealthy module and you can suscribe, current events supported for Android version are:

- HEART_POINTS_TARGET: Triggered when heart points target is met
- STEPS_TARGET: Triggered when steps target is met
- STAR_EARNED: Triggered when daily star is earned
- SIXTY_PERCENT_STEPS_TARGET: Triggered when step target progress reaches 60%
- NO_TARGET: Triggered when at 5pm local time none of the targets are met

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.onEventChanged { event ->
        when (event) {
            BeHealthyEvents.HEART_POINTS_TARGET -> {
                // do something
            }
        }
    }
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
  client.onEventChanged(event -> {
        switch (event) {
            case HEART_POINTS_TARGET:
                // do something
                break;
        }
    });
}
```


## Send progress

This method is used to send the latest information on the user's HC without having to open the screen completely.
Important:
You need to register health connect first because you will need to request permissions for health connect, you need to add this method in the oncreate of either your activity or fragment where the progress will be sent.
(If your class extends ComponentActivity you must use the parameter composeContext, otherwise if you use an activity that extends AppCompactActivity you must use the parameter context.)
The updateUserProgress method responds with a listener called BeHealthyUpdateProgressListener, which has different statuses that will be enumerated next
ERROR_GET_USER = Error while trying to retrieve user information. Please verify that the user is enrolled in the program or that the token is valid.
ERROR_UPDATE_PROGRESS = There was an error updating the user's progress.
HC_UNAVAILABLE = Health Connect is not available. Please try again later.
HC_NEED_UPDATE_OR_INSTALL = Error with HC, It's necessary to update or install Health Connect.
HC_PERMISSION = Error retrieving HC information. Please verify that the user has HC permissions. Permissions will be requested.
HC_PERMISSION_CANCEL_USER = The user did not grant permissions on multiple occasions. Manual permission granting is required.
SUCCESS = The user information was sent successfully.
If you want BH to control the actions when the user does not have permissions or does not have Health connect installed, you must set the variable performActions to true, if you want to control these actions yourself to customize the permissions screens, just set false in this variable.

### Kotlin

```
val client = IBeHealthyClient.getInstance()
.
.
.
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    client.registerHealthConnect(composeContext = this)
}
fun someMethod(){
    client.updateUserProgress("TOKEN", performActions = true){ response ->
         runOnUiThread {
           //your logic
          }
      }
}
```

### Java

```
IBeHealthyClient client = IBeHealthyClient.getInstance();
.
.
.
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    client.registerHealthConnect(this);
}
public void someMethod() {
    client.updateUserProgress("TOKEN", true, new ResponseCallback() {
        @Override
        public void statusEvent(Response response) {
            runOnUiThread(() -> {
                //your logic
            });
        }
    });
}
```


## Troubleshooting

* Recommended Dagger Hilt version: 2.42
* "You need to use a Theme.AppCompat theme (or descendant) with this activity": update Dagger Hilt dependencies to version 2.42
