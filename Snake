#include <stdlib.h>
#include <stdint.h>
#include <stdbool.h>
#include "inc/hw_memmap.h"
#include "driverlib/fpu.h"
#include "driverlib/sysctl.h"
#include "driverlib/rom.h"
#include "driverlib/pin_map.h"
#include "driverlib/uart.h"
#include "grlib/grlib.h"
#include "drivers/ili9341_240x320x262K.h"
#include "utils/uartstdio.h"
#include "driverlib/gpio.h"

#define WIDTH 320
#define HEIGHT 240
#define TILE_SIZE 10
#define MAX_SNAKE_LENGTH 100
#define SPECIAL_DURATION 30

//Strukutra Snake
typedef struct {
    int x[MAX_SNAKE_LENGTH];
    int y[MAX_SNAKE_LENGTH];
    int length;
} Snake;

// Struktura jedzenia
typedef struct {
    int x, y;
} Food;

// Struktura specjalnego obiektu
typedef struct {
    int x, y;
    bool active;
    int timer;
    bool used;
} SpecialObject;

//Kierunki
typedef enum {
    UP,
    DOWN,
    LEFT,
    RIGHT
} Direction;

tContext sContext;
Snake snake;
Food food;
Direction direction = RIGHT;
bool gameOver = false;
int score = 0;
int highScore = 0;
bool canPassThroughWalls = false;
SpecialObject special;

// Funkcja do konwersji liczby całkowitej na string
void intToStr(int num, char* str) {
    int i = 0;
    bool isNegative = false;

    // Obsługa liczby 0
    if (num == 0) {
        str[i++] = '0';
        str[i] = '\0';
        return;
    }

    // Obsługa liczb ujemnych
    if (num < 0) {
        isNegative = true;
        num = -num;
    }

    // Konwersja liczby na string
    while (num != 0) {
        str[i++] = (num % 10) + '0';
        num = num / 10;
    }

    // Dodanie znaku minus, jeśli liczba była ujemna
    if (isNegative) {
        str[i++] = '-';
    }

    str[i] = '\0'; // Kończymy string

    // Odwrócenie stringa (od końca do początku)
    int start = 0;
    int end = i - 1;
    while (start < end) {
        char temp = str[start];
        str[start] = str[end];
        str[end] = temp;
        start++;
        end--;
    }
}

// 🟩 **Rysowanie pojedynczego segmentu**
void drawRect(int x, int y, uint32_t color) {
    tRectangle rect = {x, y, x + TILE_SIZE - 1, y + TILE_SIZE - 1};
    GrContextForegroundSet(&sContext, color);
    GrRectFill(&sContext, &rect);
}

void initSnake(Snake *snake) {
    // Ustawienie początkowej długości węża
    snake->length = 5;

    // Początkowe pozycje segmentów węża
    int i;
    for (i = 0; i < snake->length; i++) {
        snake->x[i] = (5 - i) * TILE_SIZE; // Wąż zaczyna od lewej strony, przesuwając się w prawo
        snake->y[i] = TILE_SIZE; // Początkowy rząd
    }
}

// Inicjalizacja jedzenia
void initFood(Food *food) {
    food->x = (rand() % (WIDTH / TILE_SIZE)) * TILE_SIZE;
    food->y = (rand() % (HEIGHT / TILE_SIZE)) * TILE_SIZE;
}

void initSpecial(SpecialObject *special) {
    special->x = (rand() % (WIDTH / TILE_SIZE)) * TILE_SIZE;
    special->y = (rand() % (HEIGHT / TILE_SIZE)) * TILE_SIZE;
    special->active = true;
    special->timer = SPECIAL_DURATION;
}

