# Toyota Celsius Fix
[English](README.md)
## Что это
CAN фильтр чтоб заменить стремные фаренгейты на нормальные градусы Цельсия в климатах американских тойот. К сожалению, они не сделали штатный способ сделать такую простую вещь, и только поэтому приходится так изголяться.

![до-после](pictures/before-after.jpg)

## Что для этого требуется
* [Платка](https://www.aliexpress.com/w/wholesale-can-filter.html) сомнительного назначения с алиэкспресса **СИНЯЯ**
* [ST-Link v2](https://www.aliexpress.com/w/wholesale-ST%2525252dLink-v2.html)
* Dupont угловые штырьки на плату

## Как работает
Плата ставится в разрыв CAN шины между климатом и бодиком и обнуляет 2й байт в сообщениях с id **0x624**

## Как прошить

![распиновка](pictures/filter.jpg)

Нужно припаять штырьковый разъём на отладочные пятаки, подключить st-link и залить [прошивку](https://github.com/kuznet1/toyota-celsius-fix/releases/download/v0.0.1/toyota-celsius-fix.bin).

Для этого достаточно соединить с программатором 
* **VCC**
* **GND**
* **SWDIO**
* **SWCLK**

Для стабильности как при прошивке, так и в жизни настоятельно рекомендую соединить перемычкой **BOOT0** и **GND**. В моей версии платы этот вход болтался в воздухе, что приводило к зависаниям.

### Linux/Windows
```
openocd -f interface/stlink.cfg -f target/stm32f1x.cfg -c "init; stm32f1x unlock 0; reset halt; program toyota-celsius-fix.bin 0x08000000 verify reset exit"
```
или через STM32CubeProgrammer
```
STM32_Programmer_CLI -c port=SWD mode=UR -rdu -w toyota-celsius-fix.bin 0x08000000 -v -rst
```

### Как подключить
Фильтр ставится в разрыв CAN шины. Он фильтрует в обе стороны, так что направление любое. Блок климата находится за облицовочной панелью справа от педали газа. Штырьки и ответная часть, которая в комплекте с платой, в принципе совместимы с тойотовым [разъемом](http://zatonevkredit.ru/repair_manuals/raw_content/ZslpaWQBDp6zoHmhsxmk), так что можно установить без пайки в машине.
![схема](pictures/pinout.png)

| Вывод  | Цвет        | Описание      |
|--------|-------------|---------------|
| G81-1  | Серый       | Питание (ACC) |
| G81-14 | Черно-белый | Масса (GND)   |
| G81-11 | Черный      | Шина CAN H    |
| G81-12 | Белый       | Шина CAN L    |

![на месте](pictures/in-place.jpg)

### Благодарности
Спасибо [@andrewkabai](https://github.com/andrewkabai) за подробное дотошное [исследование](https://dangerouspayload.com/2020/03/10/hacking-a-mileage-manipulator-can-bus-filter-device/) этих платок.