#include <HardwareSerial.h>
#include <DFRobotDFPlayerMini.h>

#define TRIG_PIN 5
#define ECHO_PIN 18
#define GREEN_LED 21
#define RED_LED 22

#define DF_RX 16
#define DF_TX 17

HardwareSerial mySerial(2);
DFRobotDFPlayerMini myDFPlayer;

void setup() {
    Serial.begin(115200);
    mySerial.begin(9600, SERIAL_8N1, DF_RX, DF_TX);

    pinMode(TRIG_PIN, OUTPUT);
    pinMode(ECHO_PIN, INPUT);
    pinMode(GREEN_LED, OUTPUT);
    pinMode(RED_LED, OUTPUT);

    if (!myDFPlayer.begin(mySerial)) {
        Serial.println("DFPlayer not detected!");
        return;
    }
    myDFPlayer.volume(20);
}

float getDistance() {
    digitalWrite(TRIG_PIN, LOW);
    delayMicroseconds(2);
    digitalWrite(TRIG_PIN, HIGH);
    delayMicroseconds(10);
    digitalWrite(TRIG_PIN, LOW);

    long duration = pulseIn(ECHO_PIN, HIGH);
    float distance = (duration * 0.0343) / 2;
    return distance;
}

void loop() {
    float distance = getDistance();
    Serial.println(distance);

    if (distance >= 8 && distance <= 12) {
        digitalWrite(GREEN_LED, HIGH);
        digitalWrite(RED_LED, LOW);
    } else {
        digitalWrite(GREEN_LED, LOW);
        digitalWrite(RED_LED, HIGH);
        myDFPlayer.play(1);
        delay(3000);
    }
    delay(500);
}