// Rysowanie całej gry
void renderGame() {
    // Czyszczenie ekranu
    tRectangle background = {0, 0, WIDTH, HEIGHT};
    GrContextForegroundSet(&sContext, ClrBlack);
    GrRectFill(&sContext, &background);

    // Rysowanie węża
    int i;
    for (i = 0; i < snake.length; i++) {
        drawRect(snake.x[i], snake.y[i], ClrGreen);
    }

    // Rysowanie jedzenia
    drawRect(food.x, food.y, ClrRed);

    // Rysowanie obiektu specjalnego (tylko jeśli jest aktywny)
    if (special.active) {
        drawRect(special.x, special.y, ClrYellow);
    }


    // Rysowanie wyniku
    char scoreStr[10];
    intToStr(score, scoreStr);
    GrContextFontSet(&sContext, g_psFontCm12);
    GrContextForegroundSet(&sContext, ClrWhite);
    GrStringDraw(&sContext, scoreStr, -1, 10, 10, 0);

    // Rysowanie licznika bonusu - powinien pojawiać się **po zebraniu** i odliczać
    if (special.used && special.timer > 0) {
        char timerStr[10];
        intToStr(special.timer, timerStr);

        // Nadpisanie poprzedniego tekstu czarnym tłem (zapobiega miganiu)
        tRectangle rect = {WIDTH - 60, 5, WIDTH - 5, 20};
        GrContextForegroundSet(&sContext, ClrBlack);
        GrRectFill(&sContext, &rect);

        // Rysowanie nowego licznika
        GrContextForegroundSet(&sContext, ClrWhite);
        GrStringDraw(&sContext, timerStr, -1, WIDTH - 50, 10, 0);
    }
}




// Inicjalizacja gry
void initGame() {
    direction = RIGHT;
    gameOver = false;
    initSnake(&snake);
    initFood(&food);
    canPassThroughWalls = false;
    initSpecial(&special);
    renderGame();
    score = 0;
}

// 🎮 **Obsługa joysticka**
void handleJoystick() {
    if (GPIOPinRead(GPIO_PORTB_BASE, GPIO_PIN_0) == 0 && direction != DOWN) { // Góra
        direction = UP;
    }
    if (GPIOPinRead(GPIO_PORTE_BASE, GPIO_PIN_5) == 0 && direction != UP) { // Dół
        direction = DOWN;
    }
    if (GPIOPinRead(GPIO_PORTK_BASE, GPIO_PIN_7) == 0 && direction != RIGHT) { // Lewo
        direction = LEFT;
    }
    if (GPIOPinRead(GPIO_PORTE_BASE, GPIO_PIN_4) == 0 && direction != LEFT) { // Prawo
        direction = RIGHT;
    }
}


// 🪦 **Ekran końca gry**
void showGameOver() {
    if (score > highScore){
        highScore = score;
    }
    GrContextFontSet(&sContext, g_psFontCm12);
    GrContextForegroundSet(&sContext, ClrWhite);
    GrStringDrawCentered(&sContext, "Game Over!", -1, WIDTH / 2, HEIGHT / 2, 0);
    // Konwertowanie wyniku na tekst
       char scoreStr[10];  // Tablica na wynik
       intToStr(score, scoreStr);  // Konwertowanie score na string

       // Rysowanie aktualnego wyniku poniżej "Game Over!"
       GrStringDrawCentered(&sContext, scoreStr, -1, WIDTH / 2, HEIGHT / 2 + 20, 0);

       // Rysowanie aktualnego wyniku poniżej "Game Over!"
       GrStringDrawCentered(&sContext, "Score:", -1, WIDTH / 2, HEIGHT / 2 + 20, 0);
       GrStringDrawCentered(&sContext, scoreStr, -1, WIDTH / 2, HEIGHT / 2 + 40, 0);

           // Konwertowanie highScore na tekst
       char highScoreStr[10];  // Tablica na highScore
       intToStr(highScore, highScoreStr);  // Konwertowanie highScore na string

           // Rysowanie najwyższego wyniku poniżej aktualnego wyniku
       GrStringDrawCentered(&sContext, "High Score:", -1, WIDTH / 2, HEIGHT / 2 + 60, 0);
       GrStringDrawCentered(&sContext, highScoreStr, -1, WIDTH / 2, HEIGHT / 2 + 80, 0);
    SysCtlDelay(SysCtlClockGet() * 1);
    initGame();
}

