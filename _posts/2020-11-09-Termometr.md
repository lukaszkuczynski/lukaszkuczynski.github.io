---
layout: post
title: Mierzymy temperaturę z ESP8266
category: iot
tags: iot, esp12, esp2866, popolsku
featured-img: thermometer_real_photo
---

# Termometr z wyświetlaczem - zrób to sam
W dzisiejszym epizodzie podzielę się swoją drogą jaką przebyłem by stworzyć samemu termometr z wyświetlaczem oparty o znany i lubiany moduł ESP8266, płytka NodeMcu za dosłownię parę zł.

## ESP - jak to się zaczęło
Płytka trafiła do mnie przypadkiem. Potrzebowałem “dobić czymś do koszyka” podczas zakupów na jednym ze znanych i lubianych portali. Na moje ręcę napatoczyło sie urządznie opisane jako “moduł WIFI”. Patrzę, a to normalnie jest pełnoprawny mikrokontroler za 18zł. Pomyślałem że to bardzo ciekawe biorąć pod uwagę, że w podobnej cenie dostaniemy może i Arduino, ale w wersji ubogiej. Tak to do mojego domu zawitał on, ESP8266 z ESP12E. Z poprzednich lat miałem wyobrażenie że start z mikroprocesorami będzie dużo trudniejszy, że będę musiał inwestować w jakieś programatory. Pamiętam, tak właśnie było za czasów mojej nauki szkolnej. Na szczęście technika poszła do przodu także w tej dziedzinie, płytka ma wbudowane maszynki do programowania.

## Moje IDE
Jestem programistą, więc nieco czasu poświęciłem na to z czym to się je i jak to się programuje. Oczywiście istnieje droga do serca tego urządzenia używając powszechnie używanych języków programowania: JS czy Python. Jako zwolennik Pythona miałem nawet przez chwilę myśl żeby pójść tą *py*-ścieżką. Ale ostatecznie pomyślałem — działamy na mikrokontolerze. Tutaj standardem jest C++. Pozostańmy przy standardach. 

A jakie do tego IDE? Tu pierwsze programy z softem tworzyłem pod ArduinoIDE, ale szybko przesiadłem się na rozszerzenie do Visual Studio Code [Platform IO](https://platformio.org/install/ide?install=vscode), jako że na co dzień bardzo dużo korzystam z VS Code. To bezpłaty, ale na prawdę świetny soft od Microsoftu!

## Sprzęt
Do wykonania układu użyłem:
- płytka uniwersalna
- kilka kabelków łączeniowych
- ESP8266 na NodeMCU, procesor ESP-12F
- czujnik temperatury BMP180
- wyświetlacz LED ze sterownikiem I2C 1602A

Z uwagi na prostotę do wszelkiej komunikacji używam I2C, a ponieważ adresacja modułów jest unikalna dla różnych modułów to można je podłączyć ze sobą równolegle. Zobacz zresztą schemat poniżej. Zwróć uwagę że wyświetlacz i czujnik zasilane są z 2 różnych szyn bo potrzebują innych poziomów napięcia.

![](/assets/img/posts/thermometer_wiring.jpg)


## Program
Software jaki do tego powstał nie jest zbyt skomplikowany. Tak na prawdę zaproponowane podejście można zmienić. Potrzebne jest nam tylko odczytanie temperatury i wysłanie odpowiednich linii do wyświetlacza. Odpowiednie biblioteki dołączone są w pliku konfiguracji projektu PlatformIO
```ini
[env:nodemcuv2]
platform = espressif8266
board = nodemcuv2
framework = arduino
lib_deps =
   adafruit/Adafruit BMP085 Library@^1.1.0
   adafruit/Adafruit Unified Sensor@^1.1.4
   adafruit/Adafruit MQTT Library@^2.0.0
   marcoschwartz/LiquidCrystal_I2C@^1.1.4
monitor_speed = 115200

```

### Temperatura

Komunikacja z czujnikiem temperatury odbywa się dzięki bibliotece *Adafruit BMP085*. Mimo, że dotyczy ona innego czujnika jest w pełni kompatybilna z moim **BMP180**. Deklaracja biblioteki jest możliwa jako załączenie odpowiedniego pliku nagłówkowego. Potem powinniśmy stworzyć obiekt tej klasy.

```cpp
#include <Adafruit_BMP085.h>
Adafruit_BMP085 bme;
```

Po próbie inicjalizacji w funkcji `setup` odczyt parametrów w pętli następuje poprzez takie wywołania

```cpp
 temperature = bme.readTemperature();
 pressure = bme.readPressure() / 100.0F;
```
Inne czujniki oferują bogatsze zestawienie parametrów, np. **BME280** umożliwia odczyt wilgotności.

### LCD
Przed rozpoczęćiem pisania kodu obawiałem się że dostęp do LCD będzie trudniejszy, np. będę musiał samodzielnie rysować piksele albo dbać o synchronizację odpowiednich linijek, itd. Ale na szczęście z modułem Adafruit i odpowiednim sprzętem życie jest prostsze. Użyty tu wyświetlacz ma konwerter I2C, więc komunikacja następuje tu w tym protokole.

```cpp
#define LCD_COLUMNS_COUNT 16
#define LCD_ROWS_COUNT    2
 
LiquidCrystal_I2C lcd(0x27, LCD_COLUMNS_COUNT, LCD_ROWS_COUNT); 
```
Po inicjalizacji modułu i zaświeceniu tylnego światełka
```cpp
void display_values_lcd(float temperature, float pressure) {
 lcd.clear();
 lcd.setCursor(0, 0);
 lcd.printf("%.1f C", temperature);
 lcd.setCursor(0,1);
 lcd.printf("%.1f hPa (Luk)", pressure);
}
```
Jak widzisz, przed rozpoczęciem pisania musimy ustawić kursor w odpowiednim miejscu i jako że mam dwulinijkowy wyświetlacz wykorzystam zarówno wiersz nr 0 i nr 1.

## Posłowie
A oto jak wygląda instalacja u mnie wraz z krótką informacją nt. warunków pogodowych w moim pokoju:

![](/assets/img/posts/thermometer_real_photo.jpg)

**Pełny kod** jak zawsze [zamieszczam na GitHub-ie](https://github.com/lukaszkuczynski/my-iot-projects/blob/master/termometr/src/main.cpp). Jak zauważysz jest tam komunikacja po MQTT, a to po to, żeby raportować aktualne pomiary do automatyki domowej. Testuję aktualnie zarówno OpenHab jak i HomeAssistant.