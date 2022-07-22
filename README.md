# BeHealthy

## Version history

| Version | Release date |
| - | - |
| | |


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
implementation("com.github.g-behealthy:BeHealthy-Android-Framework:2.8@aar"){transitive true}
```

Note: BeHealthy makes use of external libraries so it is necessary to use the transitive true flag to obtain them.


## Full access

To embed full content (login + progress + achievements) of BeHealthy framework

```
    @Inject
    lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startCompleteApp()
}
```


## Core access

To embed core content (progress + achievements) of BeHealthy framework


```
    @Inject
    lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startProgressApp(token = AnyToken)
}
```


## Community configuration

Community value is granted to you by BeHealthy team:

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


## Set token

You can provide a token for backend integration, with the parameter "token" in the methods [startProgressApp](#core-access)

 and [startSDKEnrollmentApp](#sdk-enrollment).
 
 Example: 
 
```
    @Inject
    lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startSDKEnrollmentApp(token = AnyToken) <- Here your token
}
```


## Branding

There are 3 supported colors for BeHealthy framework: primary, secondary and tertiary, save colors with the next method.

NOTE: only send the hexadecimal color code without # symbol.

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
  
  client.saveSupportColors(colors = yourColors)
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
![Screenshot_2022-06-15-17-35-49-385_com globant behealthy dev](https://github.globant.com/storage/user/8812/files/6c33b01f-86f9-416f-8a44-1943bc7c1d3f)


## Push notifications

In order to support push notifications a Firebase project is required. Select the project, go to Project Settings > Cloud Messaging, copy server key and send it to our team.


## SDK enrollment

To enroll a new user to BeHealthy 

```
@Inject
    lateinit var client: BeHealthyClient
.
.
.

fun someMethod() {
  // additional logic
  
  client.startSDKEnrollmentApp(token = AnyToken)
}
```


## Dependency injection

For the right functioning of the application, it is necessary to add code that enables the use of Hilt for our dependency injection, as well as to activate the use of WorkManager.

In the following points you can see the code, which is mandatory for our library.

First in your app/build.gradle add the dependencies, to use Hilt

```
    // The following dependencies are mandatory
    // Hilt
    implementation "com.google.dagger:hilt-android:$hilt_version"
    kapt "com.google.dagger:hilt-android-compiler:$hilt_version"
    kapt "com.google.dagger:hilt-compiler:$hilt_version"
    // Hilt - WorkManager
    implementation 'androidx.hilt:hilt-work:1.0.0'
    kapt 'androidx.hilt:hilt-compiler:1.0.0'
    
```

In the next code we see the use of @HiltAndroidApp to initialize the use of dependency injecting, it is necessary to implement Configuration.Provider and override getWorkManagerConfiguration and pass the previously injected HiltWorkerFactory, to return a new configuration. 

```
@HiltAndroidApp
class YourClassApplication : Application(), Configuration.Provider {

    @Inject
    lateinit var workerFactory: HiltWorkerFactory

    override fun getWorkManagerConfiguration(): Configuration {
        return Configuration.Builder()
            .setWorkerFactory(workerFactory)
            .build()
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


## Firebase configuration

[Create a Firebase project](https://firebase.google.com/docs/android/setup#create-firebase-project)

[Register your app with Firebase](https://firebase.google.com/docs/android/setup#register-app)

[Add a Firebase configuration file](https://firebase.google.com/docs/android/setup#add-config-file)

1.- Add the Firebase Android configuration file to your app:

  a.- Click Download google-services.json to obtain your Firebase Android config file (google-services.json).

  b.- Move your config file into the module (app-level) directory of your app.

2.- To enable Firebase products in your app, add the [google-services plugin](https://developers.google.com/android/guides/google-services-plugin) to your Gradle files.

  a.- In your root-level (project-level) Gradle file (build.gradle), add rules to include the Google Services Gradle plugin. Check that you have Google's Maven repository, as well.

<img width="698" alt="Screen Shot 2022-06-15 at 18 14 32" src="https://github.globant.com/storage/user/8812/files/f7ed4882-9d0c-4ef6-b278-9fbc99e26900">

  b.- In your module (app-level) Gradle file (usually app/build.gradle), apply the Google Services Gradle plugin
    
<img width="675" alt="Screen Shot 2022-06-15 at 18 15 28" src="https://github.globant.com/storage/user/8812/files/c2132753-a95c-496f-8e3b-55efed5911ee">

If you need more information, please check the official documentation.

https://firebase.google.com/docs/android/setup#register-app


## Google Fit configuration

**Create a Project**

Firstable, create a new project from Google Cloud Platform, to do this, log in https://console.cloud.google.com/, once logged in, in the top bar the project should appear, otherwise you should see the option create new project.

<img width="753" alt="Screen Shot 2022-05-03 at 13 32 57" src="https://github.globant.com/storage/user/8812/files/e8761297-e1fd-4d7b-8cd3-7aec142d4733">

**Enable the Google Fit API**

Once the project is created, it is necessary to activate the use of the "Fitness API" service, to do this in the search engine look for "Fitness", it will appear as the first option

![image](https://github.globant.com/storage/user/8812/files/0fa6ecf1-8119-45d3-bd03-3e72dc2a5137)

When you select it a new screen will appear with an Enable Fitness API button, when it's activated you should see a screen like this: 

![image](https://github.globant.com/storage/user/8812/files/4be4cbbc-03e3-42a0-b639-baecad6b739c)

**Authorization scopes**

Now it is necessary to activate the scopes which BeHealthy library needs, to do this go to the following option, where xxx is the name you give to your project in GCP:

<img width="645" alt="Screen Shot 2022-05-03 at 13 48 25" src="https://github.globant.com/storage/user/8812/files/cc94b0cf-6243-493d-bdb5-1c9958bc7de2">

Click on "EDIT APP" and go to step two, where you will find the option to add scope, you must add the ones indicated in the image:

![image](https://github.globant.com/storage/user/8812/files/4b31c1f6-bbb6-488f-b4e0-6fcc6b87d32d)

More information: https://developers.google.com/fit/datatypes#authorization_scopes

**Create Credentials**

Get an OAuth 2.0 Client ID

As a first step it is necessary to create a Client ID from Google cloud platform, to do this follow the steps detailed below.

Follow these steps to enable the Fitness API in the Google API Console and get an OAuth 2.0 client ID.

1.- Go to the Google API Console.

2.- Select a project, or create a new one. Use the same project for the Android and REST versions of your app.

3.- Click Continue to enable the Fitness API.

4.- Click Go to credentials.

5.- Click New credentials, then select OAuth Client ID.

6.- Under Application type select Android.

7.- In the resulting dialog, enter your app's SHA-1 fingerprint and package name. For example:

BB:0D:AC:74:D3:21:E1:43:67:71:9B:62:91:AF:A1:66:6E:44:5D:75

com.example.android.fit-example

8.- Click Create. Your new Android OAuth 2.0 Client ID and secret appear in the list of IDs for your project. An OAuth 2.0 Client ID is a string of characters, something like this:

780816631155-gbvyo1o7r2pn95qc4ei9d61io4uh48hl.apps.googleusercontent.com

More information: https://developers.google.com/fit/android/get-api-key

IMPORTANT: 
Google requires a verification process for production apps, you can find more information in the next link 
[Submitting your app for verification](https://support.google.com/cloud/answer/9110914#submit-app-ver)


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

![Screen Shot 2022-06-16 at 4 26 22 PM](https://github.globant.com/storage/user/8812/files/249bc9ee-1a1a-4db3-9a62-64b5cb929818)

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
