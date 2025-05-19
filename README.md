Snake Game na EasyMx PRO M4 (ARM)

Prosta implementacja kultowej gry „Snake” na płytce EasyMx PRO M4. Gra wykorzystuje wyświetlacz TFT ILI9341 (240×320) oraz cztero-przyciskowy joystick do sterowania wężem. Dodatkowo wprowadzono bonus, który pozwala na przenikanie przez ściany przez ograniczony czas.

Funkcje
Sterowanie joystickiem
– ruch węża: góra, dół, lewo, prawo

Detekcja kolizji
– z krawędziami planszy (ścianami)
– z własnym ciałem

Mechanika jedzenia
– pojawianie się losowo generowanego jedzenia
– wydłużanie węża po zjedzeniu

Bonus „przenikanie przez ściany”
– pojawia się okresowo jako specjalny obiekt
– po zebraniu wąż może przechodzić przez krawędzie planszy przez określony czas

System punktacji
– aktualny wynik wyświetlany na ekranie
– zapamiętanie i wyświetlenie najlepszego wyniku (High Score)

Ekran końca gry
– komunikat o porażce po kolizji
– opcja restartu

Wymagania sprzętowe i programowe
Sprzęt

Płytka: EasyMx PRO M4 (ARM)

Wyświetlacz: TFT ILI9341, rozdzielczość 240×320 pikseli

Joystick: 4 przyciski kierunkowe

Kompilator

ARM GCC lub inny zgodny kompilator dla procesorów ARM Cortex-M

Biblioteki

TivaWare (sterowanie peryferiami)

GrLib (grafika)

Sterownik wyświetlacza ILI9341
