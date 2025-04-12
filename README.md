# project
max30100 and LM35 with esp8266

#include <Wire.h>
#include <MAX30100_PulseOximeter.h>

PulseOximeter pox;

#define REPORTING_PERIOD_MS 1000
uint32_t tsLastReport = 0;

void onBeatDetected() {
  Serial.println("Beat!");
}

void setup() {
  Serial.begin(115200);
  Wire.begin(D2, D1); 

  Serial.println("Initializing sensor...");

  if (!pox.begin()) {
    Serial.println("MAX30100 not found. Check wiring!");
    while (1); 
  }

  pox.setIRLedCurrent(MAX30100_LED_CURR_7_6MA); 
  pox.setOnBeatDetectedCallback(onBeatDetected);

  Serial.println("Sensor Ready");
}

void loop() {
  pox.update();
  delay(10); 

  if (millis() - tsLastReport > REPORTING_PERIOD_MS) {
    float hr = pox.getHeartRate();
    float spo2 = pox.getSpO2();


    int raw = analogRead(A0);
    float voltage = raw * (1.0 / 1023.0); 
    float temperature = voltage * 100.0;


    Serial.print("HR: "); Serial.print(hr, 1);
    Serial.print(" | SpO2: "); Serial.print(spo2, 1);
    Serial.print(" | Temp: "); Serial.println(temperature, 1);

    tsLastReport = millis();
  }
}
