#include <ESP8266WiFi.h>
#include <EEPROM.h>
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include "SinricPro.h"
#include "SinricProSwitch.h"

// LCD
LiquidCrystal_I2C lcd(0x27, 16, 2);

// WiFi
#define WIFI_SSID "hashim"
#define WIFI_PASS "123456788"

// Sinric
#define APP_KEY "6e93649d-b994-458e-b519-b55ef9e38d43"
#define APP_SECRET "21ecac83-77d2-49db-aaf4-5a269543da6d-86b1ccd9-d662-4aa8-aca0-747af36124d9"

// Device IDs
#define device_ID_1 "69dd0ad5ad44f4047dfd46ea"
#define device_ID_2 "69dd0af8a221de787933cfd4"
#define device_ID_3 "69dd0b19ad44f4047dfd472a"

// Relay pins
#define RELAY_PIN_1 D1
#define RELAY_PIN_2 D2
#define RELAY_PIN_3 D3

// Buttons
#define DISPLAY_BTN D5
#define RESET_BTN D4

// Energy settings
#define BULB_POWER 1.0
#define RATE 8.0

// EEPROM addresses
#define ADDR1 0
#define ADDR2 10
#define ADDR3 20

// Variables
unsigned long start1 = 0, start2 = 0, start3 = 0;
float energy1 = 0, energy2 = 0, energy3 = 0;

// Display mode toggle
int displayMode = 0;
bool lastButtonState = HIGH;

// ===== BULB CONTROL =====
bool onPowerState1(const String &deviceId, bool &state) {
  if (state) {
    digitalWrite(RELAY_PIN_1, LOW);
    start1 = millis();
  } else {
    digitalWrite(RELAY_PIN_1, HIGH);
    float hours = (millis() - start1) / 3600000.0;
    energy1 += BULB_POWER * hours;

    EEPROM.put(ADDR1, energy1);
    EEPROM.commit();

    Serial.print("Bulb1 Energy: ");
    Serial.println(energy1);
  }
  return true;
}

bool onPowerState2(const String &deviceId, bool &state) {
  if (state) {
    digitalWrite(RELAY_PIN_2, LOW);
    start2 = millis();
  } else {
    digitalWrite(RELAY_PIN_2, HIGH);
    float hours = (millis() - start2) / 3600000.0;
    energy2 += BULB_POWER * hours;

    EEPROM.put(ADDR2, energy2);
    EEPROM.commit();

    Serial.print("Bulb2 Energy: ");
    Serial.println(energy2);
  }
  return true;
}

bool onPowerState3(const String &deviceId, bool &state) {
  if (state) {
    digitalWrite(RELAY_PIN_3, LOW);
    start3 = millis();
  } else {
    digitalWrite(RELAY_PIN_3, HIGH);
    float hours = (millis() - start3) / 3600000.0;
    energy3 += BULB_POWER * hours;

    EEPROM.put(ADDR3, energy3);
    EEPROM.commit();

    Serial.print("Bulb3 Energy: ");
    Serial.println(energy3);
  }
  return true;
}

void setup() {
  Serial.begin(115200);

  EEPROM.begin(512);

  // Load saved values
  EEPROM.get(ADDR1, energy1);
  EEPROM.get(ADDR2, energy2);
  EEPROM.get(ADDR3, energy3);

  Serial.println("Loaded Saved Energy Values");

  pinMode(RELAY_PIN_1, OUTPUT);
  pinMode(RELAY_PIN_2, OUTPUT);
  pinMode(RELAY_PIN_3, OUTPUT);

  pinMode(DISPLAY_BTN, INPUT_PULLUP);
  pinMode(RESET_BTN, INPUT_PULLUP);

  digitalWrite(RELAY_PIN_1, HIGH);
  digitalWrite(RELAY_PIN_2, HIGH);
  digitalWrite(RELAY_PIN_3, HIGH);

  // Custom I2C pins
  Wire.begin(D6, D7);

  lcd.init();
  lcd.backlight();

  lcd.print("Connecting WiFi");
  Serial.println("Connecting WiFi...");

  WiFi.begin(WIFI_SSID, WIFI_PASS);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  lcd.clear();
  lcd.print("WiFi Connected");
  Serial.println("\nWiFi Connected!");

  SinricProSwitch &bulb1 = SinricPro[device_ID_1];
  SinricProSwitch &bulb2 = SinricPro[device_ID_2];
  SinricProSwitch &bulb3 = SinricPro[device_ID_3];

  bulb1.onPowerState(onPowerState1);
  bulb2.onPowerState(onPowerState2);
  bulb3.onPowerState(onPowerState3);

  SinricPro.begin(APP_KEY, APP_SECRET);
}

void loop() {
  SinricPro.handle();

  float totalEnergy = energy1 + energy2 + energy3;
  float bill = totalEnergy * RATE;

  bool currentState = digitalRead(DISPLAY_BTN);

  // Detect button press
  if (lastButtonState == HIGH && currentState == LOW) {

    displayMode = !displayMode;
    lcd.clear();

    if (displayMode == 0) {
      // SCREEN 1
      lcd.setCursor(0,0);
      lcd.print("Units:");
      lcd.print(totalEnergy);

      lcd.setCursor(0,1);
      lcd.print("Bill Rs:");
      lcd.print(bill);

      Serial.println("SCREEN 1: Units & Bill");
      Serial.print("Units: "); Serial.println(totalEnergy);
      Serial.print("Bill: "); Serial.println(bill);
    } 
    else {
      // SCREEN 2
      lcd.setCursor(0,0);
      lcd.print("Smart Energy");

      lcd.setCursor(0,1);
      lcd.print("System Ready");

      Serial.println("SCREEN 2: Message Displayed");
    }

    delay(300); // debounce
  }

  lastButtonState = currentState;

  // RESET BUTTON
  if (digitalRead(RESET_BTN) == LOW) {
    energy1 = energy2 = energy3 = 0;

    EEPROM.put(ADDR1, energy1);
    EEPROM.put(ADDR2, energy2);
    EEPROM.put(ADDR3, energy3);
    EEPROM.commit();

    lcd.clear();
    lcd.print("RESET DONE");

    Serial.println("RESET DONE");

    delay(2000);
  }
}
