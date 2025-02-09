## Общие { id=generic }
| Устройство             | Домен           | Примечания                                                                                                                                                          |
|------------------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Автоматизация          | `automation`    | Только включение/выключение. Для запуска можно создать `input_button`, передать его в УДЯ и использовать в качестве триггера в автоматизации                        |
| Бинарный датчик        | `binary_sensor` | См. [бинарные датчики](#binary-sensors)                                                                                                                             |
| Вентилятор             | `fan`           |                                                                                                                                                                     |
| Водонагреватель        | `water_heater`  || 
| Выключатель            | `switch`        ||
| Группа (старый способ) | `group`         | Только включение/выключение, для получения всех возможностей используйте [группу для конкретного типа устройств](https://www.home-assistant.io/integrations/group/) |
| Замок                  | `lock`          ||
| Камера                 | `camera`        |                                                                                                                                                                     |
| Качество воздуха       | `air_quality`   |                                                                                                                                                                     |
| Кнопка HA              | `button`        | Не путать с [физическими кнопками](./devices/button.md)                                                                                                             |
| Медиа-плеер            | `media_player`  | + телевизоры и ресиверы                                                                                                                                             |
| Освещение              | `light`         |                                                                                                                                                                     |
| Пылесос                | `vacuum`        ||
| Скрипт                 | `script`        ||
| Сцена                  | `scene`         ||
| Термостат              | `climate`       | + кондиционеры и некоторые чайники                                                                                                                                  |
| Увлажнитель            | `humidifier`    |                                                                                                                                                                     | 
| Цифровой датчик        | `sensor`        | См. [цифровые датчики](#sensors)                                                                                                                                    |
| Шторы                  | `cover`         |                                                                                                                                                                     |
|                        | `input_boolean` ||
|                        | `input_button`  |                                                                                                                                                                     |
 |                        | `input_text`    | Только для [виртуальных кнопок](./devices/button.md)                                                                                                                |

## Цифровые датчики { id=sensors }
!!! danger "Для автоматического обнаружения некоторых датчиков обязательно наличие атрибутов `device_class` и `unit_of_measurement`.<br>Если вы создаёте [датчик на шаблоне](https://www.home-assistant.io/integrations/template/#configuration-variables) - не забудьте их указать."

!!! success "Только в домене `sensor`"

| Датчик                | `device_class`   | `unit_of_measurements`                                        | Примечания                                                | 
|-----------------------|------------------|---------------------------------------------------------------|-----------------------------------------------------------|
| Температура           | `temperature`    | `°C`                                                          | Или из атрибутов `temperature`, `current_temperature`     |
| Влажность             | `humidity`       | `%`                                                           | Или из атрибутов `humidity`, `current_humidity`           |
| Давление              | `pressure`       | `pa`, `hPa`, `kPa`, `MPa`, `mmHg`, `atm`, `bar`, `mbar`       ||
| Освещенность          | `illumination`   |                                                               | Или из атрибута `illuminance`                             |
| Уровень воды          |                  |                                                               | Из атрибута `water_level` для увлажнителей и вентиляторов |
| Уровень CO2           | `carbon_dioxide` |                                                               | Или из атрибута `carbon_dioxide`                          |
| Уровень частиц PM1    |                  || Из атрибута `particulate_matter_0_1`                          |
| Уровень частиц PM2.5  |                  || Из атрибута `particulate_matter_2_5`                          |
| Уровень частиц PM10   |                  || Из атрибута `particulate_matter_10`                           |
| Уровень TVOC          |                  | `µg/m³`, `mg/m³`, `μg/ft³`, `p/m³`, `ppm`, `ppb`              | Из атрибута `total_volatile_` `organic_compounds`         |
| Напряжение            | `voltage`        || Или из атрибута `voltage`                                     |
| Потребление тока      | `current`        | `mA`, `A`                                                     | Или из атрибута `current`                                 |
| Потребляемая мощность | `power`          || Или из атрибутов `power`, `load_power`, `current_consumption` |
| Уровень заряда        | `battery`        | `%`                                                           | Или из атрибута `battery_level`                           |

## Бинарные датчики { id=binary-sensors }
!!! danger "Для автоматического обнаружения датчиков обязательно наличие атрибута `device_class`.<br>Если вы создаёте [датчик на шаблоне](https://www.home-assistant.io/integrations/template/#configuration-variables) - не забудьте его указать.<br>Если другая интеграция создала датчик без `device_class` - задайте его [вручную](./devices/sensor.md#device-class)."

!!! success "Только в домене `binary_sensor`"

| Датчик                | `device_class`                             | 
|-----------------------|--------------------------------------------|
| Открытие/закрытие     | `door`, `garage_door`, `window`, `opening` |
| Движение              | `motion`, `occupancy`, `presence`          |
| Наличие газа          | `gas`                                      |
| Наличие дыма          | `smoke`                                    |
| Низкий уровень заряда | `battery`                                  |
| Низкий уровень воды   | `water_level`                              |
| Наличие протечки      | `moisture`                                 |
| Вибрация              | `vibration`                                |

## Не поддерживаются { id=unsupported }
| Устройство    | Домен          | Примечания                                                                                                                      |
|---------------|----------------|---------------------------------------------------------------------------------------------------------------------------------|
|               | `input_select` | Нет ясности что делать при выборе разных значений<br>Обратите внимание на [пользовательские умения](./advanced/capabilities.md) |
