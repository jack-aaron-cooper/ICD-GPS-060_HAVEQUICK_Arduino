/* 
MIT License

Copyright (c) 2025 Jack Cooper

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
*/


const int outputPin = 9;  // Pin to use for output
char decInput[14];        // Store the decimal input (13 chars + null)
char hqOutput[513];       // Store the converted HAVEQUICK ICD-GPS-060 format
char manOutput[1025];     // Store the converted Manchester II tranmissions data
const char* hqLookup[] = {
    "00000000", "11100001", "01110010", "10010011",
    "10110100", "01010101", "11000110", "00100111",
    "11011000", "00111001"
};
const char* startOfMessage = "0001000111101001";

void setup() {
  Serial.begin(9600);
  DDRB = B00000010;         
}

void loop() {  
  PORTB = B00000000;

  // Check for incoming serial data
  while (Serial.available()) {
    int len = Serial.readBytesUntil('\n', decInput, sizeof(decInput) - 1);
    decInput[len] = '\0'; // Null-terminate the string

    
    if(len != 12) {
      Serial.println("ERROR: Invalid input length");
      return;
    }

    // Extract hours, minutes, seconds, day, and tfom from the input
    int h = (decInput[0] - '0') * 10 + (decInput[1] - '0');
    int m = (decInput[2] - '0') * 10 + (decInput[3] - '0');
    int s = (decInput[4] - '0') * 10 + (decInput[5] - '0');
    int d = (decInput[6] - '0') * 100 + (decInput[7] - '0') * 10 + (decInput[8] - '0');
    int y = (decInput[9] - '0') * 10 + (decInput[10] - '0');
    int tf = decInput[11] - '0';
    
    // Validate the extracted values
    if (h < 0 || h > 23 || m < 0 || m > 59 || s < 0 || s > 59 || d < 1 || d > 365 || y < 0 || y > 99 || tf < 0 || tf > 9) {
      Serial.println("ERROR: Invalid input values");
      return;
    }
    
    // Sync Bits 400 Logical Ones
    memset(hqOutput, '0', sizeof(hqOutput) - 1);
    for (int i = 0; i < 512; i++) {
      if (i < 400) {
        hqOutput[i] = '1';
      }
      else if (i < 416) {
        hqOutput[i] = startOfMessage[i-400];
      }
      else {
        hqOutput[i] =
      }
    }

    // Start of message indicator

    for (int j = 0; j < 16; j++) {
        hqOutput[idx++] = startOfMessage[j];
    }

    // Message data hours (16bit), minutes (16bit), seconds (16bit), day (24bit), tfom (4bit).
    for (int i = 0; i < 12; i++) {
      strcat(hqOutput, dec2hq(decInput[i]));
    }
    hqOutput[513] = '\0';
    
    // Convert ouput to Manchester II bipahse output 
    // Logical one is HIGH for 300µs and LOW for 300µs
    // Logical zero is LOW for 300µs and HIGH for 300µs
    for (int i = 0; i < 512; i++) {
      if(hqOutput[i] == '1') {
        manOutput[i * 2] = '1';
        manOutput[i * 2 + 1] = '0';
      }
      if(hqOutput[i] == '0') {
        manOutput[i * 2] = '0';
        manOutput[i * 2 + 1] = '1';
      }
    }
    manOutput[1025] = '\0';
    
    // Output the Manchester II formatted HAVEQUICK Signal to the specified pin at 1667 bps
    for (int i = 0; i < 1024; i++) {
      PORTB = (manOutput[i] == '1') ? B00000010 : B00000000;
      delayMicroseconds(300);
    }
  }
}

const char* dec2hq(char decimal) {
    switch (decimal) {
        case '0': return "00000000";
        case '1': return "11100001";
        case '2': return "01110010";
        case '3': return "10010011";
        case '4': return "10110100";
        case '5': return "01010101";
        case '6': return "11000110";
        case '7': return "00100111";
        case '8': return "11011000";
        case '9': return "00111001";
        default: return ""; // Handle invalid character
    }
}
