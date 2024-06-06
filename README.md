# Edge - Global Solution

# Projeto de medição da temperatura da água dos oceanos

## Equipe

Guilherme Oliveira, RM 555180 <br>
Rafael Gaspar, RM 557228 <br> 
Vinicius Monteiro, RM 555088 <br>
 
## Link da simulação em Wokwi
https://wokwi.com/projects/399844002451478529

## Dependências:
1. Sensor de temperatura (DS18B20)
2. 2x LEDs (um vermelho e um azul)
3. 3x Resistores, 1x de 4.5KΩ e 2x de 220Ω
4. Tela LCD (I2C)
5. Conectores macho / fêmea e macho / macho
6. Buzzer

## Conceito do projeto / seu funcionamento

Este projeto pode ser adaptado para várias aplicações ambientais que beneficiam os oceanos e corpos d'água em geral. Um dos benefícios é o monitoramento
da temperatura da água. A temperatura da agua é um fator crítico na saúde dos ecosistemas aquáticos. Alterações na temperatura podem afetar a vida marinha 
de varias maneiras, incluindo:

1. Biorritmos e ciclos de vida: Muitos organismos marinhos dependem de temperaturas específicas para processos como reproduçõa e migração.
2. Distribuição de espécies: Espécies podem migrar para áreas com temperaturas mais adequadas, o que pode afetar a biodiversidade.
3. Doenças: Temperaturas mais altas podem facilitar a proliferação de doenças e algas nocivas.

Ao monitorar a temperatura da água, este projeto pode ajudar a detectar e prever mudanças e anomalias que podem indicar problemas ambientais.


### Reprodução visual:

![image](/img/Captura%20de%20tela%202024-06-06%20163507.png)
![image](/img/Captura%20de%20tela%202024-06-06%20163649.png)


### Código utilizado:

```C++
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <OneWire.h>
#include <DallasTemperature.h>

LiquidCrystal_I2C lcd(0x27, 16, 2); 

#define ONE_WIRE_BUS 2
OneWire oneWire(ONE_WIRE_BUS);
DallasTemperature sensors(&oneWire);


const int ledVermelho = 8;
const int ledAzul = 7;
const int buzzer = 9;

const float TEMPERATURA_LIMITE = 21.5;

void setup() {
  lcd.begin(16, 2); 
  lcd.backlight();
  
  sensors.begin();

  pinMode(ledVermelho, OUTPUT);
  pinMode(ledAzul, OUTPUT);
  pinMode(buzzer, OUTPUT);
  
  Serial.begin(9600);
}

void loop() {
  sensors.requestTemperatures();
  float temperatura = sensors.getTempCByIndex(0);

  if (temperatura < 0) {
    temperatura = 0;
  } else if (temperatura > 25) {
    temperatura = 25;
  }
  
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Temp: ");
  lcd.print(temperatura);
  lcd.print((char)223);
  lcd.print("C");
  
  Serial.print("Temperatura: ");
  Serial.print(temperatura);
  Serial.println(" C");
  
  if (temperatura >= TEMPERATURA_LIMITE) {
    digitalWrite(ledVermelho, HIGH);
    digitalWrite(ledAzul, LOW);
    
    for (int i = 0; i < 3; i++) {
      digitalWrite(buzzer, HIGH);
      delay(200); 
      digitalWrite(buzzer, LOW);
      delay(200); 
    }
  } else {
    digitalWrite(ledVermelho, LOW);
    digitalWrite(ledAzul, HIGH);
  }

  delay(1000);
}
```