# Урок 4: Работа с датчиками и исполнительными механизмами

## Введение

В этом уроке мы познакомимся с подключением и использованием различных датчиков, научимся считывать и обрабатывать их данные, а также работать с исполнительными механизмами, такими как сервоприводы и двигатели.

## 1. Подключение и использование датчиков

### Датчик температуры (LM35, TMP36)

Датчики температуры позволяют измерять температуру окружающей среды. Они подключаются к аналоговому входу Arduino.

**Подключение:**
- VCC → 5V
- GND → GND
- OUT → A0 (например)

**Пример кода:**
```cpp
int tempPin = A0;
int tempReading = analogRead(tempPin);

// Преобразование в температуру в градусах Цельсия
float voltage = tempReading * (5.0 / 1023.0);
float temperatureC = voltage * 100.0; // Для LM35

Serial.print("Температура: ");
Serial.print(temperatureC);
Serial.println(" °C");
```

### Датчик освещенности (фоторезистор/LDR)

Фоторезистор изменяет сопротивление в зависимости от уровня освещенности.

**Подключение:**
- Один контакт → 5V через резистор (10 кОм)
- Второй контакт → A0 и GND через резистор (делитель напряжения)

**Пример кода:**
```cpp
int lightPin = A0;
int lightLevel = analogRead(lightPin);

Serial.print("Уровень освещенности: ");
Serial.println(lightLevel);
```

### Датчик влажности (HC-SR04/датчик влажности почвы)

Датчики влажности позволяют измерять уровень влажности в почве или воздухе.

**Подключение:**
- VCC → 5V
- GND → GND
- SIG → A0

**Пример кода:**
```cpp
int moisturePin = A0;
int moistureLevel = analogRead(moisturePin);

Serial.print("Влажность: ");
Serial.println(moistureLevel);
```

## 2. Считывание данных с аналоговых и цифровых датчиков

### Аналоговые датчики

Аналоговые датчики возвращают значение в диапазоне от 0 до 1023, соответствующее напряжению от 0 до 5 В.

```cpp
int sensorValue = analogRead(A0); // Считывание с аналогового пина
```

### Цифровые датчики

Цифровые датчики возвращают только два значения: HIGH (1) или LOW (0).

**Пример с кнопкой/цифровым датчиком:**
```cpp
int digitalPin = 2;
pinMode(digitalPin, INPUT);
int digitalValue = digitalRead(digitalPin);

if (digitalValue == HIGH) {
  // Что-то делаем
}
```

### Комбинированные датчики (цифровой и аналоговый выход)

Некоторые датчики имеют как цифровой, так и аналоговый выход:

```cpp
int analogValue = analogRead(A0);  // Аналоговое значение
int digitalValue = digitalRead(2); // Цифровое значение
```

## 3. Обработка и фильтрация полученных данных

### Усреднение значений (скользящее среднее)

Для уменьшения шума в показаниях датчиков применяют фильтрацию:

```cpp
const int numReadings = 10;
int readings[numReadings];      // Массив для хранения значений
int readIndex = 0;              // Индекс текущего значения
int total = 0;                  // Сумма всех значений
int average = 0;                // Среднее значение

void setup() {
  Serial.begin(9600);
  // Инициализация массива
  for (int i = 0; i < numReadings; i++) {
    readings[i] = 0;
  }
}

void loop() {
  total = total - readings[readIndex];          // Удалить старое значение
  readings[readIndex] = analogRead(A0);         // Считать новое
  total = total + readings[readIndex];          // Добавить новое
  readIndex = (readIndex + 1) % numReadings;    // Перейти к следующему индексу

  average = total / numReadings;                // Вычислить среднее

  Serial.println(average);
  delay(1);
}
```

### Фильтр с порогом (для защиты от дребезга контактов)

Полезно при работе с цифровыми датчиками или кнопками:

```cpp
int lastButtonState = LOW;
int currentButtonState = LOW;
unsigned long lastDebounceTime = 0;
const unsigned long debounceDelay = 50;

void loop() {
  int reading = digitalRead(2);
  
  if (reading != lastButtonState) {
    lastDebounceTime = millis();
  }
  
  if ((millis() - lastDebounceTime) > debounceDelay) {
    if (reading != currentButtonState) {
      currentButtonState = reading;
      // Обработка изменения состояния
    }
  }
  
  lastButtonState = reading;
}
```

### Сравнение с пороговыми значениями

Используется для принятия решений на основе данных с датчиков:

```cpp
int sensorValue = analogRead(A0);
int threshold = 512;

if (sensorValue > threshold) {
  // Уровень превышает порог
  digitalWrite(13, HIGH);
} else {
  // Уровень ниже порога
  digitalWrite(13, LOW);
}
```

