#define DT A2
#define SCK A3
bool Down = false; 
bool on = true;
int counter = 0;
int p1 = A0;
int p2 = A1;
int LI1 = 160;
int LI2 = 160;
int bzr = 4;
int ir1 = 6;
int ir2 = 5;
bool ir1o = false;
bool ir2o = false;
bool ir1n = false;
bool ir2n = false;
int people = 0;
bool entering = false ;
bool exiting = false;
int trig[] = {34,2,36,12};
int echo[] = {35,3,37,11};
const float vs = 343.2;
int piezoOutputs[] = {A5, A6, 5, 6};
int impacts[4];
int threshold = 300;
float D1,D2,v;
int I = 0;
float ceilheight = 100.0;
float duration[4], d[4];
int wait = 15;
int dT = 1000;


unsigned long readCount()
{
  unsigned long Count;
  unsigned char i;
  pinMode(DT, OUTPUT);
  digitalWrite(DT,HIGH);
  digitalWrite(SCK,LOW);
  Count=0;
  pinMode(DT, INPUT);
  while(digitalRead(DT));
  for (i=0;i<24;i++)
  {
    digitalWrite(SCK,HIGH);
    Count=Count<<1;
    digitalWrite(SCK,LOW);
    if(digitalRead(DT)) 
    Count++;
  }
  digitalWrite(SCK,HIGH);
  Count=Count^0x800000;
  digitalWrite(SCK,LOW);
  return(Count);
}

int Max(int arr[], int n){
  int m = arr[0];
  for (int i = 0; i < n; i++)
  {
    if (arr[i] > m) {m = arr[i];}
  }
  return m;
}
float Min(float arr[], int n){
  float m = arr[0];
  for (int i = 0; i < n; i++)
  {
    if (arr[i] < m) {m = arr[i];}
  }
  return m;
}

int measure(int n){
  for (int i = 0; i < n; i++)
  {
    impacts[i] = analogRead(piezoOutputs[i]);
  }
  return Max(impacts, n);
}

bool isUp(){
  bool us = (distance() < ceilheight);
  Serial.println(analogRead(p1));
  Serial.println(analogRead(p2));
  bool lm = ((analogRead(p1) < LI1)&&(analogRead(p2) < LI2));
  Serial.print("isUp  : ");
  Serial.print(lm);
  Serial.println(us);
  return lm||us;
}

void enter(){
  // Burglar alarm system.
  ir1o = ir1n;
  ir2o = ir2n;
  ir1n = digitalRead(ir1);
  ir2n = digitalRead(ir2);
  Serial.print(entering);
  Serial.print(" "); 
  Serial.print(exiting);
  Serial.print(" "); 
  Serial.print(ir1n);
  Serial.print(ir2n);
  Serial.println(people);
  if(ir1o && ir2o){
    if(ir2n && (!ir1n)){
      entering=true;
      exiting=false;
    }
    else if(ir1n && (!ir2n)){
      exiting=true;
      entering=false;
    }
    else if(!ir1n && !ir2n){
      Serial.println("Too fast !");
    }
  }
  else if(ir1n && ir2n){
    if(ir2o && (!ir1o) && exiting){people--;}
    else if(ir1o && (!ir2o) && entering){people++;}
    else {Serial.println("Too fast again!");}
    entering=false;
    exiting=false;
  }   
}

bool checkImpact(){
  //Return true if peizoelectric gives a reading more than the threshhold,
  int m = measure(2);
    Serial.print("Piezo : ");
    Serial.println(m);
  if(m > threshold){return true;}
  return false;
}
void Buzz(){
  //Buzzer
  Serial.println("Buzz");
  digitalWrite(bzr,HIGH);
}

void TurnON(){
    digitalWrite(8,HIGH);
    pinMode(bzr, OUTPUT);
    for(int i=0;i<4;i++){
      pinMode(trig[i], OUTPUT);
    }
    on = true;
    delay(1000);
}
void TurnOFF(){
    digitalWrite(8,LOW);
    pinMode(bzr, INPUT);
    for(int i=0;i<4;i++){
      pinMode(trig[i], INPUT);
    }   
    on = false;
}

float distance(){
  int m = 0;  
  Serial.print("Ultrasonic : ");
  for(int i=0;i<3;i++){
  // Send ultrasonic pulse
  digitalWrite(trig[i], LOW);
  delayMicroseconds(2);
  digitalWrite(trig[i], HIGH);
  delayMicroseconds(10);
  digitalWrite(trig[i], LOW);
 
  // Measure echo duration
  duration[i] = pulseIn(echo[i], HIGH);
  if(duration[i] == 0)continue;  
  // Calculate distance in meters
  d[i] = duration[i] * 0.5 * vs / 10000.0; 
  if(d[i] > m){m=d[i];}
  Serial.print(d[i]); 
  Serial.print(" ");
  delay(100);
    // Divide by 2 to account for round-trip
  }
  Serial.println("");
  Serial.print("Minimum : ");
  Serial.println(m);
  return m;
}

float Update() {
  delay(10);
  if(I>15){
    I = 0;
    D2 = D2/15;
    v = (D2-D1)/150.0;
    D1 = D2;
    D2 = 0.0;
  }
  I++;
  D2 += distance();
}

void setup(){
  //pin connections and declarations
    Serial.begin(9600);
    pinMode(p1,INPUT);
    pinMode(p2,INPUT);
    pinMode(8,OUTPUT);
    for(int i=0;i<4;i++){
      pinMode(echo[i], INPUT);
    }
    /*
    delay(5000);
    LI1 = analogRead(p1);
    LI2 = analogRead(p2);
    */
}
void loop(){
  enter();
  if(people!=1){
    TurnOFF();
    return;
  } else {
    if(!on){TurnON();}
  } 
  if(!Down){
    if(checkImpact()){
      Down = true;
      counter = 0;
    }
    else {
      return;
        }
  }
  if(counter<wait){
    if(isUp()){
      counter = 0;
      Down = false;
     } else{
      counter++;
      Serial.println(counter);
      delay(dT);
      }
   } else {
     Buzz();
     delay(5000);
      }
  Serial.println("");
  
}
