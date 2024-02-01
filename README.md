# Projeto de Dado Eletrônico com MAX7219 (matriz 8x8)
Baseado em https://www.squids.com.br/arduino/index.php/projetos-arduino/projetos-squids/basico/254-projeto-75-criando-faces-de-um-dado-com-modulo-matriz-de-leds-dado-eletronico-arduino?highlight=WyJtYXg3MjE5Il0=

![image](https://github.com/maf27br/ARD.MAX7219.Dado/assets/68168344/f41795db-65be-4547-a8e3-4363cef4e30d)

Foto da Montagem:
![20240201_190839](https://github.com/maf27br/ARD.MAX7219.Dado/assets/68168344/7cf70532-d7d9-4324-8ced-0f26d47f31ee)

Código:
```
#include <LedControlMS.h>
#include "faces.h"

int DIN = 11;
int CS = 7;
int CLK = 13;

const int faces[] = { F1, F2, F3, F4, F5, F6 };
const byte btn = 12;
LedControl lc = LedControl(DIN, CLK, CS, 0);

void setup() {
  lc.shutdown(0, false);
  lc.setIntensity(0, 8);
  lc.clearDisplay(0);
  pinMode(btn, INPUT_PULLUP);
  randomSeed(analogRead(0));
  drawScreen(faces[random(0, 6)]);
}

void loop() {
  if (!digitalRead(btn)) {         // se o botão for acionado (digitalRead(btn) = 0 - pullup)
    while (!digitalRead(btn)) {};  // aguarda soltar o botão
    lc.clearAll();
    delay(200);
    diceRound();  // roda uma jogada do dado
  }
}

void diceRound() {
  byte result = 0;                   // inicializa a variável "result" com 0
  byte rodada = random(15, 35);      // gera comprimento da rodada, quantos números passarão
  for (byte i = 0; i < rodada; i++)  // loop de 0 até comprimento da rodada
  {
    result = random(0, 6);      // gera número de 0 a 5, devido a string começar em 0 e finalizar em 5
    drawScreen(faces[result]);  // mostra as faces do dado até o dado parar
    delay(50 + i * 10);         // espera para passar cada número (varia velocidade)
  }
}
void drawScreen(byte buffer[]) {
  for (int i = 0; i < 8; i++) {                       // linhas
    for (int j = 0; j < 8; j++) {                     // colunas
      lc.setLed(0, j, i, bitRead(buffer[i], 7 - j));  // usando bitRead() INVERTI i com j para display em pé
      //lc.setLed(0, i, j, (buffer[i] >> 7-j) & 0X01); // usando operador bitwise
    }
  }
}
```
