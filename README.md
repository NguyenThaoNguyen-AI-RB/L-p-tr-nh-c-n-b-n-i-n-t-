# L-p-tr-nh-c-n-b-n-i-n-t-
Lập trình căn bản điện tử
#include <DHT11.h>
// Cấu hình chân
#define DHTPIN A0        // Chân kết nối cảm biến DHT11
#define DHTTYPE DHT11   // Loại cảm biến

#define LED_SUOI 13      // LED sưởi
#define LED_TAN  12      // LED tản nhiệt
#define LED_DOAM 11      // LED điều chỉnh độ ẩm

#define BTN_UPT A1       // Tăng ngưỡng trên
#define BTN_DWT A2       // Giảm ngưỡng trên
#define BTN_UPD A3       // Tăng ngưỡng dưới
#define BTN_DWD A4       // Giảm ngưỡng dưới

DHT DHT11(DHTPIN, DHTTYPE);

// Biến ngưỡng
int ghndt = 38;   // Ngưỡng nhiệt độ trên
int ghndd = 35;   // Ngưỡng nhiệt độ dưới

void setup() {
  Serial.begin(9600);
  dht11.begin();

  pinMode(LED_SUOI, OUTPUT);
  pinMode(LED_TAN, OUTPUT);
  pinMode(LED_DOAM, OUTPUT);

  pinMode(BTN_UPT, INPUT_PULLUP);
  pinMode(BTN_DWT, INPUT_PULLUP);
  pinMode(BTN_UPD, INPUT_PULLUP);
  pinMode(BTN_DWD, INPUT_PULLUP);

  digitalWrite(LED_SUOI, LOW);
  digitalWrite(LED_TAN, LOW);
  digitalWrite(LED_DOAM, LOW);
}

void loop() {
  // Đọc dữ liệu DHT11
  float temp = dht11.readTemperature();
  float humi = dht11.readHumidity();

  // Gửi dữ liệu qua Serial để LabVIEW nhận
  Serial.print("ND:");
  Serial.print(temp);
  Serial.print(" DA:");
  Serial.println(humi);

  // Điều khiển LED theo điều kiện
  if (isnan(temp) || isnan(humi)) {
    digitalWrite(LED_SUOI, LOW);
    digitalWrite(LED_TAN, LOW);
    digitalWrite(LED_DOAM, LOW);
    return;
  }

  if (humi < 60 || humi > 75) {
    digitalWrite(LED_SUOI, LOW);
    digitalWrite(LED_TAN, LOW);
    digitalWrite(LED_DOAM, HIGH);
  } else if (temp < ghndd) {
    digitalWrite(LED_SUOI, HIGH);
    digitalWrite(LED_TAN, LOW);
    digitalWrite(LED_DOAM, LOW);
  } else if (temp > ghndt) {
    digitalWrite(LED_SUOI, LOW);
    digitalWrite(LED_TAN, HIGH);
    digitalWrite(LED_DOAM, LOW);
  } else {
    digitalWrite(LED_SUOI, LOW);
    digitalWrite(LED_TAN, LOW);
    digitalWrite(LED_DOAM, LOW);
  }

  // Đọc nút nhấn
  if (!digitalRead(BTN_UPT)) {
    if (ghndt < 50) ghndt++;
    delay(300);
  }

  if (!digitalRead(BTN_DWT)) {
    if (ghndt > ghndd + 1) ghndt--;
    delay(300);
  }

  if (!digitalRead(BTN_UPD)) {
    if (ghndd < ghndt - 1) ghndd++;
    delay(300);
  }

  if (!digitalRead(BTN_DWD)) {
    if (ghndd > 5) ghndd--;
    delay(300);
  }

  delay(1000); // chờ 1s giữa mỗi lần đọc
}
