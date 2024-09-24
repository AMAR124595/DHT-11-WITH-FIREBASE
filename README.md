# DHT-11-WITH-FIREBASE
This project creates a simple IoT system where temperature and humidity data from a DHT11 sensor are displayed on an I2C LCD and stored online in Firebase. This allows for real-time monitoring and data access from anywhere, demonstrating how to combine hardware and cloud services effectively in IoT applications.
# AIM
To setting up an IoT system using an ESP8266 microcontroller to interface with an I2C LCD display and a DHT11 temperature and humidity sensor.With the Help of  VS code
# Components
* Esp8266
* I2C 16x2 LCD display
* DHT 11
# Connection
![circuit2-o](https://github.com/user-attachments/assets/0acd00f5-1c45-4645-96f0-6f22ea122b43)
## PINS
* GND ----> GND(Esp8266)
* VCC ----> VIN( In Esp8266 NO 5V pin ,Connect the pin After the PRogram Loadded)
* SDA ----> D2(Esp8266)
* SCL ----> D1(Esp8266)
# Procedure
1. Create a ESP8266 Project On Visual Studio Code With Arduino FrameWork
2. Interface ESP8266 microcontroller with DHT 11 and do an Example code print the Data on serial monitor.
   * Library ---> lib_deps = bonezegei/Bonezegei_DHT11@^1.0.1
3. Interface ESP 8266 With I2C 16x2 LCD display and display the value on the LCD
   * Library ---> lib_deps = marcoschwartz/LiquidCrystal_I2C@^1.1.4
4. Create a Firebase Project For Read the RealTime Data
5. Collect The network credentials, URL database, and project API key ,Email ,Password
6. Install the Firebase-ESP-Client Library.Then, program the ESP8266 to Interface with Firebase
   * Library ---> lib_deps = mobizt/Firebase Arduino Client Library for ESP8266 and ESP32@^4.4.14
7. Insert your network credentials To the project work.
# Code 
```
    #if defined(ESP32)
    #include <WiFi.h>
    #elif defined(ESP8266)
    #include <ESP8266WiFi.h>
    #endif
    #include <LiquidCrystal_I2C.h>
    #include <Bonezegei_DHT11.h>
    #include <Firebase_ESP_Client.h>
    #include <addons/TokenHelper.h>
    #include <addons/RTDBHelper.h>
    /****************************************************************************************************************/
    #define WIFI_SSID "-----------------"
    #define WIFI_PASSWORD "------------------"
    #define API_KEY "--------------------------------------------"
    #define DATABASE_URL "---------------------------------------"
    #define USER_EMAIL "-----------------"
    #define USER_PASSWORD "---------"
    FirebaseData fbdo;
    FirebaseAuth auth;
    FirebaseConfig config;
    Bonezegei_DHT11 dht(D4);

    LiquidCrystal_I2C lcd(0x27,16,2);
/****************************************************************************************************************/

    int count = 0 ;
    String msg; 
    float h; 
    float t;
/****************************************************************************************************************/
    void Connect_WiFi();
    void Firebase_Store(String PATH,String MSG);
    String Firebase_getString(String PATH);
  /****************************************************************************************************************/
    void setup()
    {
         Connect_WiFi(); 
         dht.begin();  
    }
/****************************************************************************************************************/
    void loop()
    {
      if (dht.getData()) { 
      h = dht.getHumidity(); //Store humidity value in variable h
      t = dht.getTemperature(); ////Store temperature value in variable t
      
      lcd.clear();
      lcd.setCursor(0,0);
      lcd.print("Humidity: ");
      Serial.print("Humidity: ");
      lcd.setCursor(9,0);
      lcd.print(h);
      Serial.print(h);
      lcd.setCursor(14,0);
      lcd.print(" %");
      Serial.println(" %");

      lcd.setCursor(0,1);
      lcd.print("Temp: ");
      Serial.print("Temperature: ");
      lcd.setCursor(7,1);
      lcd.print(t);
      Serial.print(t);
      lcd.setCursor(13,1);
      lcd.print(" *C");
      Serial.println(" *C");
      Firebase_Store("/HUMIDITY/DATA",String(h));
      Firebase_Store("/Temperature/DATA",String(t));
      }
    }
/****************************************************************************************************************/
    void Connect_WiFi()
    {
          Serial.begin(9600);
          lcd.init();
          lcd.backlight();
          delay(100); 
          WiFi.disconnect();
          delay(800); 
          Serial.println("Connecting to Wi-Fi");
          lcd.setCursor(0,0);
          lcd.print("Connecting to Wi-Fi"); 
          WiFi.begin(WIFI_SSID, WIFI_PASSWORD);
          Serial.print("Connecting to Wi-Fi");
          
          delay(100);
          while (WiFi.status() != WL_CONNECTED)
          {
            Serial.print(".");
            lcd.setCursor(0,1);
            lcd.print(".");
            delay(300);
          }
          Serial.println();
          lcd.clear();
          lcd.setCursor(0,0);
          lcd.print("Connected ");
          Serial.print("Connected with IP: ");
          Serial.println(WiFi.localIP());
          Serial.println();
          Serial.printf("Firebase Client v%s\n\n", FIREBASE_CLIENT_VERSION);
          config.api_key = API_KEY;
          auth.user.email = USER_EMAIL;
          auth.user.password = USER_PASSWORD;
          config.database_url = DATABASE_URL;
          config.token_status_callback = tokenStatusCallback;
           #if defined(ESP8266)
            fbdo.setBSSLBufferSize(2048 /* Rx buffer size in bytes from 512 - 16384 */, 2048 /* Tx buffer size in bytes from 512 - 16384 */);
          #endif
          Firebase.begin(&config, &auth);
          Firebase.reconnectWiFi(true);
          Firebase.setDoubleDigits(5);
          config.timeout.serverResponse = 10 * 1000;
    }
/****************************************************************************************************************/
    void Firebase_Store(String PATH,String MSG)
    {
          
          
          Serial.print("Uploading data \" ");
          Serial.print(MSG);
          Serial.print(" \"  to the location \" ");
          Serial.print(PATH);
          Serial.println(" \"");
          Firebase.RTDB.setString(&fbdo, PATH, MSG);
          delay(50);
    }
/****************************************************************************************************************/
    String Firebase_getString(String PATH)
    {
      String msg = (Firebase.RTDB.getString(&fbdo, PATH) ? fbdo.to<const char *>() : fbdo.errorReason().c_str());
      delay(50);
      return msg;
    }
/****************************************************************************************************************/
```
# OutPut
![VideoCapture_20240924-223931](https://github.com/user-attachments/assets/4a7a271a-7d9b-4ad2-9d20-f854f8210362)
![VideoCapture_20240924-223944](https://github.com/user-attachments/assets/43f995c8-5288-42bc-9259-d95233509735)
![VideoCapture_20240924-223948](https://github.com/user-attachments/assets/0bcefeb2-93a4-47b2-94ed-87e34b404c9a)


