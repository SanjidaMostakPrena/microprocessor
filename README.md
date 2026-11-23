// পিন ডিফাইন করা হলো
const int ldrPin = A0;      // LDR সেন্সর পিন
const int pirPin = 2;       // PIR মোশন সেন্সর পিন
const int ledPin = 9;       // সরাসরি LED কন্ট্রোল পিন (অবশ্যই PWM পিন হতে হবে: 3, 5, 6, 9, 10, 11)

// থ্রেশহোল্ড ও ব্রাইটনেস সেটিংস
int ldrThreshold = 500;     // অন্ধকার মাপার মান (0 থেকে 1023 এর মধ্যে)
int dimBrightness = 30;     // অন্ধকারে হালকা আলো (0 - 255 এর মধ্যে, 30 মানে প্রায় ১২% আলো)
int fullBrightness = 255;   // মোশন ডিটেক্ট হলে ফুল আলো (১০০%)

// টাইমার ভেরিয়েবল
unsigned long motionDelay = 5000; // মোশন থামার পর ৫ সেকেন্ড ফুল আলো থাকবে
unsigned long lastMotionTime = 0;

void setup() {
  pinMode(pirPin, INPUT);
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  int ldrValue = analogRead(ldrPin);   // আলোর পরিমাণ পড়া হচ্ছে
  int pirState = digitalRead(pirPin);  // মোশন চেক করা হচ্ছে

  // সিরিয়াল মনিটরে মান দেখার জন্য
  Serial.print("LDR: ");
  Serial.print(ldrValue);
  Serial.print(" | PIR: ");
  Serial.println(pirState);

  // শর্ত ১: যদি অন্ধকার হয়
  if (ldrValue < ldrThreshold) { 
    
    // যদি মোশন ধরা পড়ে
    if (pirState == HIGH) {
      analogWrite(ledPin, fullBrightness); // ১০০% উজ্জ্বল আলো
      lastMotionTime = millis();
    } 
    // মোশন না থাকলে ৫ সেকেন্ড পর আলো কমে যাবে
    else {
      if (millis() - lastMotionTime > motionDelay) {
        analogWrite(ledPin, dimBrightness); // ডিম (Dim) আলো
      }
    }
  } 
  // শর্ত ২: দিনের আলো থাকলে
  else {
    analogWrite(ledPin, 0); // LED সম্পূর্ণ বন্ধ
  }

  delay(100);
}
