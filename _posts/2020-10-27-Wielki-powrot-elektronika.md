---
layout: post
title: Wielki powrót Elektronika
category: iot
tags: iot, raspberry, popolsku
featured-img: raspberries
---

# Zmierz sobie temperaturę czyli Wielki powrót Elektronika

Tak, skończyłem szkołę elektroniczną. Tak, miałem dobre oceny. Nie, nigdy nie była to moja pasja. Ale zaczynam się w to wkręcać. Po 10 latach.

Czym się różni szkoła od rozwijania Twojego hobby? Mówią “szkola jak kibel”. I tak, niestety czasem masz wrażenie, że można pracować chętnie, ale tylko wtedy, kiedy Ty czujesz, że ma to jakieś znaczenie. Cel, celowość, wynik, efekt. Kiedy jednak uczysz się, żeby tylko zaliczyć... to ciężko o serce do walki. Kiedy rozdrażniony (smutny) nauczyciel chodzi po klasie i krzyczy patrząć na Twojego pSpice z mozolnie narysowanym nudnym jak śmierć układem z tranzystorem 

> gdzie jest **wynik**?!

..to jaki jest tego **wynik**?. Nie zachęca Cię to tego, żeby elektronika stała się Twoją pasją. A świat (tak, nawet te 10 lat temu) poszedł do przodu i z elektroniki można wycisnąć coś ciekawego, twórczego.

## 3 osoby, które zmieniły moje myślenie
Pierwszą z nich jest pan Mirek, szkolny profesor (a może doktor :)). To on sprawił, że zainteresowałem się programowaniem. Mieliśmy na laborkach procesor sygnałowy DSP. Co prawda zmuszaliśmy mikroprocesor go do zadań mało “sygnałowych” - większość ogarnął by przeciętny Arduino. Ale to wtedy zainteresowałem się rozmową z maszynami — programowaniem.

Potem na scenie pojawia się drugi kolega: Krzysiek. To zapalony miłośnik Pythona, który sprzedał mi chrapkę na języki skryptowe i tanie komputery jednoukładowe. To właśnie wtedy kupiłem swoje pierwsze Raspberry Pi 3B.

Trzeci człowiek to Staszek. Mimo swojego doświadczenia w wielu dziedzinach konwencjonalnego programowania i tworzenia wielu udanych architektur nie ograniczał się do “smutnej Javy”. Raczej tworzył na Arduino i miał smykałkę do nauki dzieci tegoż samego.

Myślę że te 3 osoby miały swój wklad w to, że w tym miesiącu..

## ..odkopałem swoje Raspberry
Po tych 2 latach trwania w nicości przypomniałem sobie o mojej maszynie, która spoczywa gdzieś w czeluściach szafy na balkonie. Chciałem rozszerzyć swoje rozwiązana o obsługę IoT na Azure. Pojawił się wtedy odwieczny problem analizatorów i analityków danych :) "Skąd wziąć dane?" Na początek pomyślałem o konsumpcji API. Ale po co, skoro można sobie takie dane samemu wyprodukować. A jakie dane mogę produkować? Może lepiej po prostu obserwować naturalne źródło danych! Tak, jak 90% początkujących IoTowców zacząłem mierzyć temperaturę.

## Potrzebny sprzęt
Po małych zakupach w sklepie elektronicznym już po 2 dniach od zamówienia (jak oni teraz szybko wysyłają rzeczy z Allegro!) wracałem wesoło z pierwszymi przyrządami, m.in. czujnikiem temperatury i wilgotności. Oczywiście przyda się nam też trochę kabelków i płytka uniwersalna. Mój zestaw pomiarowy nie prezentuje się może nazbyt profesjonalnie ale “robi robotę”. W jego skład wchodzi:
- rzeczone Raspberry PI B3
- czujnik
- kabelki

Komunikacja jest w protokole I2C do którego wystarczą 4 przewody. Ja stosuję następujące kodowanie kolorów: 
- biały : dane
- niebieski: zegar
- czarny / brązowy: masa
- czerwony / pomarańczowy: zasilanie

Uwaga: Czujnik zasilamy napięciem 3.3 V a nie 5 V. 

![](/assets/img/posts/rpi_weather_bb.png)


## Podłączyłem czujnik
Zanim zaczniesz pisać kod w Pythonie i aplikację do odczytów zalecam sprawdzenie czy Twoje Raspberry w ogóle rozpoznaje czujnik. Przyznam, że na początku trochę się napociłem tylko dlatego, że czujnik nie był dobrze dociśnięty do płytki uniwersalnej. W elektronice styki są bardzo ważne.

Twoja maszynka musi być ustawiona na odczyt danych z szyny I2C. Ustawisz to narzędziem `raspbi-config`. Poszukaj czy masz włączoną opcję I2C w *Interfacing Options*.

Czujnik podłączałem zgodnie z instrukcją na [tej stronie](https://www.raspberrypi-spy.co.uk/2016/07/using-bme280-i2c-temperature-pressure-sensor-in-python/). Nie ma potrzeby się dublować, więc nie będę pisał tego co tam sam możesz przeczytać. Jeśli włączona jest opcja I2C, to możesz przetestować czy “widać” czujnik. Odpal komendę `i2cdetect -y 1`. Czujnik powinien być widoczny pod adresem `0x76` albo `0x77`.


## “Napisałem” kod
Kod, który czytał temperaturę i wysyłał to na IoT Azure Hub został wystawiony [na mojego GitHuba](https://github.com/lukaszkuczynski/my-iot-projects/tree/master/pogodynka/raspberrypi). Jeśli chcesz możesz tam spojrzeć, choć jest mocno **wzorowany** na przykładach dostępnych ze strony Microsoft (IoT hub) oraz raspberrypi-spy (komunikacja z BME280). Korzystając z odpowiednich bibliotek kod jest banalnie prosty wystarczyło bowiem wywołać odpowiednią funkcję do odczytu parametrów temperatury, wilgotności itd.

```python
temperature,pressure,humidity = readBME280All()
```

Tak pobrane parametry możesz wysłać dalej lub zapisać zgodnie z Twoimi potrzebami. 

U mnie, czujnik jest podłączony i dzielnie zapodaje dane w chmurę. Tak to wygląda w realu. Moje Raspberry Zero gdzieś tam majaczy za szybą...

![](/assets/img/posts/raspberry_balcony_crop.jpeg)



## Raspberry to za dużo
Do komunikacji po I2C i wysyłania tego na WiFi nie potrzeba aż takiego “kombajnu” jak Raspberry. Dlatego ostatnio wykorzystałem do tego ESP8622 - niezły tańszy odpowiednik Arduino. On też obsłguje standardy WiFi i ma całkiem sporo pinów na wejścia/wyjścia. Zrobiłem na nim już parę doświadczeń, ale może opowiem o tym kiedy indziej.

Póki co, miłego majstrowania! Odkopujcie stare komputery i róbcie inteligencję w domach!