## 4. Работа с сервоприводами и двигателями

### Подключение сервопривода

Сервоприводы позволяют точно управлять углом поворота вала (обычно от 0° до 180°).

**Подключение:**
- Красный (или коричневый) → 5V
- Черный (или черный) → GND
- Желтый (или оранжевый) → Цифровой пин (например, 9)

**Пример кода:**
```cpp
#include <Servo.h>

Servo myServo;
int servoPin = 9;

void setup() {
  myServo.attach(servoPin);  // Подключить серво к пину
}

void loop() {
  myServo.write(0);    // Повернуть на 0 градусов
  delay(1000);
  myServo.write(90);   // Повернуть на 90 градусов
  delay(1000);
  myServo.write(180);  // Повернуть на 180 градусов
  delay(1000);
}
```

### Подключение и управление двигателями

Для управления двигателями постоянного тока часто используются драйверы моторов (L298N, L293D и т.д.) или транзисторы.

**Пример с драйвером L298N:**
```cpp
int motorPin1 = 7;
int motorPin2 = 8;

void setup() {
  pinMode(motorPin1, OUTPUT);
  pinMode(motorPin2, OUTPUT);
}

void loop() {
  // Вращение вперед
  digitalWrite(motorPin1, HIGH);
  digitalWrite(motorPin2, LOW);
  delay(2000);
  
  // Остановка
  digitalWrite(motorPin1, LOW);
  digitalWrite(motorPin2, LOW);
  delay(1000);
  
  // Вращение назад
  digitalWrite(motorPin1, LOW);
  digitalWrite(motorPin2, HIGH);
  delay(2000);
  
  // Остановка
  digitalWrite(motorPin1, LOW);
  digitalWrite(motorPin2, LOW);
  delay(1000);
}
```

### Управление скоростью двигателя с помощью ШИМ

Скорость двигателя можно регулировать с помощью ШИМ-сигнала:

```cpp
int enablePin = 5;  // ШИМ пин на драйвере двигателя

void loop() {
  // Увеличение скорости
  for (int speed = 0; speed <= 255; speed += 5) {
    analogWrite(enablePin, speed);
    delay(50);
  }
  
  delay(1000);
  
  // Уменьшение скорости
  for (int speed = 255; speed >= 0; speed -= 5) {
    analogWrite(enablePin, speed);
    delay(50);
  }
  
  delay(1000);
}
```

## 5. Примеры проектов с использованием изученных компонентов

### Автоматический полив растений

Комбинация датчика влажности почвы и водяного насоса:

```cpp
#include <Servo.h>

Servo waterServo;
const int moisturePin = A0;
const int servoPin = 9;
const int threshold = 300; // Порог влажности

void setup() {
  Serial.begin(9600);
  waterServo.attach(servoPin);
  waterServo.write(0); // Серво закрыта (насос выключен)
}

void loop() {
  int moistureLevel = analogRead(moisturePin);
  Serial.print("Влажность: ");
  Serial.println(moistureLevel);
  
  if (moistureLevel < threshold) {
    Serial.println("Влага низкая! Включаем полив...");
    waterServo.write(90); // Открыть серво (включить насос)
    delay(5000);          // Работа насоса 5 секунд
    waterServo.write(0);  // Закрыть серво (выключить насос)
    delay(300000);        // Ждать 5 минут перед следующим поливом
  } else {
    Serial.println("Влажность в норме");
  }
  
  delay(1000);
}
```

### Система охраны с датчиком движения

Комбинация датчика движения, сервопривода и светодиодов:

```cpp
const int pirPin = 2;      // Датчик движения
const int servoPin = 9;    // Серво для поворота камеры
const int ledPin = 13;     // Индикатор срабатывания
#include <Servo.h>

Servo cameraServo;

void setup() {
  pinMode(pirPin, INPUT);
  pinMode(ledPin, OUTPUT);
  cameraServo.attach(servoPin);
  Serial.begin(9600);
}

void loop() {
  int motionState = digitalRead(pirPin);
  
  if (motionState == HIGH) {
    Serial.println("Обнаружено движение!");
    digitalWrite(ledPin, HIGH);
    cameraServo.write(180); // Повернуть камеру
    delay(5000);            // Ждать 5 секунд
    cameraServo.write(0);   // Повернуть камеру обратно
    delay(1000);
  } else {
    digitalWrite(ledPin, LOW);
  }
  
  delay(100);
}
```

Эти проекты демонстрируют интеграцию различных датчиков и исполнительных механизмов для решения реальных задач. Практическое применение этих компонентов позволяет создавать полноценные автоматизированные системы.