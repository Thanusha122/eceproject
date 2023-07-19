//TRANSMITTOR CODE

#include <MPU6050_tockn.h>
#include <Wire.h>
#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

MPU6050 mpu6050(Wire);

RF24 radio(7, 8);
const byte address[6] = "00001";
struct Data_Package
 {
  int c ;
  int d;
  };
Data_Package data;
void setup() 
{
  radio.begin();
  radio.openWritingPipe(address);
  radio.setPALevel(RF24_PA_MIN);
  radio.stopListening();
  Wire.begin();
  Serial.begin(9600);
  mpu6050.begin();
  mpu6050.calcGyroOffsets(true);
  }



void loop() {
  mpu6050.update();
  data.d=mpu6050.getAccAngleX();
  data.c=mpu6050.getAccAngleY();
  radio.write(&data, sizeof(Data_Package));
  Serial.println(data.d);
  Serial.println(data.c);
 
  delay(15);
  

}


//RECEIVER CODE


#include <SPI.h>
#include <nRF24L01.h>
#include <RF24.h>

int in1=2;
int in2=3;
int in3=4;
int in4=5;
int el1=9;
int el2=10;
int speed=200; //0-250

int a,b;

RF24 radio(7, 8);
const byte address[6] = "00001";
struct Data_Package {
  int c;
  int d;
};
Data_Package data;


void setup() {
  Serial.begin(9600);
  radio.begin();
  radio.openReadingPipe(0, address);
  radio.setPALevel(RF24_PA_MIN);
  radio.startListening();

  pinMode(in1,OUTPUT);
  pinMode(in2,OUTPUT);
  pinMode(in3,OUTPUT);
  pinMode(in4,OUTPUT);
  pinMode(el1,OUTPUT);
  pinMode(el2,OUTPUT);

  }
  
void loop() {
  if (radio.available()) {
    radio.read(&data, sizeof(Data_Package));} 
 Serial.print("x:");   
 Serial.print(data.c);
 Serial.println();
 Serial.print("y:");  
 Serial.print(data.d);
 Serial.println();

a=data.c;
b=data.d;

 

  if(a>30)
  {
    f();
  }
  else if(a<-30){
    bk();
  }
  else if(b<-30){
    l();
  }
  else if(b>30){
    r();
  }
  else{
    s();
  }
  
 
 }

 void f(){
      analogWrite(el1,speed);
      analogWrite(el2,speed);
      digitalWrite(in1,HIGH);
      digitalWrite(in2,LOW);    
      digitalWrite(in3,HIGH);
      digitalWrite(in4,LOW);
 }
  void bk(){
      analogWrite(el1,speed);
      analogWrite(el2,speed);
      digitalWrite(in1,LOW);
      digitalWrite(in2,HIGH);    
      digitalWrite(in3,LOW);
      digitalWrite(in4,HIGH);
 }
 void l(){
      analogWrite(el1,speed);
      analogWrite(el2,speed);
      digitalWrite(in1,LOW);
      digitalWrite(in2,HIGH);    
      digitalWrite(in3,HIGH);
      digitalWrite(in4,LOW);
 }
  void r(){
      analogWrite(el1,speed);
      analogWrite(el2,speed);
      digitalWrite(in1,HIGH);
      digitalWrite(in2,LOW);    
      digitalWrite(in3,LOW);
      digitalWrite(in4,HIGH);
 }
 void s(){
      analogWrite(el1,0);
      analogWrite(el2,0);
      
 }
