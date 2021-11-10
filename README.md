# **Лабораторна робота №2**
## "Дослідження методів генерації сигналів у ПАС на прикладі багаторівневих лінійних кодів"
---
## **Мета роботи:** ознайомлення із формуванням аналогових сигналів у мікропроцесорній системі PSoC 5 виробництва Cypress та принципами лінійного кодування сигналів за допомогою кодів 2BQ1, 4B3T, 3B4B, AMI, HDB3
>
## **Завдання:**
1. Отримати тип лінійного коду, розрядність ЦАП та вхідну послідовність, згідно із варіантом

|№ Варіанту|Тип лінійного коду|Розрядність DAC|Задана послідовність|
|:--------:|:----------------:|:--------------|:-------------------|
|8         |3B4B              |8              |101100001010        |

2. Створити програмну реалізацію генератора заданого лінійного коду.
3. Під’єднати осцилограф до виходу генератора на замалювати форму генерованого сигналу.
>
## Порядок виконання роботи
![shema](#)
*Рис.1 Принципова схема спроектованого пристрою*
>
## Код програми

```c
#include "project.h"
#define SIZE(x) sizeof(x)/sizeof(x[0])
#define MINUS 0
#define PLUS 255
uint8 signal[]={1,0,1,1,0,0,0,0,1,0,1,0};
int main(void)
{
    CyGlobalIntEnable;
    if( SIZE(signal)%3 == 0)    // якщо сигнал не буде кратний 3, ЦАП не ініціалізується.
        VDAC8_Start();
    uint8 RDS = 0;
    for(;;)
    {
        for(uint8 i = 0; i < SIZE(signal); i+=3)
        {
            if(signal[i] == 0 && signal[i+1] == 0 && signal[i+2] == 0)  //000
            {
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
            }
            else if(signal[i] == 0 && signal[i+1] == 0 && signal[i+2] == 1)  //001
            {
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
            }
            else if(signal[i] == 0 && signal[i+1] == 1 && signal[i+2] == 0)  //010
            {
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
            }
            else if(signal[i] == 0 && signal[i+1] == 1 && signal[i+2] == 1)  //011
            {
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
            }
            else if(signal[i] == 1 && signal[i+1] == 0 && signal[i+2] == 0)  //100
            {
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
            }
            else if(signal[i] == 1 && signal[i+1] == 0 && signal[i+2] == 1)  //101
            {
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(PLUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
                VDAC8_SetValue(MINUS);
                CyDelay(1000);
            }
            else if(signal[i] == 1 && signal[i+1] == 1 && signal[i+2] == 0)  //110
            {
                if(RDS == 0)
                {
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    RDS = 1;
                }
                else if(RDS == 1)
                {
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    RDS = 0;
                }
            }
            else if(signal[i] == 1 && signal[i+1] == 1 && signal[i+2] == 1)  //111
            {
                if(RDS == 0)
                {
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    RDS = 1;
                }
                else if(RDS == 1)
                {
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    VDAC8_SetValue(PLUS);
                    CyDelay(1000);
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    VDAC8_SetValue(MINUS);
                    CyDelay(1000);
                    RDS = 0;
                }
                else
                {
                    CyDelay(3000);
                }
            }
        }   
    }
}
```

>
## Результат програми

![exper](#)
*Рис.2 Осцилограма знята експериментально*

![teor](#)
*Рис.3 Осцилограми розрахована теоретично*
>
## **Висновок:** під час виконання даної лабораторної роботи я дослідив та вивчив методи генерації сигналів у ПАС на прикладі багаторівневих лінійних кодів.
