//code for smart IV bottle alarm system
#include <Wire.h>
#include <LiquidCrystal_PCF8574.h>
#include "HX711.h"

#define DOUT  A1 // HX711 data pin
#define CLK   A0 // HX711 clock pin
#define LED   13

LiquidCrystal_PCF8574 lcd(0x27);
HX711 scale;

int thresholdWeight = 20;
bool smsSent = false;

void setup() {
    pinMode(LED, OUTPUT);
    Serial.begin(9600);
    lcd.begin(16, 2);
    lcd.setBacklight(255);
    lcd.setCursor(0, 0);
    lcd.print("IV Monitor Init");
    delay(2000);

    // Initialize HX711
    scale.begin(DOUT, CLK);
    scale.set_scale(); // Replace with your calibration factor
    scale.tare();

    // Initialize GSM
    Serial.println("Initializing GSM...");
    if (!sms.begin(9600)) {
        Serial.println("GSM module failed!");
        lcd.setCursor(0, 1);
        lcd.print("GSM Error!");
        while (true); // Halt execution if GSM fails
    }
    lcd.clear();
}

void loop() {
    float weight = scale.get_units(5); // Average of 5 readings
    lcd.setCursor(0, 0);
    lcd.print("Weight: ");
    lcd.print(weight);
    lcd.print(" g ");

    if (weight < thresholdWeight && !smsSent) {
        sendSMS();
        smsSent = true;
    } else if (weight >= thresholdWeight) {
        smsSent = false; // Reset when weight goes above threshold
    }
    delay(1000);
}

void sendSMS() {
    digitalWrite(LED, HIGH);
    lcd.setCursor(0, 1);
    lcd.print("Sending SMS...");
    sms.print("AT+CMGS=\"+917800600555\"\r");
    delay(100);
    sms.print("DRIPS EMPTY");
    sms.write(26); // ASCII code for Ctrl+Z
    delay(5000);
    lcd.clear();
    lcd.setCursor(0, 1);
    lcd.print("SMS SENT");
    digitalWrite(LED, LOW);
}
