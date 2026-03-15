# Gatekeeper — Flipper Zero App

Secure BadUSB password launcher, защищённый мастер-комбо из кнопок.

---

## Структура файлов

```
gatekeeper/
├── application.fam   ← манифест приложения
├── gatekeeper.h      ← заголовки, структуры, константы
├── gatekeeper.c      ← вся логика
└── gatekeeper_icon.png  ← 10×10 иконка (добавьте сами)
```

---

## Как собрать

### Требования
- [flipperzero-firmware](https://github.com/flipperZero/flipperzero-firmware) или [unleashed-firmware](https://github.com/DarkFlippers/unleashed-firmware)
- Python 3, arm-none-eabi-gcc

### Шаги
```bash
# 1. Клонируйте прошивку (пример: unleashed)
git clone --recursive https://github.com/DarkFlippers/unleashed-firmware.git
cd unleashed-firmware

# 2. Скопируйте папку приложения
cp -r /path/to/gatekeeper applications_user/gatekeeper/

# 3. Добавьте иконку 10×10 пикселей PNG
cp my_icon.png applications_user/gatekeeper/gatekeeper_icon.png

# 4. Соберите .fap
./fbt fap_gatekeeper

# 5. Скопируйте на карту памяти Flipper
# Файл будет в: build/f7-firmware-D/apps/Tools/gatekeeper.fap
# Скопируйте его в /ext/apps/Tools/ на SD-карте Flipper
```

---

## Пользовательский сценарий

### Первый запуск
1. Приложение просит **задать мастер-комбо** (3–8 кнопок из ↑↓←→ OK BCK)
2. Нужно ввести комбо **дважды** для подтверждения
3. Комбо сохраняется в `SD:/apps_data/gatekeeper/gatekeeper.dat`

### Каждый вход
1. Экран разблокировки — введите комбо + OK
2. При неверном вводе — надпись "Wrong combo!" и буфер сбрасывается
3. Удержание BACK на экране разблокировки — выход из приложения

### Главное меню
| Кнопка | Действие |
|--------|----------|
| ↑ / ↓ | Навигация по списку |
| OK на "+ Add" | Добавить новую запись |
| OK на записи | Открыть Deploy-экран |
| BACK | Вернуться на экран разблокировки |

### Добавление записи
1. Нажмите **+ Add Password** в верхней строке
2. Введите **имя** (отображается в списке)
3. Введите **текст** — именно он будет напечатан через HID при деплое

> Ввод текста через встроенную клавиатуру Flipper (TextInput view)

### Deploy-экран
- В центре — название записи
- Нажмите **OK** → прогресс-бар заполняется, текст вводится через USB HID
- По завершении → "Done! [BACK] return"
- BACK в любой момент — вернуться в меню

---

## Хранение данных

Данные сохраняются в бинарном формате:
```
[1 byte: длина комбо]
[N bytes: байты комбо (GK_BTN_*)]
[1 byte: кол-во записей]
для каждой записи:
  [1 byte: длина имени]
  [N bytes: имя]
  [1 byte: длина текста]
  [N bytes: текст]
```

Файл: `/ext/apps_data/gatekeeper/gatekeeper.dat`

---

## Коды кнопок в пароле

| Кнопка  | Код |
|---------|-----|
| ↑ Up    | 0x01 |
| ↓ Down  | 0x02 |
| ← Left  | 0x03 |
| → Right | 0x04 |
| OK      | 0x05 |
| BACK    | 0x06 |

---

## Безопасность

- Комбо хранится в бинарном виде на SD — не зашифровано.  
  Для дополнительной защиты можно добавить XOR-обфускацию ключом.
- Приложение не блокирует Flipper физически — защита программная.
- Не оставляйте Flipper разблокированным без присмотра.

---

## Добавление иконки

Создайте PNG 10×10 пикселей чёрно-белый, назовите `gatekeeper_icon.png` и положите рядом с `.c` файлом. Инструмент `fbt` конвертирует его автоматически.
