# BeHealthy

## Version history

| Version | Release date |
| - | - |
| 1.0.1 | Jul 18th, 2022|
| 1.2.2 | Oct 13rd, 2022|


## Requirements

* Minimum OS version: 6.0
* Designed for cellphone interface
* Supports only portrait orientation
* Supports only light mode


## How to install library

In the settings.gradle file of your project add maven with the url of Jitpack.io

```
    repositories {
        google()
        mavenCentral()
        jcenter() // Warning: this repository is going to shut down soon
        
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

First in your project/build.gradle add the dependencies, to use Kotlin, Hilt and Crashlytics

```
buildscript {
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
        classpath 'com.google.firebase:firebase-crashlytics-gradle:$crashlytics_version'
    }
}
plugins {
    id 'com.android.application' version '7.2.0' apply false
    id 'com.android.library' version '7.2.0' apply false
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

**Important:** Enabling the use of Hilt in your application does not limit dependency injection, you can use the injection tool of your choice.


## Full access

To embed full content (login + progress + achievements) of BeHealthy framework

### Kotlin

```
@Inject
lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startBeHealthy()
}
```

### Java

```
@Inject
BeHealthyClient client;

void someMethod() {
  // additional logic
  
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


## Google Fit configuration

**Create a Project**

Firstable, create a new project from Google Cloud Platform, to do this, log in https://console.cloud.google.com/, once logged in, in the top bar the project should appear, otherwise you should see the option create new project.

<img width="753" alt="e8761297-e1fd-4d7b-8cd3-7aec142d4733" src="https://user-images.githubusercontent.com/106997285/180486763-fe144cea-eabf-4212-99ff-9c47a194726b.png">

**Enable the Google Fit API**

Once the project is created, it is necessary to activate the use of the "Fitness API" service, to do this in the search engine look for "Fitness", it will appear as the first option

![0fa6ecf1-8119-45d3-bd03-3e72dc2a5137](https://user-images.githubusercontent.com/106997285/180486789-8be30697-1c58-4522-abce-5fa14d6958e3.png)


When you select it a new screen will appear with an Enable Fitness API button, when it's activated you should see a screen like this: 

![4be4cbbc-03e3-42a0-b639-baecad6b739c](https://user-images.githubusercontent.com/106997285/180486808-4cd13a8c-a0f2-476a-881a-81c1f5021c40.png)


**Authorization scopes**

**When to go through verification**

You need to go through verification before you launch a user-facing app. You can continue to build and test your app while waiting to complete verification. When your app is successfully verified, the unverified app screen is removed from your client.
You don't need to go through verification for the following kinds of apps:
- Apps in development: if your app is experimental or a test build, you don't need to go through verification unless you decide to launch it to the public.
- OAuth-based plugins: if you're setting up an OAuth-based plugin for a popular platform, such as SMTP for WordPress, you don't need to go through the verification process.
- Internal apps: if your app is an internal web app for users in the same G Suite domain and the app is associated with a Cloud Organization that all of your users belong to, you don't need to go through verification. For more information, see public and internal applications.

**All apps**

Steps to prepare for verification

All apps that request access to data using Google APIs must complete brand verification. Make sure all branding information on the OAuth consent screen, such as the project name shown to users, support email, homepage URL, privacy policy URL, and so on, accurately represents the app's identity.

Make sure that your homepage meets the following requirements:
- Your homepage must be publicly accessible, and not behind a sign-in page.
- Your homepage must make clear its relevance to the app you’re verifying.
- Your homepage must be accurate, inclusive, and easily accessible to all users.
- Links to the Google Play Store or Facebook are not considered valid application homepages.

Make sure that your app's Privacy Policy meets the following requirements:

- The Privacy Policy must be visible to users, hosted within the domain of your website, and linked from the OAuth consent screen on the Google API Console.
- The Privacy Policy must disclose the manner in which your application accesses, uses, stores, or shares Google user data. Your use of Google user data must be limited to the practices disclosed in your published Privacy Policy.

**Apps requesting sensitive scopes**

Steps for apps requesting sensitive scopes

1. Complete the preparation steps for All apps.

2. Prepare a detailed justification for each requested scope as well as an explanation for why a narrower scope wouldn't be sufficient. For example: My app will use https://www.googleapis.com/auth/calendar to show a user's Google calendar data on the scheduling screen of my app, so that users can manage their schedules through my app and sync the changes with their Google calendar.

3. Your requested scope must be as granular as possible (if your requested scope goes beyond the usage needed, then we will either reject your request or suggest a more applicable scope).
- Prepare a video that fully demonstrates the OAuth grant process by users and shows, in detail, the usage of sensitive scopes in the app.
- Show that the OAuth Consent Screen correctly displays the App Name.
- Show the OAuth grant process that users will experience, in English (the consent flow, and, if you use Google Sign-in, the sign-in flow).
- Show that the URL bar of the OAuth Consent Screen correctly includes your app’s Client ID.
Note: This is not required for chrome extensions, native Android, and iOS apps.
Show how the data will be used by demonstrating the functionality enabled by each sensitive and restricted scope you request.

4. Upload the video to YouTube. You’ll need to provide a link to the video as part of the verification process. Let us know if your app requires registration or features a local login. If any of your OAuth clients are not ready for production, we suggest you delete or remove them from the project requesting verification. You can do this in the Google Cloud Console.

**How is this info presented to users?**

This is the consent screen that users see

<img width="894" alt="Screen Shot 2022-09-22 at 1 57 12 PM" src="https://user-images.githubusercontent.com/105304517/191855272-389eac49-5efa-4327-b0de-afd3083ccd1a.png">

**How to send a project to verification?**

Go to Google Cloud Console, select your project and select APIs & Services option

<img width="1792" alt="Screen Shot 2022-09-22 at 3 11 33 PM" src="https://user-images.githubusercontent.com/105304517/191855726-a7c69cb3-aff2-4877-a25f-bb0326754b95.png">

If you have an OAuth 2.0 client ID associated with your project you can skip this section, otherwise, select Credentials section and create a new credential, select OAuth client ID option

<img width="1792" alt="Screen Shot 2022-09-22 at 3 19 05 PM" src="https://user-images.githubusercontent.com/105304517/191858066-d5843e59-0497-40ac-8364-a17e65089706.png">

For Application Type select Android, and fill the other fields of the form with the following information:

<img width="1792" alt="Screen Shot 2022-09-22 at 3 20 37 PM" src="https://user-images.githubusercontent.com/105304517/191855804-bca2415a-9d2e-4431-9185-0803c65ce214.png">

**Name:** the name you want for client ID, it’ll be better if it’s something related with production or release
**Package Name:** package name of your Android application
**SHA-1:** this information can be obtained from your Google Play Console, select the app and go to Setup > App Integrity > App Signing

After you create your OAuth Client ID, go to the OAuth consent screen section, click EDIT APP and follow the instructions in order to send the project to verification.

<img width="1792" alt="Screen Shot 2022-09-22 at 3 31 02 PM" src="https://user-images.githubusercontent.com/105304517/191855924-ade694ec-46b3-42df-82ae-e150ad0ab6a2.png">

Scopes to send to verification

<img width="519" alt="Screen Shot 2022-09-22 at 2 52 47 PM" src="https://user-images.githubusercontent.com/105304517/191856570-ccd66957-abe6-400f-bcf9-f6c2e3dc003d.png">

Verification process can take up to 4-6 weeks.


## Fitbit configuration

**Register for a Fitbit Dev Account**

1. Go to https://dev.fitbit.com/apps and create an account. You'll need to use a valid email address and confirm it before continuing.

2. Create a Fitbit App
  a. Open https://dev.fitbit.com/apps/new
  b. Fill out the fields about your app. The first five fields (app name, description, website, organization, and organization website) will be specific to your organization and are going to be visible for your users when they grant permissions inside the app.

Important:
  For OAuth 2.0 Application Type select Client
  Redirect URL: tells Fitbit where to take the user once they have authenticated. It is **important** that you use the following redirect 
  
```
    com.globant.behealthy://redirect
```

![249bc9ee-1a1a-4db3-9a62-64b5cb929818](https://user-images.githubusercontent.com/106997285/180487358-a2aec5c4-eab0-4997-80a4-f5b78cde2702.png)


Agree and click Register. You should get a screen with your `Client Id`. Copy this client id you are going to need it later.

**Configuration in your project**

Now you need to create a new file called **fitbit_auth_config.json** and add it to your project raw folder, you can find it in app/res/raw, if it doesn't exist you have to create it

Now replace client-id by your project information 

```
{
  "client-id": "XXXXX",
  "authorization-uri": "https://www.fitbit.com/oauth2/authorize",
  "token-uri": "https://api.fitbit.com/oauth2/token",
  "redirect-uri": "com.globant.behealthy://redirect",
  "authorization-scope": "activity heartrate profile weight"
}
```


## Troubleshooting