// 🔄 **Aktualizacja stanu gry**
void updateGame() {
    // Przesunięcie segmentów węża
    int i;
    for (i = snake.length - 1; i > 0; i--) {
        snake.x[i] = snake.x[i - 1]; // Przesuwamy pozycje segmentów węża
        snake.y[i] = snake.y[i - 1]; // To jest ważne, aby zachować pozycje ciałka węża
    }

    // Ruch głowy węża
    if (direction == UP) {
        snake.y[0] -= TILE_SIZE;
    }
    if (direction == DOWN) {
        snake.y[0] += TILE_SIZE;
    }
    if (direction == LEFT) {
        snake.x[0] -= TILE_SIZE;
    }
    if (direction == RIGHT) {
        snake.x[0] += TILE_SIZE;
    }

    // Sprawdzenie kolizji z jedzeniem
    if (snake.x[0] == food.x && snake.y[0] == food.y) {
        if (snake.length < MAX_SNAKE_LENGTH) {
            snake.length++;
            score++;
        }
        // Losowanie nowej pozycji dla jedzenia
        food.x = (rand() % (WIDTH / TILE_SIZE)) * TILE_SIZE;
        food.y = (rand() % (HEIGHT / TILE_SIZE)) * TILE_SIZE;
    }

    // Sprawdzenie, czy wąż zebrał bonus
    // Sprawdzenie, czy wąż zebrał bonus
    if (snake.x[0] == special.x && snake.y[0] == special.y) {
        canPassThroughWalls = true;    // Włączamy efekt bonusu
        special.active = false;        // Dezaktywujemy bonus (nie rysujemy go)
        special.used = true;           // Aktywujemy licznik czasu bonusu
        special.timer = SPECIAL_DURATION;

        // Przenosimy bonus poza mapę, żeby nie można go było ponownie zebrać
        special.x = -10;
        special.y = -10;
    }

    // Jeśli bonus jest aktywny, licznik powinien się odliczać
    if (special.used) {
        if (special.timer > 0) {
            special.timer--;  // Odliczanie czasu bonusu
        } else {
            // Gdy czas się skończy, efekt znika
            canPassThroughWalls = false;
            special.used = false;  // Wyłączamy licznik
        }
    }

    // Jeśli bonus nie jest aktywny i nie jest już używany, losujemy nową pozycję
    if (!special.active && !special.used) {
        special.active = true;
        special.x = (rand() % (WIDTH / TILE_SIZE)) * TILE_SIZE;
        special.y = (rand() % (HEIGHT / TILE_SIZE)) * TILE_SIZE;
    }





    // Jesli nie ma bonusu sprawdzaj kolizje
    if (!canPassThroughWalls){
        // Sprawdzenie kolizji ze ścianami
        if (snake.x[0] < 0 || snake.x[0] >= WIDTH || snake.y[0] < 0 || snake.y[0] >= HEIGHT) {
            gameOver = true; // Zakończenie gry, jeśli wąż wyjdzie poza ekran
        }
    }
    else {
        if (snake.x[0] < 0) snake.x[0] = WIDTH - TILE_SIZE;
        if (snake.x[0] >= WIDTH) snake.x[0] = 0;
        if (snake.y[0] < 0) snake.y[0] = HEIGHT - TILE_SIZE;
        if (snake.y[0] >= HEIGHT) snake.y[0] = 0;
    }

    // Sprawdzenie kolizji z własnym ciałem
    for (i = 1; i < snake.length; i++) {
        if (snake.x[0] == snake.x[i] && snake.y[0] == snake.y[i]) {
            gameOver = true; // Gra kończy się, gdy wąż zderzy się z własnym ciałem
        }
    }
    if (gameOver) {
          showGameOver();
    }
}


int main(void)
{
       SysCtlClockSet(SYSCTL_SYSDIV_2_5 | SYSCTL_USE_PLL | SYSCTL_XTAL_16MHZ | SYSCTL_OSC_MAIN);

       SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOB);
       SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOE);
       SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOH);
       SysCtlPeripheralEnable(SYSCTL_PERIPH_GPIOK);
       ILI9341_240x320x262K_Init();

       GPIOPinTypeGPIOInput(GPIO_PORTB_BASE, GPIO_PIN_0 | GPIO_PIN_7);
       GPIOPinTypeGPIOInput(GPIO_PORTE_BASE, GPIO_PIN_4 | GPIO_PIN_5);
       GPIOPinTypeGPIOInput(GPIO_PORTK_BASE, GPIO_PIN_7);

       GrContextInit(&sContext, &g_sILI9341_240x320x262K);

       srand(SysCtlClockGet());

       // Inicjalizacja gry
       initGame();
       // Główna pętla gry
           while (1)
           {
               // Sprawdzenie, czy gra została zakończona
               if (gameOver) {
                   showGameOver();
               }

               // Obsługa joysticka (zmiana kierunku)
               handleJoystick();

               // Aktualizacja stanu gry (ruch węża, kolizje, itp.)
               updateGame();

               // Rysowanie stanu gry
               renderGame();

               // Małe opóźnienie, aby nie przepełnić procesora
               SysCtlDelay(SysCtlClockGet() / 70);  // predkosc gry
           }
}
