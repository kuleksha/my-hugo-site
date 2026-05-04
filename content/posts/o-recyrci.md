+++
date = '2026-04-27T1:24:33+03:00'
draft = false
title = 'Ресурсы'
+++

## Практические рекомендации по выбору пластика

* **Для точности** — PLA.
* **Для термостойкости** — ABS или Nylon.
* **Для прочности и универсальности** — PETG.
* **Для гибкости** — TPU.
* **Совместимость:** Не смешивать материалы без необходимости.
* **Конструкция:** Для многоматериальных изделий использовать механические соединения, а не адгезию.
* **Хранение:** Хранить материалы в сухих боксах (особенно PETG и Nylon).
* **Поверхность:** Использовать правильные поверхности — PEI, стекло или текстурированные пластины.

## Полный обзор популярных материалов
Основные материалы, которые используются в FDM-печати:
• PLA — полилактид, полярный биополимер.
• ABS — сополимер акрилонитрила, бутадиена и стирола.
• PETG — гликол-модифицированный PET, аморфный и гибкий.
• TPU — термопластичный полиуретан, эластомер.
• Nylon (PA6, PA12) — полиамиды, полукристаллические термопласты.
Дополнительно существуют: PC, ASA, HIPS, PP, POM, композиты (GF, CF, Kevlar).


## Реализация смены цветов с помощью слайсера

```gcode
[gcode_macro CHANGE_TOOL]
gcode:
    {% set tool = params.TOOL|int %}
    RESPOND MSG="Switching to tool T{tool}"
    {% if tool == 0 %}
        RIGHT_SIDE MOVE1=0
    {% elif tool == 1 %}
        RIGHT_SIDE MOVE1=70
    {% elif tool == 2 %}
        LEFT_SIDE MOVE1=0
    {% elif tool == 3 %}
        LEFT_SIDE MOVE1=70
    {% else %}
        RESPOND MSG="Unknown tool!"
    {% endif %}

[gcode_macro RIGHT_SIDE]
description: "Фидинг справа"
gcode:
  {% set m1 = params.MOVE1|default(0)|float %}
  {% set m2 = params.MOVE2|default(100)|float %}
  {% set speed = params.SPEED|default(50)|float %}
  {% set accel = params.ACCEL|default(500)|float %}
  {% set angle = params.ANGLE|default(90)|int %}
  {% set return_angle = params.RETURN_ANGLE|default(0)|int %}
  RESPOND MSG="RIGHT_SIDE position {m1}"
  # позиция каретки
  MANUAL_STEPPER STEPPER=fil_step1 MOVE={m1} SPEED={speed} ACCEL={accel}
  G4 P2000
  # поворот сервопривода
  SET_SERVO SERVO=fil_servo1 ANGLE={angle}
  G4 P2000
  # подача филамента
  MANUAL_STEPPER STEPPER=fil_step2 MOVE={m2} SPEED={speed} ACCEL={accel}
  G4 P2000
  # возврат фидера
  MANUAL_STEPPER STEPPER=fil_step2 MOVE=0 SPEED={speed} ACCEL={accel}
  G4 P2000
  # возврат серво
  SET_SERVO SERVO=fil_servo1 ANGLE={return_angle}
  G4 P2000
  
[gcode_macro LEFT_SIDE]
description: "Фидинг слева"
gcode:
  {% set m1 = params.MOVE1|default(0)|float %}
  {% set m2 = params.MOVE2|default(100)|float %}
  {% set speed = params.SPEED|default(50)|float %}
  {% set accel = params.ACCEL|default(500)|float %}
  {% set angle = params.ANGLE|default(90)|int %}
  {% set return_angle = params.RETURN_ANGLE|default(0)|int %}
  RESPOND MSG="LEFT_SIDE position {m1}"
  # позиция каретки
  MANUAL_STEPPER STEPPER=fil_step3 MOVE={m1} SPEED={speed} ACCEL={accel}
  G4 P2000
  # поворот серво
  SET_SERVO SERVO=fil_servo2 ANGLE={angle}
  G4 P2000
  # подача филамента
  MANUAL_STEPPER STEPPER=fil_step4 MOVE={m2} SPEED={speed} ACCEL={accel}
  G4 P2000
  # возврат подачи
  MANUAL_STEPPER STEPPER=fil_step4 MOVE={-m2} SPEED={speed} ACCEL={accel}
  G4 P2000
  # возврат серво
  SET_SERVO SERVO=fil_servo2 ANGLE={return_angle}
  G4 P2000
```
