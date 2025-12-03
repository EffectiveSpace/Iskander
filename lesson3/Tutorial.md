# Урок 3: Элементы алгоритмизации в Arduino

## Введение

Алгоритмизация — это процесс разработки алгоритмов, то есть последовательностей действий для решения задач. В программировании Arduino используются основные структуры алгоритмов: линейные, разветвляющиеся и циклические. В этом уроке мы рассмотрим условные операторы, циклы и оператор выбора.

## Условные операторы (if, else, else if)

Условные операторы позволяют выполнять различные части кода в зависимости от определенных условий.

### Оператор if
Оператор `if` проверяет условие и выполняет блок кода, если условие истинно.

```cpp
if (условие) {
  // код выполняется, если условие истинно
}
```

Пример:
```cpp
int sensorValue = analogRead(A0);

if (sensorValue > 512) {
  digitalWrite(13, HIGH);
}
```

### Операторы if-else
Оператор `else` позволяет выполнить альтернативный блок кода, если условие ложно.

```cpp
if (условие) {
  // код для истинного условия
} else {
  // код для ложного условия
}
```

Пример:
```cpp
int buttonState = digitalRead(2);

if (buttonState == HIGH) {
  digitalWrite(13, HIGH);
} else {
  digitalWrite(13, LOW);
}
```

### Конструкция if-else if
Для проверки нескольких условий используется конструкция `if-else if`.

```cpp
if (условие1) {
  // код для условия1
} else if (условие2) {
  // код для условия2
} else {
  // код для всех остальных случаев
}
```

Пример:
```cpp
int sensorValue = analogRead(A0);

if (sensorValue < 300) {
  digitalWrite(8, HIGH);
  digitalWrite(9, LOW);
  digitalWrite(10, LOW);
} else if (sensorValue < 600) {
  digitalWrite(8, LOW);
  digitalWrite(9, HIGH);
  digitalWrite(10, LOW);
} else {
  digitalWrite(8, LOW);
  digitalWrite(9, LOW);
  digitalWrite(10, HIGH);
}
```

## Циклы

Циклы позволяют многократно выполнять один и тот же блок кода.

### Цикл for
Цикл `for` используется, когда заранее известно количество итераций.

```cpp
for (инициализация; условие; изменение) {
  // тело цикла
}
```

Пример:
```cpp
// Мигание светодиодом 5 раз
for (int i = 0; i < 5; i++) {
  digitalWrite(13, HIGH);
  delay(500);
  digitalWrite(13, LOW);
  delay(500);
}
```

### Цикл while
Цикл `while` выполняется до тех пор, пока условие истинно.

```cpp
while (условие) {
  // тело цикла
}
```

Пример:
```cpp
int counter = 0;

while (counter < 10) {
  digitalWrite(13, HIGH);
  delay(200);
  digitalWrite(13, LOW);
  delay(200);
  counter++;
}
```

### Цикл do-while
Цикл `do-while` гарантирует, что тело цикла выполнится хотя бы один раз.

```cpp
do {
  // тело цикла
} while (условие);
```

Пример:
```cpp
int sensorValue;

do {
  sensorValue = analogRead(A0);
  delay(100);
} while (sensorValue < 100); // Продолжать, пока значение не станет >= 100
```

## Оператор выбора switch-case

Оператор `switch` используется для выбора одного из нескольких возможных вариантов выполнения кода.

```cpp
switch (переменная) {
  case значение1:
    // код для значения1
    break;
  case значение2:
    // код для значения2
    break;
  default:
    // код по умолчанию
    break;
}
```

Важно: используйте `break` для выхода из ветви `case`, иначе выполнение продолжится в следующей ветви.

Пример:
```cpp
int button = digitalRead(2);

switch (button) {
  case HIGH:
    digitalWrite(13, HIGH);
    break;
  case LOW:
    digitalWrite(13, LOW);
    break;
  default:
    // код по умолчанию (никогда не выполнится в этом примере)
    break;
}
```

## Примеры использования в Arduino проектах

### Контроль температуры
```cpp
int temp = analogRead(A0); // Условное считывание температуры

if (temp > 700) {  // Если температура выше порога
  digitalWrite(8, HIGH);   // Включить вентилятор
  digitalWrite(9, LOW);    // Выключить нагреватель
} else if (temp < 300) {  // Если температура ниже порога
  digitalWrite(8, LOW);    // Выключить вентилятор
  digitalWrite(9, HIGH);   // Включить нагреватель
} else {
  digitalWrite(8, LOW);    // Вентилятор выключен
  digitalWrite(9, LOW);    // Нагреватель выключен
}
```

### Счетчик нажатий кнопки
```cpp
int buttonState = 0;
int lastButtonState = 0;
int pushCounter = 0;

void loop() {
  buttonState = digitalRead(2);

  if (buttonState != lastButtonState) {  // Нажатие кнопки
    if (buttonState == HIGH) {           // Если кнопка нажата
      pushCounter++;                     // Увеличить счетчик
      Serial.print("Кнопка нажата раз: ");
      Serial.println(pushCounter);
    }
    delay(50);  // Защита от дребезга
  }
  lastButtonState = buttonState;
}
```

### Автоматическая система освещения
```cpp
int lightLevel = analogRead(A0);  // Уровень освещенности
int lampState = 0;

if (lightLevel < 200) {           // Темно
  lampState = 1;                  // Включить лампу
} else if (lightLevel > 800) {    // Светло
  lampState = 0;                  // Выключить лампу
} else {
  // Промежуточное освещение - оставить как есть
}

digitalWrite(13, lampState);
```

## Логические операторы

Для создания сложных условий используются логические операторы:

- `&&` (И) - оба условия должны быть истинны
- `||` (ИЛИ) - хотя бы одно условие должно быть истинно
- `!` (НЕ) - инвертирует значение условия

Пример:
```cpp
int temp = analogRead(A0);
int humidity = analogRead(A1);

if (temp > 600 && humidity < 300) {   // Температура высокая И влажность низкая
  digitalWrite(8, HIGH);              // Включить систему охлаждения
} else if (temp < 200 || humidity > 700) {  // Температура низкая ИЛИ влажность высокая
  digitalWrite(9, HIGH);              // Включить систему подогрева
}

if (!(digitalRead(2) == HIGH)) {      // Если кнопка НЕ нажата
  digitalWrite(10, LOW);              // Выключить индикатор
}
```

Эти элементы алгоритмизации позволяют создавать сложные и функциональные программы для Arduino, которые могут принимать решения и выполнять различные действия в зависимости от условий и входных данных.