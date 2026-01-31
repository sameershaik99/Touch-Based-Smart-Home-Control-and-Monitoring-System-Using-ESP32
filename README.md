#include <Adafruit_GFX.h>
#include <Adafruit_ILI9341.h>
#include <Wire.h>
#include <Adafruit_FT6206.h>
#include <DHT.h>

// ---------------- TFT ----------------
#define TFT_CS   5
#define TFT_DC   16
#define TFT_RST  17

Adafruit_ILI9341 tft(TFT_CS, TFT_DC, TFT_RST);
Adafruit_FT6206 ctp;

// ---------------- OUTPUT DEVICES ----------------
#define LED1 2     // Room Light
#define LED2 4     // Fan
#define LED3 15    // Night Lamp

// ---------------- SENSORS ----------------
#define DHTPIN 14
#define DHTTYPE DHT22
#define LDR_PIN 34
#define PIR_PIN 26

DHT dht(DHTPIN, DHTTYPE);

// ---------------- STATES ----------------
bool lightState = false;
bool fanState   = false;
bool lampState  = false;

// ---------------- SENSOR VALUES ----------------
float temperature, humidity;
int lightLevel;
bool motionDetected;

// ---------------- TOUCH HELPER ----------------
bool isTouchInside(int x, int y, int bx, int by, int bw, int bh) {
  return (x > bx && x < bx + bw && y > by && y < by + bh);
}

// ---------------- SETUP ----------------
void setup() {
  Serial.begin(115200);
  Wire.begin(21, 22);

  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);
  pinMode(LED3, OUTPUT);
  pinMode(PIR_PIN, INPUT);

  dht.begin();

  tft.begin();
  tft.setRotation(0);          // PORTRAIT MODE (240x320)
  tft.fillScreen(ILI9341_BLACK);

  if (!ctp.begin(40)) {
    Serial.println("Touch controller not found");
    while (1);
  }

  drawUI();
}

// ---------------- LOOP ----------------
void loop() {
  handleTouch();
  readSensors();
  updateDisplay();
  delay(200);
}

// ---------------- UI ----------------
void drawUI() {
  tft.fillScreen(ILI9341_BLACK);
  tft.setTextColor(ILI9341_WHITE);
  tft.setTextSize(2);

  tft.setCursor(30, 10);
  tft.println("SMART HOME");

  drawButton(40, 50,  "LIGHT");
  drawButton(40, 100, "FAN");
  drawButton(40, 150, "LAMP");

  tft.setCursor(10, 210); tft.println("TEMP:");
  tft.setCursor(10, 235); tft.println("HUM:");
  tft.setCursor(10, 260); tft.println("LIGHT:");
  tft.setCursor(10, 285); tft.println("MOTION:");
}

void drawButton(int x, int y, const char* label) {
  tft.drawRect(x, y, 200, 40, ILI9341_GREEN);
  tft.setCursor(x + 60, y + 12);
  tft.println(label);
}

// ---------------- TOUCH HANDLER (ACCURATE) ----------------
void handleTouch() {
  if (!ctp.touched()) return;

  // Read twice to reduce noise
  TS_Point p1 = ctp.getPoint();
  delay(10);
  TS_Point p2 = ctp.getPoint();

  // Average touch points (Wokwi FT6206 portrait mapping)
  int x = (p1.y + p2.y) / 2;
  int y = (p1.x + p2.x) / 2;

  // LIGHT
  if (isTouchInside(x, y, 40, 50, 200, 40)) {
    lightState = !lightState;
    delay(350);
  }
  // FAN
  else if (isTouchInside(x, y, 40, 100, 200, 40)) {
    fanState = !fanState;
    delay(350);
  }
  // LAMP
  else if (isTouchInside(x, y, 40, 150, 200, 40)) {
    lampState = !lampState;
    delay(350);
  }

  digitalWrite(LED1, lightState);
  digitalWrite(LED2, fanState);
  digitalWrite(LED3, lampState);
}

// ---------------- SENSOR READ + AUTO LIGHT ----------------
void readSensors() {
  temperature = dht.readTemperature();
  humidity = dht.readHumidity();
  lightLevel = analogRead(LDR_PIN);
  motionDetected = digitalRead(PIR_PIN);

  // AUTO LIGHT USING PIR
  if (motionDetected) {
    lightState = true;
  }

  digitalWrite(LED1, lightState);
}

// ---------------- DISPLAY UPDATE ----------------
void updateDisplay() {
  tft.setTextColor(ILI9341_YELLOW, ILI9341_BLACK);
  tft.setTextSize(2);

  // TEMP
  tft.fillRect(90, 210, 140, 18, ILI9341_BLACK);
  tft.setCursor(90, 210);
  tft.print(temperature, 1); 
  tft.print(" C");

  // HUM
  tft.fillRect(90, 235, 140, 18, ILI9341_BLACK);
  tft.setCursor(90, 235);
  tft.print(humidity, 1); 
  tft.print(" %");

  // LIGHT LEVEL
  tft.fillRect(90, 260, 140, 18, ILI9341_BLACK);
  tft.setCursor(90, 260);
  if (lightLevel < 1500)      tft.print("LOW");
  else if (lightLevel < 3000) tft.print("MED");
  else                        tft.print("HIGH");

  // MOTION
  tft.fillRect(90, 285, 200, 18, ILI9341_BLACK);
  tft.setCursor(90, 285);
  tft.print(motionDetected ? "DETECTED" : "NO MOTION");
}
