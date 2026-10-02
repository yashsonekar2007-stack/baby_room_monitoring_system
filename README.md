#include <WiFi.h>
#include <DHT.h>
#include <ThingSpeak.h>


const char* WIFI_NAME = "Wokwi-GUEST";
const char* WIFI_PASSWORD = "";


unsigned long myChannelNumber = 3XXXX61; 
const char* myWriteAPIKey = "P5SQXXXXXXH1H7Q9";     


#define DHTPIN 15
#define DHTTYPE DHT22 
#define RED_LED 12
#define GREEN_LED 14
#define BUZZER 13


const float TEMP_MIN = 20.0;
const float TEMP_MAX = 26.0;
const float HUM_MIN = 40.0;
const float HUM_MAX = 60.0;

DHT dht(DHTPIN, DHTTYPE);
WiFiClient client;

int alertCount = 0;
unsigned long lastTime = 0;
const unsigned long delayTime = 15000; 

void setup() {
  Serial.begin(115200);
  
  pinMode(RED_LED, OUTPUT);
  pinMode(GREEN_LED, OUTPUT);
  pinMode(BUZZER, OUTPUT);
  
  dht.begin();
  
  // Connect to WiFi
  Serial.print("Connecting to WiFi");
  WiFi.begin(WIFI_NAME, WIFI_PASSWORD);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi Connected!");
  
  ThingSpeak.begin(client);
}

void loop() {
  
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();

 
  if (isnan(temperature) || isnan(humidity)) {
    Serial.println("Failed to read from DHT sensor!");
    return;
  }

 
  bool isUnsafe = false;

  if (temperature < TEMP_MIN || temperature > TEMP_MAX || humidity < HUM_MIN || humidity > HUM_MAX) {
    isUnsafe = true;
  }

 
  Serial.println("-------------------------------------");
  Serial.print("Temperature: "); Serial.print(temperature); Serial.println(" °C");
  Serial.print("Humidity: "); Serial.print(humidity); Serial.println(" %");
  

  if (isUnsafe) {
    alertCount++; // Increment total alert count
    Serial.println("STATUS: [UNSAFE] Triggering Alerts!");
    
    digitalWrite(GREEN_LED, LOW);
    digitalWrite(RED_LED, HIGH);
    
    // Generate a 1000Hz tone on the buzzer pin
    tone(BUZZER, 1000); 
  } else {
    Serial.println("STATUS: [SAFE] Conditions Normal.");
    digitalWrite(RED_LED, LOW);
    digitalWrite(GREEN_LED, HIGH);
    
    // Stop the tone when conditions are safe
    noTone(BUZZER); 
  }
  
  Serial.print("Total Cumulative Alerts: "); Serial.println(alertCount);

  // Send data to ThingSpeak every 15 seconds
  if ((millis() - lastTime) > delayTime) {
    ThingSpeak.setField(1, temperature);
    ThingSpeak.setField(2, humidity);
    ThingSpeak.setField(3, alertCount);
    
    int responseCode = ThingSpeak.writeFields(myChannelNumber, myWriteAPIKey);
    
    if (responseCode == 200) {
      Serial.println("ThingSpeak update successful.");
    } else {
      Serial.println("Error updating ThingSpeak. HTTP error code " + String(responseCode));
    }
    
    lastTime = millis();
  }

  delay(2000); // Local reading interval
}
