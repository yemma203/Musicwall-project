#include "LedControl.h"

/* Led matrix - Max7219 Declared */
LedControl lc = LedControl(12, 10, 11, 1);

const int maxScale = 11;

/*  Sensor - Max9812 Declared */
const int sensorPin = A4;
const int sampleWindow = 50;  // 50ms = 20Hz
unsigned int sample;

unsigned long startMillis;
unsigned long timeCycle;

unsigned int signalMax = 0;
unsigned int signalMin = 1024;
unsigned char index = 0;

unsigned int peakToPeak[8];
unsigned int displayPeak[8];
unsigned int temp[8]={0,0,0,0,0,0,0,0};
unsigned int signalMaxBuff[8];
unsigned int signalMinBuff[8];


void setup() {
  // Led matrix
  lc.shutdown(0, false); // bật hiện thịç
  lc.setIntensity(0, 1); // chỉnh độ sáng 
  lc.clearDisplay(0); // tắt tất cả led

  Serial.begin(9600);
}

void loop() {
  startMillis = millis();
  //peakToPeak = 0;

  signalMax = 0;
  signalMin = 1024;
  
  // Get data in 50ms
  while (millis() - startMillis < sampleWindow) {
    sample = analogRead(sensorPin);
    
    if (sample < 1024) {
      if (sample > signalMax) {
        signalMax = sample;
      }
      if (sample < signalMin) {
        signalMin = sample;
      }
    } 

    // 20Hz - 64Hz - 125Hz - 250Hz - 500Hz - 1kHz (timeCycle = 1/F)(ms)
    timeCycle = millis() - startMillis;
    if (timeCycle == 1 || timeCycle == 2 || timeCycle == 4 || timeCycle == 8 
        || timeCycle == 16 || timeCycle == 32 || timeCycle == 40 || timeCycle == 50) {
              signalMaxBuff[index] = signalMax;
              signalMinBuff[index] = signalMin;
              index = (index + 1) % 8;
              delay(1);
              //Serial.println(timeCycle);
    }
  }

  // Delete pointer to array
  index = 0;

  // Calculation after get samples
  for (int i = 0; i < 8; i++) {  // i = row (led matrix)
    // sound level
    peakToPeak[i] = signalMaxBuff[i] - signalMinBuff[i];
    
    // Map 1v p-p level to the max scale of the display
    displayPeak[i] = map(peakToPeak[i], 0, 1023, 0, maxScale);

    // Show to led matrix
    displayLed(displayPeak[i], i);
    
    // Led drop down
    if (displayPeak[i] >= temp[i]) {
      temp[i] = displayPeak[i];
    }
    else {
      temp[i]--;
    }
    
    lc.setLed(0, i, temp[i], true);
    delayMicroseconds(250);
  }
   
}

void displayLed(int displayPeak, int row) {
  switch (displayPeak) {
    case 0 : lc.setRow(0, row, 0x80); break;
    case 1 : lc.setRow(0, row, 0xC0); break;
    case 2 : lc.setRow(0, row, 0xE0); break;
    case 3 : lc.setRow(0, row, 0xF0); break;
    case 4 : lc.setRow(0, row, 0xF8); break;
    case 5 : lc.setRow(0, row, 0xFC); break;
    case 6 : lc.setRow(0, row, 0xFE); break;
    case 7 : lc.setRow(0, row, 0xFF); break;
  }
}
