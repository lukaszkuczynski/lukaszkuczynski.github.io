---
layout: post
title: Szafa świeci - automatyka domowa z ESP
category: iot
tags: iot, esp12, esp8266, popolsku
featured-img: streetlight
---

Projekt powstał jako spełnienie potrzeb domowych - ciemność w szafie. Któregoś pięknego popołudnia zostałem zapytany przez małżonkę czy jest możliwość instalaji światła w szafie. W poniższym poście zaprezentuję jak zaprząc do pracy **ESP** do sterowania oświetleniem i włączyć to w system automatyki domowej. Idąc dalej możemy tak wpiąć jakiekolwiek urządzenie zgodne z protokołem MQTT. W moim wypadku automatyka domowa sterowana jest przez **Home Assistant** zainstalowany na **RaspberryPi 3B**.

### *Disclaimer*
>Tak, wiem, że istnieje prostsze rozwiązanie. Jeśli chcemy uzyskać świecenie przy otwarciu drzwi nie potrzebujemy do tego mikrokontrolera. Ale jak wtedy uzyskamy informację o otwarciu drzwi przez automatykę? Poza tym, był to miły sposób na przećwiczenie komunikacji GPIO i sterowania większymi prądami poprzez przekaźnik.

## Projekt
Urządzenie działa w następujący sposób. Gdy szafa jest otwierana to następuje zapalenie świateł. Gdy jest zamykana to gasimy światła. Proste, prawda? :) Dodatkowo wysyłany jest sygnał po WIFI o zmianie stanu przy każdej okazji.

Do wykonania użyłem następujących elementów:
- NodeMcu ESP8622
- przekaźnik
- czujnik zbliżeniowy magnetyczny
- taśma LED
- zasilacz do LED
- zasilacz USB 5V

W projekcie użyto przekaźnik do załączania obwodu zasilającego LED. Jest tam 12V, co znacznie przekracza możliwości napięciowe procesora ESP. Z kolei czujnik zbliżeniowy zwiera obwód, który podaje sygnał logiczny 1 na jeden z PINów procesora ustawionych na odczyt.

![](/assets/img/posts/wardrobe_led_bb.png)


## Program
Program do obsługi danych jest bardzo prosty. Tak, jak opisano w sekcji *Disclaimer* ponieważ następuje bardzo proste tłumaczenie jednego stanu logicznego wejściowego (otwarty / zamknięty) na drugi stan logiczny (zaświeć / zgaś) nie potrzeba tutaj nawet mikrokontrolera. Ale, bo mogę (*because I can*) i bo to jest zabawa (*for fun*) jest tu jeszcze dołożona inteligencja w postaci raportowania stanu do automatyki domowej. Poza tym, możemy łatwo zaaplikować logikę np. opóźnienia albo uzależnienia światła od pory dnia lub tygodnia.

Poniżej podaję implementację logiki włączającej światła zależnie od stanu czujnika zbliżeniowego:

```cpp
void updateLEDstatusAsToDoorStatus()
{
 int reedSensorValue = digitalRead(REED_SENSOR_PIN);
 bool isDoorOpen = reedSensorValue == 0;
 if (isDoorOpen)
 {
   Serial.println("Door opened, let us turn on the light");
   digitalWrite(RELAY_PIN, LOW);
 }
 else
 {
   Serial.println("Door closed, lights down!");
   digitalWrite(RELAY_PIN, HIGH);
 }
 send_mqtt_door_state(isDoorOpen);
}
```
Pozostały kod jak zawsze został wypchany [na GitHub](https://github.com/lukaszkuczynski/my-iot-projects/blob/master/openlight/src/main.cpp).


## MQTT
Komunikacja naszego urządzenia z automatyką domową odbywa się poprzez sieć WIFI, wszak rodzina modułów ESP12 była przede wszystkim modułami WIFI. Jednym ze sposobów wymiany danych jest lekki protokół MQTT. W takim układzie dane przekazywane są w architekturze publish-subscribe, tak więc potrzebujemy przynajmniej 3 punktów komunikacji:
- host
- klient
- subskrybent

W moim wypadku hostem jest Raspberry. Do zabawy na Debian-opodobnym systemie zainstaluj paczkę rodziny *mosquitto*

```bash
sudo apt install -y mosquitto mosquitto-clients
```
Do przetestowania takiego układu możesz odpalić dwa programy `mosquitto_pub` i `mosquitto_sub`, które tak jak wskazują ich nazwy, służą do publikowania i subskrybowania wiadomości. Przykład przetestowania opisany jest na [fajnym portalu dla miłośników internetów rzeczy](https://randomnerdtutorials.com/testing-mosquitto-broker-and-client-on-raspbbery-pi/).

## Konfiguracja Home Assistant
Przez krótki czas na mojej “malince” testowałem OpenHAB, ale ostatnio zmigrowałem się do HomeAssistant. Jest lżejszy i osobiście przyjemniej mi się z nim pracuje. Jest to system do automatyki domowej, pod który możesz podłączać czujniki i elementy wykonawcze i tworzyć automatyzację. Konfiguracja jest wyklikiwalna, ale można ją także ustawić poprzez pliki konfiguracyjne YAML. Oto przykład deklaracji sensora MQTT w systemie.

```yaml
mqtt:
  broker: 192.168.0.XXXX

sensor:
  - platform: mqtt
    state_topic: /bedroom/wardrobe_door
    name: wardrobe_door
  - platform: mqtt
    state_topic: /bedroom/power
    name: wardrobe_esp_voltage
    unit_of_measurement: V
```
Po udanej konfiguracji jesteśmy w stanie zobaczyć stan otwarcia naszych drzwi w aplikacji, tutaj przykład z instalki na moim IPhone podłączonym pod domową WIFI

![](/assets/img/posts/home_assistant.jpeg)

## Wnioski i przyszłość
Aktualnie wykorzystuję płytkę developerską ESP - NodeMcu. Oczywiście jest to tymczasowe rozwiązanie i jednym z pomysłów uproszczenia projektu jest wykorzystanie gotowych przekaźników z ESP01, [np takich](https://pl.aliexpress.com/item/32819462977.html).

Najtrudniejsze z całego projektu oceniam: **łączenie taśm LED**. Konektory sprawiły mi dużo bólu i trudności :) Poza tym, nieco czasu spędziłem na szukaniu rozwiązań problemów gdy HomeAssistant był uruchomiony **z obrazu Dockera** - były problemy z siecią wewnętrzną. Ale poza tym zabawa była fajna i nie było to rocket science, wszak połączyliśmy ledwie jedno wejście z jednym wyjściem. Dobrym doświadczeniem jest to, że konfiguracja HomeAssistant z MQTT jest na prawdę prosta i można go łatwo zainstalować na każdym systemie z Pythonem. Poza tym, cieszę się, jak łatwo sterować dużymi napięciami i prądami z tego pięknego chińskiego maleństwa - ESP8266.