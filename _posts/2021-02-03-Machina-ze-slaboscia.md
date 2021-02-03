---
layout: post
title: Machina ze słabością do pomarańczy (czyli RPi z Tensorflow)
category: ml
tags: popolsku, robotyka, rpi, raspberry
featured-img: hoyoung-unsplash-orange
---

Gdy zacząłem parać się elektroniką to na początek stycznia wystawiłem sobie hobbistyczny cel - zrobić zdalnie sterowany samochodzik do końca miesiąca.
Poniższy wpis to dokumentacja tego wydarzenia. Ostatecznie udało się choć jest kilka kosmetycznych szczegółów które wymagają upiększenia. 

![](/assets/img/posts/rpi80b3_edited.jpeg)

Moje starania możesz z łatwością powtórzyć za niewiele ponad 250 zł. 
Oczywiście możesz postawić na profesjonalizm i zamówić w pełni inteligentny i ładnie złożony [JetBot](https://www.nvidia.com/en-us/autonomous-machines/embedded-systems/jetbot-ai-robot-kit/), ale wybór pozostawiam Tobie: robić i się uczyć czy składać tak jak pokazano. Ja zawsze byłem zwolennikiem pierwszego podejścia. Jeśli jednak chcielibyśmy pojść w totalnie ekonomiczne rozwiązanie i nie zależy nam na live-stream z kamery, to nie ma problemu by takie coś zrobić na bazie istniejących mikrokontrolerów, np modułach na ESP (NodeMCU albo ESP32), mają bowiem łączność WIFI “z pudełka”.

## Dlaczego samochodzik?
Od kiedy kupiłem pojedynczy silnik krokowy musiał odczekać swoje. Przeleżał dobre kilka tygodni w pudełku. Ale potem przemyślałem sprawę i zanim zaprojektowałem kolejny system z sensorem pomyślałem - jakie miałem myślenie w dzieciństwie? Zrobić autko, którym da się sterować. Zdalnie sterować. A dziś radio jest już wszędzie, wszystkie nasze mniejsze i większe urządzenia “chodzą po BT / WIFI”. A gdzieś na półce leżał on, czarny i zakurzony mały komputerek.

Na imię mu Raspberry.

Raspberry to komputer, a konkretniej komputer jednopłytkowy czyli SBC (Single Board Computer). Ale co bardzo istotne oprócz łatwego uruchamiania na nim wszystkiego co tylko zechcesz (przeglądarka, Python, Tensorflow itd) mamy na nim wyprowadzenia generalnego użycia (GPIO). Dzięki temu możemy sterować cyfrowymi układami z logiką 5V. A sterownik do silnika jest tu w pełni zgodny.

## Dwa tryby działania
Machiną bawię się dwojako. 

Możliwe jest zdalne sterowanie i obserwowanie w kamerze gdzie jadę. Brak jakiejkolwiek stabilizacji więc obraz jakiego doświadczam wygląda mniej więcej tak. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/umVhn3joyxU" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Jest tak zapewne dlatego że mocowanie to kawałek kartoniku. Gdy tylko dobiorę się do drukarki 3D albo z kolegą zaprojektuje mały stelaż będzie na pewno lepiej.

Mogę też badać działanie uczenia maszynowego. Oczywiście nie trenowałem sieci neuronowej samemu bo choć bawiłem się już ML to nie miałem doświadczenia z badaniem obrazów - Computer Vision.  Dlatego podstawiłem gotowy model w Tensorflow, który jest już wytrenowany na przykładowych obiektach. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/cBvLYKAxYOg" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>

Odkryj więcej [czytając o zbiorze COCO](https://cocodataset.org/#home). Aktualnie program jest tak ustawiony że gdy wykryty zostanie obiekt typu pomarańcz to pojazd wykonuje kilkusekundowy krótką jazdę wprost. Nie ma tu na razie logiki, która dostosowywała by tor jazdy do tego gdzie dokładnie ten pomarańcz stoi (ta pomarańcz?).

## Materiały
Do wykonania użyłem następujących materiałów:
- podwozie do auta z silnikami (~30zł)
- raspberry pi 3 (~170zł)
- kamera (~50 zł)
- powerbank (~10zł)
- 2 akumulatory 3.5V oraz trzymajki (~20 zł)
- sterownik silników (~10zł)

## Wykonanie
### Sprzęt
Sterownik podpięty jest 2 parami przewodów do sterowania sygnałem PWM. Mamy tutaj parę GPIO12/GPIO16 (lewy silnik) i GPIO13/GPIO26 (prawy). Dwa z tych czterech pinów zapewniają sprzętowe PWM. A oto jakie PINy mamy dostępne na Raspberry 3.

![](/assets/img/posts/rpi_pinout.png)


A oto jak połączone są peryferia w moim projekcie. Poniższy rysunek nie pokazuje dokładnych wartości napięć baterii.

![](/assets/img/posts/rpi80b3_bb.png)


### Oprogramowanie
Umówmy się. Nie odkryłem Ameryki tworząc programy. 
Program do sterowania zdalnego to prosta [aplikacja we Flasku](https://github.com/lukaszkuczynski/rpi80b3/blob/master/app.py). Komunikacja z silnikami odbywa się poprzez nadawanie sygnału PWM. 
Z kamerą mam dwie opcje do wyboru. Pierwszą jest zwyczajne przekierowanie obrazu na streaming HTML, który jest widoczny w pliku https://github.com/lukaszkuczynski/rpi80b3/blob/master/serve_camera.py. Drugą jest detekcja obrazu. Jak to już wcześniej zaznaczyłem Tensorflow odwala tutaj brudną robotę. Detekcja polega na prostym klasyfikowaniu obrazów pochodzących z kamery z gotowym modelem zaczytanym z repozytorium. W moim wypadku jest tutaj klasyczny model COCO z 90 klasami obiektów. Zdalne sterowanie jest poprzez korzystanie z prostego [serwisu Flask](https://github.com/lukaszkuczynski/rpi80b3/blob/master/app.py), który jak wszelkie inne tematy uruchamiam na systemie operacyjnym Raspberry jako [serwis Linuxowy](https://github.com/lukaszkuczynski/rpi80b3/blob/master/rpi80b3.service).


## Cele
Aktualnie machina po detekcji pomarańczy nie dostosowuje idealnie toru jazdy do faktycznej lokalizacji obiektu. Raczej wali przed siebie kiedy rozpozna określony obraz. Istnieje możliwość dostosowania takiego toru jazdy do faktycznego położenia obiektu.

Takie auto to idealne poletko dla czujników. Czujniki odległości które umożliwiałyby awaryjny STOP w razie spotkania ze ścianą. Czujnik światła mógłby zapalać światło gdyby go zabrakło. Czujnik obrotów żeby pokazywać aktualną prędkość. Po I2C z RPi komunikuje się już wyświetlacz OLED. Moglibyśmy pokazywać na nim więcej informacji niż temperaturę, np można sygnalizować o potrzebie wymiany baterii.

Nie mamy na razie żadnej regulacji prędkości. Jest to też pole do rozwoju. Moglibyśmy np. po kilkukrotnym wybraniu kierunku przód/tył przyspieszać, ew. zwalniać gdy wybierany jest przeciwny kierunek.

Ułożenie elementów na podwoziu też nie jest optymalne. Gdy lepiej to poukładamy to machina może ogarnąć jeszcze więcej :)
Lekcje
Raspberry to dobra podstawa do tworzenia własnych zabawek. Nie kupuj, twórz własne! Wykorzystanie sieci neuronowych też jest proste jak tylko uda Ci się uruchomić odpowiednią bibliotekę na maszynie. Tensorflow Lite daje dużo możliwości dla twórców i majsterkowiczów zgromadzonych wokół IoT. 
