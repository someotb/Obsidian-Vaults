
# Оператор goto

Аргументы против goto:
1) Плохой тон
2) Нечитабельность кода
3) Заменим циклами и ветвлением
4) Мешает оптимизации компилятором
5) [Эдсгер Дейкстра](https://clck.ru/3QWfHS) против!

Пример с использованием goto:
```c
#include<stdio.h>

int main()
{
    int x;
    
A:  scanf("%d", &x);
    if (x == 0) goto D;
B:  if (x % 2 == 0) goto C;
    printf("Number %d is even\n", x);
    goto A;
C:  printf("Number %d is odd\n", x);
    goto A;
D:
    return 0;
}
```

Ну это же пиз..., нечитаемый код, трудный для понимания, а реализует проверку на четность числа, близкий к ассемлерному коду

# Оператор switch

Аргументы против switch:
1) Работает только с целыми числами
2) switch внутри организован через goto, а goto это meh...
3) Использование break для поведения if else

Пример с использовнием switch:
```c
#include<stdio.h>

int main()
{
    int x;
    scanf("%d", &x);

    switch (x)
    {
    case 1:
        printf("One!\n");
        break;
    case 2:
        printf("Two!\n");
        break;
    case 3:
        printf("Three!\n");
        break;
    case 0:
        printf("Zero!\n");
        break;            
    default:
        printf("Don't know this number!\n");
        break;
    }
}
```