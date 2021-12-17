# Расширенная настройка

- [Фильтрация устройств (filter)](#фильтрация-устройств-filter)
- [Параметры устройств (entity_config)](#параметры-устройств-entity_config)
  - [Режим включения кондиционеров и термостатов](#режим-включения-кондиционеров-и-термостатов)
  - [Ограничение уровня громкости](#ограничение-уровня-громкости)
- [Прочие настройки](#прочие-настройки)
- [Несколько компонентов с разными фильтрами](#несколько-компонентов-с-разными-фильтрами)

## Фильтрация устройств (filter)
Есть два способа настроить список устройств, которые будут отдаваться в УДЯ (фильтры):
1. Через GUI: Настройки > Интеграции > Yandex Smart Home > Настроить
2. Через YAML в ключе `yandex_smart_home.filter`. Фильтры в YAML приоритнее тех, что в GUI.

Поддерживаемые фильтры:
  * `include_domains`
  * `include_entities`
  * `include_entity_globs`
  * `exclude_domains`
  * `exclude_entities`
  * `exclude_entity_globs`

Приоритизация по фильтрам работает аналогично фильтрам в интеграции [Recorder](https://www.home-assistant.io/integrations/recorder/#configure-filter).

Пример конфигурации:
```yaml
yandex_smart_home:
  filter:
    include_domains:
      - switch
      - light
    include_entities:
      - media_player.tv
      - media_player.tv_lg
      - media_player.receiver
    include_entity_globs:
      - sensor.temperature_*
    exclude_entities:
      - light.highlight
    exclude_entity_globs:
      - sensor.weather_*
```

## Параметры устройств (entity_config)
Для каждого устройства поддерживается изменение параметров по-умолчанию через словарь `yandex_smart_home.entity_config`. В большинстве случаев никаких дополнительных настроек не требуется.

Наличие устройства в `entity_config` не означает, что оно будет отдано в УДЯ. Не забудьте разрешить устройство в фильтрах.

| Параметр      | По умолчанию    | Варианты                          | Описание                                                 |
| ------------- | --------------- | --------------------------------- | -------------------------------------------------------- |
| `name`        |                 | Русские буквы и пробелы           | Название устройства в УДЯ
| `room`        | Пространство    | Русские буквы и пробелы           | Комната, в которой находится устройство, [подробнее о комнатах](../README.md#комнаты)
| `type`        | Автоматически   | [Список](https://yandex.ru/dev/dialogs/smart-home/doc/concepts/device-types.html#device-types__types) | Переопредление стандартного типа устройства. Например, домен `switch` по умолчанию отдается как "выключатель" (`devices.types.switch`) и реагирует на команду "Алиса, включи ХХХ". А если задать `devices.types.openable`, то у такого устройства изменится иконка и фраза на "Алиса, **открой** XXX". После изменения типа **обязательно удалите** устройство вручную через квазар и выполните обновление списка устройств.
| `turn_on`     | Автоматически   |                                   | Вызываемый сервис при включении устройства               |
| `turn_off`    | Автоматически   |                                   | Вызываемый сервис при выключении устройства              |
| `features`    |                 | `volume_mute`, `volume_set`, `next_previous_track` | Только для домена `media_player`. Явное указание поддерживаемых устройством функций. Использутся для устройств, которые меняют набор функций в зависимости от своего состояния (например Chrome Cast).
| `support_set_channel` |         | `false`             | Отключает функцию выбора канала для `media_player` через цифровую панель и сервис `media_player.play_media`. Может потребоваться для устройств, которые не поддерживают выбор канала, но поддерживают сервис `play_media`. |

Пример конфигурации:
```yaml
yandex_smart_home:
  entity_config:
    fan.xiaomi_miio_device:
      name: Увлажнитель
      room: Гостинная
      type: devices.types.humidifier  # параметр необязательный, вычисляется автоматически!
    switch.gate:
      name: Ворота
      room: Улица
      type: devices.types.openable
    climate.smart_ir:
      name: Кондиционер
      room: Кухня
      turn_on:  # переопределение сервиса для включения устройства
        service: climate.turn_on
        entity_id: climate.smart_ir
```

### Режим включения кондиционеров и термостатов
Для устройств в домене `climate` реализован особый алгоритм включения: если поддерживается режим `heat_cool` или `auto` -
включается этот режим, а если не поддерживается - то включение происходит через сервис `climate.turn_on` (поведение сервиса зависит от интеграции, через которую подключено устройство).

В некоторых случаях включение в режиме `auto` нежелательно. Чтобы этого избежать есть два **разных** пути:
1. Отключить `auto` путём изменения списка поддерживаемых режимов (`hvac_modes`) через [кастомизацию](https://www.home-assistant.io/docs/configuration/customizing-devices/). В этом случае режим `auto` так же пропадёт из интерфейса Home Assistant. Пример:
    ```yaml
    climate.smartir_ac:
      hvac_modes:
         - cool
         - heat
         - dry
         - 'off'
    ```

2. Переопределить сервис включения в `entity_config`:
    ```yaml
    yandex_smart_home:
      entity_config:
        climate.smartir_ac:
          turn_on:
            service: climate.turn_on
            entity_id: climate.smartir_ac
    ```

### Ограничение уровня громкости
```yaml
yandex_smart_home:
  entity_config:
    media_player.receiver:
      range:
        max: 95
        min: 20
        precision: 2
```

## Прочие настройки
Задаются через словарь `yandex_smart_home.settings`:

| Параметр         | По умолчанию    | Возможные варианты                | Описание                                                 |
| ---------------- | --------------- | --------------------------------- | -------------------------------------------------------- |
| `pressure_unit`  | `mmHg`          | `pa`, `mmHg`, `atm`, `bar`        | Единица измерения давления, в которой будут переданы данные в УДЯ. Конвертация из значения в Home Assistant автоматическая на основании атрибута устройства `unit_of_measurement`. |

Пример:
```yaml
yandex_smart_home:
  settings:
    pressure_unit: mmHg
```

## Несколько компонентов с разными фильтрами
Несмотря на то, что компонент можно подключить к разным навыкам (или аккаунтам) в УДЯ, фильтры для разных навыков остаются едиными. Это можно обойти созданием дубликата компонента:
* В `custom_components` скопируйте каталог `yandex_smart_home` в `yandex_smart_home2` (это пример, название может быть любым)
* В `yandex_smart_home2/manifest.json` измените параметр `domain` на `yandex_smart_home2`
* В `yandex_smart_home2/const.py` измените переменную `DOMAIN` на `yandex_smart_home2`

Настройка дублированного компонента выполняется через словарь `yandex_smart_home2`, пример:
```yaml
yandex_smart_home2:
  filters:
    include_domains:
      - switch
```

Дублированный компонент будет доступен по ссылке `https://YOUR_HA_DOMAIN:PORT/api/yandex_smart_home2`

Облачное подключение для дублированного компонента так же поддерживается.
