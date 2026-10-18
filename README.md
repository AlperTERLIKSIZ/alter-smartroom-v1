#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);

#define FAN 5
#define KITAPLIK 6
#define TAVAN 7
#define RGB 8

bool fan = false;
bool kitaplik = false;
bool tavan = false;
bool rgb = false;

int mod = 0;

void setup() {
  Serial.begin(9600);

  pinMode(FAN, OUTPUT);
  pinMode(KITAPLIK, OUTPUT);
  pinMode(TAVAN, OUTPUT);
  pinMode(RGB, OUTPUT);

  digitalWrite(FAN, HIGH);
  digitalWrite(KITAPLIK, HIGH);
  digitalWrite(TAVAN, HIGH);
  digitalWrite(RGB, HIGH);

  lcd.init();
  lcd.backlight();

  lcd.print("Sistem Hazir");
  delay(1500);

  showLCD();
}

void applyOutputs() {
  digitalWrite(FAN, fan ? LOW : HIGH);
  digitalWrite(KITAPLIK, kitaplik ? LOW : HIGH);
  digitalWrite(TAVAN, tavan ? LOW : HIGH);
  digitalWrite(RGB, rgb ? LOW : HIGH);
}

void showLCD() {
  lcd.clear();
  lcd.setCursor(0, 0);

  if (mod == 1) lcd.print("STANDART MOD");
  else if (mod == 2) lcd.print("OYUN MODU");
  else lcd.print("MANUEL KONTROL");

  lcd.setCursor(0, 1);
  lcd.print("F:"); lcd.print(fan);
  lcd.print(" K:"); lcd.print(kitaplik);
  lcd.print(" T:"); lcd.print(tavan);
  lcd.print(" R:"); lcd.print(rgb);
}

void loop() {
  if (Serial.available() > 0) {
    char cmd = Serial.read();

    if (cmd == '\n' || cmd == '\r' || cmd == ' ') return;

    switch (cmd) {
      case '1':
        mod = 1;
        kitaplik = true;
        tavan = true;
        rgb = false;
        fan = false;
        break;

      case '2':
        mod = 2;
        kitaplik = false;
        tavan = false;
        rgb = true;
        fan = true;
        break;

      case '3':
        mod = 0;
        kitaplik = !kitaplik;
        break;

      case '4':
        mod = 0;
        tavan = !tavan;
        break;

      case '5':
        mod = 0;
        rgb = !rgb;
        break;

      case '6':
        mod = 0;
        fan = !fan;
        break;

      default:
        return;
    }

    applyOutputs();
    showLCD();
  }
}
