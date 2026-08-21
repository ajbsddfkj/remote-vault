
## **1. Wstęp**

#### 1.1. Tło problemu i motywacja (Dlaczego CGH jest dziś kluczowe?)

>zacznijmy od tego czym jest rzeczywistość rozszerzona (AR). powiemy jaki jest problem ze współczesnymi systemami vr i ar, czemu iluzja głębi powoduje nudności i zmęczenie oczu [[depth accomodation]], powiemy też o występowaniu konfliktu akomodacyjno-wergencyjnym przy sesjach oglądania gogli vr. 

#### 1.2. Cel i zakres pracy (Wskazanie, że celem jest implementacja, optymalizacja oraz porównanie algorytmów CGH na potrzeby systemów AR)
>Dlatego po tym jak wystarczająco dobrze nakreślę problemy współczesnego VR i AR przedstawię technologię, która eliminuje te problemy czyli podstawa merytoryczna mojej pracy - holograficzny wyświetlacz dooczny w trybie AR. 
>Trzeba w tym rozdziale jasno określić cele, to będzie potem podstawa do mierzenia jakości twojej pracy. 
>	Jeżeli powiem, że celem jest optymalizacja algorytmu do tworzenia wyświetlacza czasu rzeczywistego, no to chujowo będzie jeśli w metrykach okaże się, że hologram tworzy się 5 minut. 
>	Jeśli powiem, że wyświetlacz ma być szerokokątny i pracować w jak największym FOV no to chujowo będzie jeśli mój rzut obiektu nie będzie perspektywiczny i zakres kątowy będzie odpowiadał fragmentowi pola modulatora. 
>to się rozumie samo przez się. cele pracy muszą być dobrze określone, aby efekt pracy był miarodajny. dlatego cele obierz w jasny sposób i nakieruj pracę na odpowiedni tor. Takie cele chcę sobie obrać:
>	- optymalizacja czasowa
>	- redukcja szumu plamkowego
>	- rekonstrukcja głębi obiektów 3D z poprawnymi wskaźnikami głębi
>	- usuwanie niewidocznych powierzchni
>	- poszerzenie kąta widzenia FOV
>	- implementacja w systemach AR
>zakres pracy to obrane przeze mnie decyzje konstrukcyjne, które nadały pracy taki kształt jaki ma. Tutaj muszę stanąć w obronie moich decyzji i wyborów i uargumentować dlaczego te wybory najlepiej spełniają moje postawione cele. W skład zakresu mojej pracy wchodzą między innymi:
>	- stworzenie sceny 3D w jednostkach metrycznych z możliwością skalowania rozmiaru 
>	- renderowanie obiektu z zastosowanym algorytmem usuwania niewidocznych punktów
>		- rzutowanie ortogonalne
>		- (TO DO) z rzutowaniem perspektywicznym
>	- generowanie hologramu metodą chmury punktowej
>	- (TO DO) usuwanie pikseli nieuczestniczących w rekonstrukcji z algorytmem obszaru wsparcia
>	- przyspieszenie algorytmu z frameworkiem CUDA ([[release no. 1]]) do obliczeń równoległych
>	- (TO DO) kwantyzacja pól falowych dla różnych głębokości z algorytmem LUT i WRP 
>	- odzyskiwanie fazy z algorytmem Gerchberga-Saxona
>	- alternatywny kod porównawczy mojego promotora
>	- numeryczna rekonstrukcja z wykorzystaniem algorytmu S-FFT
>	- (TO DO) optyczna rekonstrukcja w systemie AR mojego promotora
>	- (TO DO) analiza porównawcza wykorzystanych systemów, algorytmów, parametrów, układów i 
#### 1.3. Układ pracy (Krótki opis zawartości kolejnych rozdziałów)
>układ został zarysowany w moim poprzednim zakresie pracy, ale teraz żeby kurwa jebany laik  mógł to zrozumieć, muszę kompleksowo naprostować kolejność działania w zakresie pracy. tutaj muszę uniknąć powtórzeń. W tym zdecydowanie krótkim i zbieżnym opisie muszę opowiedzieć co jest tematem pracy i zapowiedzieć czytelnikowi co w tej mojej pracy odnajdzie wartościowego.

## **2. Podstawy teoretyczne holografii i systemów AR/VR**

#### 2.1. Fizyczne podstawy światła i dyfrakcji (Interferencja, propagacja fali)
    
#### 2.2. Cyfrowa holografia (CGH) a holografia tradycyjna
    
#### 2.3. Rola holografii w nowoczesnych systemach wyświetlania (Zastosowanie i znaczenie w AR/VR)
    

## **3. Przegląd obecnych metod generowania hologramów (State of the Art)**

#### 3.1. Reprezentacja scen 3D w grafice komputerowej (Chmury punktów vs. siatki wielokątów/warstwy)
    
#### 3.2. Przegląd algorytmów CGH (Zalety i wady istniejących rozwiązań, algorytmy iteracyjne np. Gerchberg-Saxton)
    

## **4. Projekt i optymalizacja badanych algorytmów CGH**

#### 4.1. Autorski algorytm oparty na chmurze punktów (Twoja unikalna metoda, opis matematyczny)
    
#### 4.2. Alternatywna metoda generowania hologramów _(Tu wpleciesz nowy kod od promotora)_
    
#### 4.3. Przepływ danych (Data pipeline) – diagramy i schematy blokowe transformacji sceny fizycznej w wirtualną
    
#### 4.4. Strategie optymalizacji (Metody redukcji czasu obliczeń i poprawy jakości)
    

## **5. Architektura oprogramowania i implementacja w języku Python**

#### 5.1. Struktura bazy kodu (Opis modułów i klas – napisany tak, by był zrozumiały dla laika)
    
#### 5.2. Wykorzystane biblioteki i narzędzia (Dlaczego wybrano konkretne pakiety)
    
#### 5.3. Wyzwania implementacyjne i sprzętowe (Zarządzanie pamięcią, wąskie gardła wydajności/performance bottlenecks, rozwiązania typu broadcasting czy zrównoleglanie obliczeń)
    

## **6. Wyniki eksperymentów i ewaluacja**

#### 6.1. Metodyka badań i parametry wejściowe (Opis obrazów testowych, odległość, długość fali)
    
#### 6.2. Rekonstrukcja numeryczna i metryki jakości (Analiza jakościowa: PSNR, SSIM, wydajność dyfrakcyjna)
    
#### 6.3. Analiza wydajnościowa (Czas wykonania, czas na batch, liczba iteracji)
    
#### 6.4. Optyczna rekonstrukcja w układzie AR _(Ten punkt zadowoli promotora – fizyczne testy wygenerowanych przez Ciebie hologramów w jego układzie)_
    
#### 6.5. Porównanie wyników (Zestawienie Twojej metody z "nowym kodem")
    

## **7. Podsumowanie i wnioski**

#### 7.1. Wnioski końcowe (Czy zrealizowano cele? Oczywiście, że tak!)
    
#### 7.2. Ograniczenia rozwiązania (Szczera ocena, np. brak gotowości na wideo w czasie rzeczywistym)
    
#### 7.3. Kierunki dalszego rozwoju (Co można by poprawić w przyszłości)
	