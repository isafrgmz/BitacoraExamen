# BitacoraExamen

## Descripcion 

Esta bitácora consistirá en los avances paso a paso del proyecto final del curso Tecnología I. El proyecto es una experimentación material entre un led, un sensor de sonido y un motor DC, sumado a otros materiales que conforman la obra.

## Materiales
- 2 arduinos
- Bateria 9V
- Motor DC
- Led blanco
- Sensor de sonido Max4466
- Cds rotos

## Códigos

### Código motor

Este código controla la potencia de un motor conectado al pin 6 del Arduino utilizando modulación por ancho de pulso (PWM). En cada ciclo, el programa genera un valor aleatorio de potencia moderada para el motor y realiza una rampa suave de encendido incrementando gradualmente la señal PWM hasta alcanzar ese valor. Luego mantiene el motor encendido por un tiempo aleatorio, tras lo cual aplica una rampa de apagado decreciendo suavemente la señal PWM hasta apagarlo por completo. Finalmente, espera un intervalo aleatorio antes de repetir el proceso. El uso de randomSeed(analogRead(A0)) garantiza variabilidad en los tiempos y potencias generados.

int motorPin = 6;

void setup() {
pinMode(motorPin, OUTPUT);
randomSeed(analogRead(A0)); 
}

void loop() {
int targetPWM = random(80, 140);  
int rampDelay = 15;               
int step = 5;                     

// Rampa de encendido suave
for (int pwm = 0; pwm <= targetPWM; pwm += step) {
analogWrite(motorPin, pwm);
delay(rampDelay);
}

// Tiempo encendido
int onTime = random(200, 500);  
delay(onTime);

// Rampa de apagado suave
for (int pwm = targetPWM; pwm >= 0; pwm -= step) {
analogWrite(motorPin, pwm);
delay(rampDelay);
}

analogWrite(motorPin, 0); 

// Espera antes de volver a encender
int offTime = random(1000, 3000);
delay(offTime);
}

### Código led y sensor sonido

Este código mide la amplitud del sonido captado por un micrófono conectado al pin analógico A0. Durante un intervalo de 50 milisegundos, lee continuamente el valor de la señal de audio y determina el valor máximo y mínimo para calcular la amplitud pico a pico. Luego, convierte esta amplitud en un valor entre 0 y 255, que se utiliza para controlar la intensidad de un LED conectado al pin 9 mediante modulación por ancho de pulso (PWM). 

const int micPin = A0;
const int led1 = 9;  

const int sampleWindow = 50;
unsigned int signalMax = 0;
unsigned int signalMin = 1023;

void setup() {
  Serial.begin(9600);
  pinMode(led1, OUTPUT);

}

void loop() {
  unsigned long startMillis = millis();
  signalMax = 0;
  signalMin = 1023;

  while (millis() - startMillis < sampleWindow) {
    int sample = analogRead(micPin);
    if (sample < 1023) {
      if (sample > signalMax) signalMax = sample;
      if (sample < signalMin) signalMin = sample;
    }
  }

  int peakToPeak = signalMax - signalMin; 
  int intensity = map(peakToPeak, 0, 1023, 0, 255); 

  Serial.print("Amplitud: ");
  Serial.println(peakToPeak);

  analogWrite(led1, intensity);

  delay(10); // pausa
}
