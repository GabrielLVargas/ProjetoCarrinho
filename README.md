//Vinicius Kendy Yamane 16.02248-3
//Gabriel Luppo Vargas 15.00843-6
// PROJETO ESTUFA

#include "mbed.h"
#include "Adafruit_SSD1306.h"
#include "DHT11.h"
#include "stdio.h"

SPI spi(p5,p6,p7);
Adafruit_SSD1306_Spi lcd(spi,p21,p22,p23);
DigitalIn presenca(p24);
Timer t;

Ticker flipper;
DHT11 d(p15);

PwmOut pwm1(p25);
PwmOut pwm2(p26);
AnalogIn ldr(p16);

InterruptIn umid(p14);
DigitalOut bomba(p13);

char nome[10] = "Bem Vindo";
char nome1[25] = "";
char nome2[25] = "";

void ReadTH(){
    int s,m,n;
    s = d.readData();
    if (s == DHT11::OK){
        n = sprintf(nome1, "Temperatura: %i",d.readTemperature());
        m = sprintf(nome2, "Umidade: %i",d.readHumidity());    
    }
}

void Bomba(){
    bomba = 1;
    wait(0.5);
}

void Luz(){
    if (ldr == 0.5f){
        pwm1.write(0.5);
        pwm2.write(0.5);
    }
    else if (ldr == 0.6f){
        pwm1.write(0.4);
        pwm2.write(0.4);
    }
    else if (ldr == 0.7f){
        pwm1.write(0.3f);
        pwm2.write(0.3f); 
    }
    else if (ldr == 0.8f){
        pwm1.write(0.2f);
        pwm2.write(0.2f);
    }
    else{
        pwm1.write(0.8f);
        pwm2.write(0.8f);
    }
}

int main() {
    ReadTH();
    lcd.clearDisplay();
    lcd.setTextCursor(40,20);
    for(int i = 0; i < strlen(nome); i++)
        lcd.writeChar(nome[i]);
    lcd.display();
    
    wait(2);
    
    pwm1.period(0.01f);
    pwm2.period(0.01f);
    
    flipper.attach(&ReadTH, 4.0);
    flipper.attach(&Luz, 2.0);
    
    umid.rise(&Bomba);
    
    t.start();
    
    while(1) {
        if (presenca==1){
            t.reset();
            lcd.clearDisplay();
            lcd.setTextCursor(0,1);
            for(int i = 0; i < strlen(nomeNovo1); i++)
                lcd.writeChar(nomeNovo1[i]);
            lcd.setTextCursor(0,21);
            for(int i = 0; i < strlen(nomeNovo2); i++)
                lcd.writeChar(nomeNovo2[i]);
            lcd.display();
        }
        if (presenca==0 & t.read()<=10){
            lcd.clearDisplay();
            lcd.setTextCursor(0,1);
            for(int i = 0; i < strlen(nome1); i++)
                lcd.writeChar(nome1[i]);
            lcd.setTextCursor(0,21);
            for(int i = 0; i < strlen(nome2); i++)
                lcd.writeChar(nome2[i]);
            lcd.display();
        }
        if (presenca==0 & t.read()>=10){
            lcd.clearDisplay();
            lcd.display();
        }
    }
}
