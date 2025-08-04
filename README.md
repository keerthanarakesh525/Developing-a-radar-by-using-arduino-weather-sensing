# Developing-a-radar-by-using-arduino-weather-sensing
Arduino based radar system that detects objects and monitors weather conditions with real time visualization

#include <Servo.h>
#include <DHT.h>

#define TRIG_PIN 9
#define ECHO_PIN 10
#define SERVO_PIN 6
#define DHTPIN 7
#define DHTTYPE DHT11

Servo myServo;
DHT dht(DHTPIN, DHTTYPE);

long duration;
int distance;
int angle;

void setup() {
  Serial.begin(9600);
  myServo.attach(SERVO_PIN);
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  dht.begin();
}

void loop() {
  // Sweep from 0° to 180° and back
  for (angle = 0; angle <= 180; angle++) {
    myServo.write(angle);
    delay(20);
    distance = getDistance();
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();

    // Send data over Serial
    Serial.print("Angle:");
    Serial.print(angle);
    Serial.print(",Distance:");
    Serial.print(distance);
    Serial.print("cm,Temp:");
    Serial.print(temp);
    Serial.print("C,Humidity:");
    Serial.print(hum);
    Serial.println("%");
  }

  for (angle = 180; angle >= 0; angle--) {
    myServo.write(angle);
    delay(20);
    distance = getDistance();
    float temp = dht.readTemperature();
    float hum = dht.readHumidity();

    Serial.print("Angle:");
    Serial.print(angle);
    Serial.print(",Distance:");
    Serial.print(distance);
    Serial.print("cm,Temp:");
    Serial.print(temp);
    Serial.print("C,Humidity:");
    Serial.print(hum);
    Serial.println("%");
  }
}

int getDistance() {
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  duration = pulseIn(ECHO_PIN, HIGH);
  distance = duration * 0.034 / 2;
  return distance;
}
