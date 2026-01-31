# Lab 5 - Power Management Lab

## Lab Prerequisites

- ESP32 (x1)
- USB-C cable for ESP32 (x1)
- Power Profiler Kit II (one kit per group of 3)
- Micro-USB cable for Power Profiler Kit (one cable per group)
- HC-SR04 Ultrasonic Sensor (x1)
- Jumper Cables
- Breadboard

## Goals

In this lab, you'll learn the basics about how to measure the power consumption of your Seeeduino XIAO ESP32S3 microcontroller in differeng usage scenarios. You'll also learn how to develop your own strategy of keeping your microcontroller to be energy-friendly.


* Connect your ESP32S3 to the UW MPSK Wifi
* Establish connection between your ESP32S3 and Firebase Realtime Database (RTDB)
* Transmitting your data from your device to the RTDB
* Use nRF Power Profiler and its kit to measure the power consumption of your ESP32S3
* Developing your own strategy based on different *mode* of ESP32S3 to save energy

### Some useful external resources:

* [nRF Connect Download](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-Desktop/Download#infotabs)
* [J-Link Download Page](https://www.segger.com/downloads/jlink/)
* [UW MPSK Setup Instructions](https://itconnect.uw.edu/tools-services-support/networks-connectivity/uw-networks/campus-wi-fi/uw-mpsk/)
* [Firebase Setup Instructions](https://randomnerdtutorials.com/esp32-firebase-realtime-database/)
* [ESP32 Deep Sleep](https://randomnerdtutorials.com/esp32-deep-sleep-arduino-ide-wake-up-sources/)

## Deliverables

**Submit a pdf with all of the following:**

### Transmitting your HC-SR04 Data to Firebase

* ✏️ Take a screenshot on your Firebase RTDB's webpage to show that your data has been successfully uploaded.

### Power Consumption Measuring

* ✏️ Take a screenshot of the plot on your power profiler app to show the power consumption change during a **1 minute time window with 5 different *mode or stages (Deep Sleep, Idle, Ultrasonic reading, Ultrasonic + BLE, Ultrasonic + WiFi)*** for the ESP32. Annnotate what each stage stands for.
* ✏️ Set the time window **from 1 minute to 10 seconds** and take a screenshot for *each* mode/stage. Calculate how long would a 500mAh battery last when the ESP32S3 is in each mode/stage.
* ✏️ Update your code to change the data transmission rates for BLE and WiFi and measure how the power consumption changes. Take a screenshot of the power consumption plots for both BLE and WiFi. Make plots for BLE and WiFi that show the relationship between transmission rates and average current used. Calculate how long a 500mAh battery would last for these new transmission rates.

### Create your own power-saving strategy

* ✏️ Describe your own policy/strategy for both BLE and WiFi to make sure a 500mAh battery can support it without being recharged for at least 24 hours
* ✏️ Implement both your strategies for BLE and WiFi. Simulate your use case and take a screenshot of the power consumption for BLE and WiFi. Annotate each stage on the plot. Calculate how long the battery will last for both your strategies.
* ✏️ Upload your code to GitHub. Add a link to your repo in the pdf.








## **1. Find the MAC address of your ESP32**

These instructions are also located on this [website](https://randomnerdtutorials.com/get-change-esp32-esp8266-mac-address-arduino/)

```arduino
// Complete Instructions to Get and Change ESP MAC Address: https://RandomNerdTutorials.com/get-change-esp32-esp8266-mac-address-arduino/
#include <Arduino.h>
#include <WiFi.h>

void setup(){
  Serial.begin(115200);
  while(!Serial);
  delay(1000);
  WiFi.begin();
  Serial.println();
  Serial.print("ESP Board MAC Address:  ");
  Serial.println(WiFi.macAddress());
}
 
void loop(){
  Serial.println(WiFi.macAddress());
  delay(1000);
}
```

## 2. Configure your ESP32 to work on the UW MPSK WiFi network

Follow the instructions on UW’s IT Connect [website](https://itconnect.uw.edu/tools-services-support/networks-connectivity/uw-networks/campus-wi-fi/uw-mpsk/):

![Untitled](images/Untitled.png)

The output should look like this if the device was successfully generated.

## 3. Confirm that your ESP32 is connected to the UW MPSK wifi

```arduino
#include <WiFi.h>

const char* ssid     = "UW MPSK";
const char* password = "your_PASSWORD"; // Replace with your password received from UW MPSK

void setup() {
  Serial.begin(115200);
  while(!Serial);
  delay(1000);
  // Connect to WiFi
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi");
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("");
  Serial.println("Connected to WiFi");
}

void loop() {
  // Test WiFi connection
  if(WiFi.status() == WL_CONNECTED) {
    Serial.println("WiFi is still connected");
  } else {
    Serial.println("WiFi connection lost");
  }
  
  delay(5000); // Wait for 5 seconds before checking again
}
```

## 4. Set up a Firebase account

Follow the instructions from **step 1 to 3** in the link [here](https://randomnerdtutorials.com/esp32-firebase-realtime-database/). You will also need the api key for your Firebase project.

Click on the gear next to Project Overview and click on general.

![general_image](images/general_settings.svg)

At the bottom of the general settings tab you should see the option to connect to iOS, Android, or Web app. Select web app.

![webApp](images/webApp.svg)

You will be prompted to give your app a nickname, you can give it any name you like. Then click on the register app button. You do not need to select the option to set up Firebase Hosting. After you have registered your app you can click the button to return to console. You will find the api key for your Firebase app here.

![Firebase API key](images/apiKey.svg)

**NOTE:** API Keys should never be publicly available. Make sure when you upload your code to Github that all API keys, log in, and user info is removed. There are many bots that scrape through Github looking for sensitive information that has not been removed or obfuscated.

The Firebase-ESP-Client Library mentioned in the previous link is deprecated. Install FirebaseClient library instead.
![](images/firebase_lib.png)

Here is some example code you can run to see if you have set up your Firebase correctly. You will need to input information that you have previous set up such as WiFi credentials, user email and password, Firebase RTDB URL, and the API Key. This example code should upload test data of various types every fifteen seconds. Make sure to take a look at the code and try and understand what functions and methods it is using to upload data to Firebase. Alternatively, you can put it into your favorite LLM and have it understand it for you. 🤡

```arduino
/**
 * The bare minimum code example for using Realtime Database service.
 *
 * The steps which are generally required are explained below.
 *
 * Step 1. Include the network, SSL client and Firebase libraries.
 * ===============================================================
 *
 * Step 2. Define the user functions that are required for library usage.
 * =====================================================================
 *
 * Step 3. Define the authentication config (identifier) class.
 * ============================================================
 * In the Firebase/Google Cloud services REST APIs, the auth tokens are used for authentication/authorization.
 *
 * The auth token is a short-lived token that will be expired in 60 minutes and need to be refreshed or re-created when it expired.
 *
 * There can be some special use case that some services provided the non-authentication usages e.g. using database secret
 * in Realtime Database, setting the security rules in Realtime Database, Firestore and Firebase Storage to allow public read/write access.
 *
 * The UserAuth (user authentication with email/password) is the basic authentication for Realtime Database,
 * Firebase Storage and Firestore services except for some Firestore services that involved with the Google Cloud services.
 *
 * It stores the email, password and API keys for authentication process.
 *
 * In Google Cloud services e.g. Cloud Storage and Cloud Functions, the higest authentication level is required and
 * the ServiceAuth class (OAuth2.0 authen) and AccessToken class will be use for this case.
 *
 * While the CustomAuth provides the same authentication level as user authentication unless it allows the custom UID and claims.
 *
 * Step 4. Define the authentication handler class.
 * ================================================
 * The FirebaseApp actually works as authentication handler.
 * It also maintains the authentication or re-authentication when you place the FirebaseApp::loop() inside the main loop.
 *
 * Step 5. Define the SSL client.
 * ==============================
 * It handles server connection and data transfer works.
 *
 * In this beare minimum example we use only one SSL client for all processes.
 * In some use cases e.g. Realtime Database Stream connection, you may have to define the SSL client for it separately.
 *
 * Step 6. Define the Async Client.
 * ================================
 * This is the class that is used with the functions where the server data transfer is involved.
 * It stores all sync/async taks in its queue.
 *
 * It requires the SSL client and network config (identifier) data for its class constructor for its network re-connection
 * (e.g. WiFi and GSM), network connection status checking, server connection, and data transfer processes.
 *
 * This makes this library reliable and operates precisely under various server and network conditions.
 *
 * Step 7. Define the class that provides the Firebase/Google Cloud services.
 * ==========================================================================
 * The Firebase/Google Cloud services classes provide the member functions that works with AsyncClient.
 *
 * Step 8. Start the authenticate process.
 * ========================================
 * At this step, the authentication credential will be used to generate the auth tokens for authentication by
 * calling initializeApp.
 *
 * This allows us to use different authentications for each Firebase/Google Cloud services with different
 * FirebaseApps (authentication handler)s.
 *
 * When calling initializeApp with timeout, the authenication process will begin immediately and wait at this process
 * until it finished or timed out. It works in sync mode.
 *
 * If no timeout was assigned, it will work in async mode. The authentication task will be added to async client queue
 * to process later e.g. in the loop by calling FirebaseApp::loop.
 *
 * The workflow of authentication process.
 *
 * -----------------------------------------------------------------------------------------------------------------
 *  Setup   |    FirebaseApp [account credentials/tokens] ───> InitializeApp (w/wo timeout) ───> FirebaseApp::getApp
 * -----------------------------------------------------------------------------------------------------------------
 *  Loop    |    FirebaseApp::loop  ───> FirebaseApp::ready ───> Firebase Service API [auth token]
 * ---------------------------------------------------------------------------------------------------
 *
 * Step 9. Bind the FirebaseApp (authentication handler) with your Firebase/Google Cloud services classes.
 * ========================================================================================================
 * This allows us to use different authentications for each Firebase/Google Cloud services.
 *
 * It is easy to bind/unbind/change the authentication method for different Firebase/Google Cloud services APIs.
 *
 * Step 10. Set the Realtime Database URL (for Realtime Database only)
 * ===================================================================
 *
 * Step 11. Maintain the authentication and async tasks in the loop.
 * ==============================================================
 * This is required for authentication/re-authentication process and keeping the async task running.
 *
 * Step 12. Checking the authentication status before use.
 * =======================================================
 * Before calling the Firebase/Google Cloud services functions, the FirebaseApp::ready() of authentication handler that bined to it
 * should return true.
 *
 * Step 13. Process the results of async tasks at the end of the loop.
 * ============================================================================
 * This requires only when async result was assigned to the Firebase/Google Cloud services functions.
 */

// Step 1

#define ENABLE_USER_AUTH
#define ENABLE_DATABASE

// For ESP32
#include <WiFi.h>
#include <WiFiClientSecure.h>
#include <FirebaseClient.h>

// Step 2
void asyncCB(AsyncResult &aResult);
void processData(AsyncResult &aResult);

// Step 3
UserAuth user_auth("WEB_API_KEY", "USER_EMAIL", "USER_PASSWORD");

// Step 4
FirebaseApp app;

// Step 5
// Use two SSL clients for sync and async tasks for demonstation only.
WiFiClientSecure ssl_client1, ssl_client2;

// Step 6
// Use two AsyncClients for sync and async tasks for demonstation only.
using AsyncClient = AsyncClientClass;
AsyncClient async_client1(ssl_client1), async_client2(ssl_client2);

// Step 7
RealtimeDatabase Database;

bool onetimeTest = false;

// The Optional proxy object that provides the data/information
//  when used in async mode without callback.
AsyncResult dbResult;

void setup()
{
    Serial.begin(115200);

    WiFi.begin("UW MPSK", "WIFI_PASSWORD");

    Serial.print("Connecting to Wi-Fi");
    while (WiFi.status() != WL_CONNECTED)
    {
        Serial.print(".");
        delay(300);
    }
    Serial.println();
    Serial.print("Connected with IP: ");
    Serial.println(WiFi.localIP());
    Serial.println();

    // The SSL client options depend on the SSL client used.

    // Skip certificate verification (Optional)
    ssl_client1.setInsecure();
    ssl_client2.setInsecure();

    // Set timeout for ESP32 core sdk v3.x. (Optional)
    ssl_client1.setConnectionTimeout(1000);
    ssl_client1.setHandshakeTimeout(5);
    ssl_client2.setConnectionTimeout(1000);
    ssl_client2.setHandshakeTimeout(5);

    // ESP8266 Set buffer size (Optional)
    // ssl_client1.setBufferSizes(4096, 1024);
    // ssl_client2.setBufferSizes(4096, 1024);

    // Step 8
    initializeApp(async_client1, app, getAuth(user_auth), processData, "🔐 authTask");

    // Step 9
    app.getApp<RealtimeDatabase>(Database);

    // Step 10
    Database.url("FIREBASE_RTDB_URL");
}

void loop()
{
    // Step 11
    app.loop();

    // Step 12
    if (app.ready() && !onetimeTest)
    {
        onetimeTest = true;

        // The following code shows how to call the Firebase functions in both async and await modes
        
        // for demonstation only. You can choose async or await mode or use both modes in the same application. 
        // For await mode, no callback and AsyncResult object are assigned to the function, the function will
        // return the value or payload immediately.

        // For async mode, the value or payload will be returned later to the AsyncResult object 
        // or when the callback was called.
        // If AsyncResult was assigned to the function, please don't forget to check it before 
        // exiting the loop as in step 13.

        // For elaborate usage, plese see other examples.

        // Realtime Database set value.
        // ============================

        // Async call with callback function
        Database.set<String>(async_client1, "/examples/BareMinimum/data/set1", "abc", processData, "RealtimeDatabase_SetTask");

        // Async call with AsyncResult for returning result.
        Database.set<bool>(async_client1, "/examples/BareMinimum/data/set2", true, dbResult);

        // Realtime Database get value.
        // ============================

        // Async call with callback function
        Database.get(async_client1, "/examples/BareMinimum/data/set1", processData, false, "RealtimeDatabase_GetTask");

        // Async call with AsyncResult for returning result.
        Database.get(async_client1, "/examples/BareMinimum/data/set2", dbResult, false);

        // Await call which waits until the result was received.
        String value = Database.get<String>(async_client2, "/examples/BareMinimum/data/set3");
        if (async_client2.lastError().code() == 0)
        {
            Serial.println("Value get complete.");
            Serial.println(value);
        }
        else
            Firebase.printf("Error, msg: %s, code: %d\n", async_client2.lastError().message().c_str(), async_client2.lastError().code());
    }

    // Step 13
    processData(dbResult);
}

void processData(AsyncResult &aResult)
{
    // Exits when no result is available when calling from the loop.
    if (!aResult.isResult())
        return;

    if (aResult.isEvent())
        Firebase.printf("Event task: %s, msg: %s, code: %d\n", aResult.uid().c_str(), aResult.eventLog().message().c_str(), aResult.eventLog().code());

    if (aResult.isDebug())
        Firebase.printf("Debug task: %s, msg: %s\n", aResult.uid().c_str(), aResult.debug().c_str());

    if (aResult.isError())
        Firebase.printf("Error task: %s, msg: %s, code: %d\n", aResult.uid().c_str(), aResult.error().message().c_str(), aResult.error().code());

    if (aResult.available())
        Firebase.printf("task: %s, payload: %s\n", aResult.uid().c_str(), aResult.c_str());
}



```
## 5. Write Code to implement different power consumption modes/stages

Once you have a successfully been able to send data to Firebase, write a program that has the ESP32 go through five different modes/stages. In each mode/stage the ESP32 will consume different amounts of power. Each mode/stage should last for 10-12 seconds. The five modes/stages are: Deep Sleep, Idle, Ultrasonic readings, Ultrasonic + BLE + send to another ESP32, and Ultrasonic + WiFi + sent to Firebase. For more information on how to put the ESP32 into deep sleep mode and the different ways to wake it up, please refer to the link in the external resources section at the top of this page.
## 5. Download nRF Connect for Desktop and J-Link Configurator (if you have not done so already)

Find the software on this [website](https://www.nordicsemi.com/Products/Development-tools/nRF-Connect-for-desktop/Download#infotabs) and then install the Power Profiler App to connect with the Power Profiler Kit (see image below). Also, install the J-LINK Configurator software. The link of downloading J-LINK is [here](https://www.segger.com/downloads/jlink/).

**Note: For Macs with Apple Silicon, use the `Universal Installer`**

![Untitled](images/Untitled%201.png)

After you install “Power Profiler,” open it to see the following interface:

![Untitled](images/Untitled%202.png)

## 6. Connect your ESP32 circuit to your Power Profiler Kit and view power consumption through the Power Profiler App

First, build a small circuit by connecting your ultrasonic sensor to your ESP32. Ensure that the sensor is working properly by printing the measured distance in your serial monitor.

Next, connect your ESP32 to the Power Profiler Kit (PPK), as shown in the figure below. Connect the Vout and GND pin of the PPK to the 5V and GND pin of your ESP32. Make sure that all devices and peripherals have a common ground connection.


**NOTE: The PPK will provide power to your ESP32. Make sure that your ESP32 is NOT connected by USB to your computer at the same time the PPK is supplying power.**

The PPK will provide power to your ESP32 and ultrasonic sensor. Select the “USB DATA/POWER” port on the Power Profiler Kit. Turn the button on.

![Untitled](images/Untitled%203.png)

NOTE : You should connect your micro-USB into the `USB DATA/Power` port.

Next, turn on the Power Profiler App and select the PPK you just connected. Program the device if prompted.

![Untitled](images/Untitled%204.png)

Next, ensure that the settings on your Power Profiler App match the settings in the image below.

1. Choose source meter mode
2. Set the supply voltage to 5V
3. Set sample rate to 1000Hz
4. Click start
5. Enable power output

![1706729602082](images/1706729695033.png)

If everything is working properly, you should see a visualization of the measurement of power consumption, as the following figure shows (the values can be different). This visualization is the power consumption over time.

![SCR-20240130-sdrl.png](images/SCR-20240130-sdrl.png)

Take a screenshot of the power consumption visualization over one minute.

Annotate, like above, the key moments on the screenshot which depict the events which consume different amounts of power. These events are:

1. Idle ESP32 (not running WiFi/BLE or ultrasonic sensor)
2. Only ultrasonic sensor working
3. Ultrasonic + BLE + Sending data to another ESP32
4. Ultrasonic + Wifi + Sending data to Firebase
5. Deep Sleep mode

Record the average current for each mode/stage above, and estimate how long would a 500mAh battery last if the device is kept being used under each mode.

## 7. Estimate the battery life for different use cases

Using the window duration selector at the top of the Power Profiler App, change the time window to 3 or 10 seconds. For each of the five power-consumption use cases described above, wait until the window is filled with only the power consumption curve that is unique to each use case.

Take a screenshot of the power consumption curve and the average power consumption for each use case.

![Untitled](images/Untitled%206.png)

A typical ‘[ADAFRUIT INDUSTRIES 1578 Lithium Ion Polymer Battery](https://www.adafruit.com/product/1578)’ has a capacity of 500mAh, which means that it can support a system running at 500 mA for 1 hour.

Estimate how long the battery would support each of the five use cases before dying. For example, if the average current is 50mA, then the battery can last for 10 hours (500mAh / 50mA = 10 hours).

Rewrite the Arduino code to transmit your ultrasonic sensor data to another ESP32 (BLE) and Firebase (WiFi) at the five following rates:

1. 2 times per second (2 Hz)
2. 1 time per second (1 Hz)
3. Once every 2 seconds (0.5 Hz)
4. Once every 3 seconds (0.333 Hz)
5. Once every 4 seconds (0.25 Hz)

Identify what the power consumption (use average current multiplied by the voltage, unit in Watt, W) is for each of the five different data transmission rates. Plot a graph with Excel or Google Sheets to find the correlation between the data transmission rate and power consumption.

Zoom in the window to read the averaged current on your Power Profiler app, and estimate how long a 500mAh battery would support each of the five different data transmission rates.

## 8. Create your own power consumption guidelines for the ESP32 and ultrasonic sensor

Given the insights that you have gained thus far, create your own power consumption guidelines for a battery-powered device. This ESP32-enabled device must use an ultrasonic sensor to measure distance and transmit that distance data. The device must operate for **24 hours** on a single 500mAh battery. Do this for BLE and WiFi.

Assume that this device is designed to detect whether or not an object (or person) has moved in front of the ultrasonic sensor in a room. The results of that detection should be uploaded to the Firebase cloud database. Rewrite the Arduino code to develop new power consumption guidelines for this new device. There are several variables that you can change, including deep sleep duration and frequency, or the data transmission rate, among others. For example, you can develop a policy such as *“If the ultrasonic sensor’s readout is greater than 50cm for 30 seconds, turn the ESP32 to sleep mode for 30 seconds.”*

Upload the code onto your ESP32, reconnect the Power Profiler Kit, and take a screenshot of a 1-minute window of the power consumption visualization. Make sure that your screenshot includes at least the deep-sleep mode and the working mode of your device. Include the average current in the screenshot. Finally, estimate your strategy's power consumption for 24 hours under your simulated scenario, and prove that it can be supported by a 500mAh battery without recharging. Annotate any notable changes in current on the visualization, and summarize the guidelines you’ve developed.
