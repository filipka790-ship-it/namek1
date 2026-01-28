# Инструкция по тестированию API - Игра "Намек"

## Базовый URL
```
https://se.ifmo.ru/~s368721/namek/backend
```

---

## 1. Функция - Регистрация
**Endpoint:** `newuser.php`  
**Метод:** POST  
**Content-Type:** application/json

### Параметры:
```json
{
  "login": "player1",
  "password": "password123"
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "token": 123456789
}
```

---

## 2. Функция - Вход (Авторизация)
**Endpoint:** `auth.php`  
**Метод:** POST  
**Content-Type:** application/json

### Параметры:
```json
{
  "login": "player1",
  "password": "password123"
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "token": 123456789
}
```

**Сохраните token для дальнейших запросов!**

---

## 3. Функция - Создание игры
**Endpoint:** `create_game.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "seats": 5,
  "cards_limit": 10,
  "time_for_hint": 60
}
```

**Описание параметров:**
- `seats` - количество мест в игре (3-5 рекомендуется)
- `cards_limit` - количество карт в колоде (рекомендуется 10)
- `time_for_hint` - время на подсказки в секундах

### Ответ (успешный):
```json
{
  "ok": true,
  "game_id": 1,
  "player_id": 1
}
```

**Сохраните game_id и player_id!**

---

## 4. Функция - Получить список доступных игр
**Endpoint:** `games_list.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "only_joinable": true,
  "limit": 50,
  "offset": 0
}
```

**Описание параметров:**
- `only_joinable` - показывать только игры в лобби (необязательно)
- `limit` - количество игр (по умолчанию 50)
- `offset` - смещение (по умолчанию 0)

### Ответ (успешный):
```json
{
  "ok": true,
  "games": [
    {
      "game_id": 1,
      "state": "lobby",
      "seats": 5,
      "players_count": 2,
      "cards_limit": 10,
      "time_for_hint": 60,
      "created_at": "2026-01-27T10:30:00"
    }
  ],
  "limit": 50,
  "offset": 0
}
```

---

## 5. Функция - Присоединиться к игре
**Endpoint:** `join_game.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 987654321
```

### Параметры:
```json
{
  "game_id": 1
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "player_id": 2
}
```

**Сохраните player_id второго игрока!**

---

## 6. Функция - Получить состояние игры
**Endpoint:** `state.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "game": {
    "id": 1,
    "state": "lobby",
    "seats": 5,
    "score": 0,
    "time_for_hint": 60,
    "cards_limit": 10,
    "deck_left": 10
  },
  "players": [
    {
      "id": 1,
      "login": "player1",
      "seat_no": 1
    },
    {
      "id": 2,
      "login": "player2",
      "seat_no": 2
    }
  ],
  "round": null,
  "you": {
    "player_id": 1,
    "is_leader": false,
    "is_judge": false
  }
}
```

---

## 7. Функция - Начать игру (собрать колоду)
**Endpoint:** `start_game.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "deck_built",
  "game_id": 1,
  "players": 2,
  "deck_size": 10
}
```

---

## 8. Функция - Начать раунд
**Endpoint:** `start_round.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "round_id": 1,
  "leader_id": 1,
  "judge_id": 2
}
```

**Описание:**
- `leader_id` - ID ведущего (отгадывает слово)
- `judge_id` - ID судьи (проверяет подсказки)

---

## 9. Функция - Выбрать номер слова (фаза choose_number)
**Endpoint:** `choose_number.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1,
  "round_id": 1,
  "target_pos": 3
}
```

**Описание параметров:**
- `target_pos` - номер слова (от 1 до 5)

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "hinting_started"
}
```

---

## 10. Функция - Отправить подсказку (фаза hinting)
**Endpoint:** `submit_hint.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 987654321
```

### Параметры:
```json
{
  "player_id": 2,
  "round_id": 1,
  "slot": 1,
  "text": "это животное"
}
```

**Описание параметров:**
- `slot` - слот для подсказки (всегда 1)
- `text` - текст подсказки

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "hint_saved"
}
```

---

## 11. Функция - Начать очистку подсказок (фаза judge_cleanup)
**Endpoint:** `begin_cleanup.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 987654321
```

### Параметры:
```json
{
  "player_id": 2,
  "round_id": 1
}
```

**Может вызвать только судья (judge_id)**

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "judge_cleanup"
}
```

---

## 12. Функция - Судья удалить подсказку
**Endpoint:** `judge_remove_hint.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 987654321
```

### Параметры:
```json
{
  "player_id": 2,
  "round_id": 1,
  "hint_id": 5,
  "reason": "слишком похоже на ответ"
}
```

**Может вызвать только судья**

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "removed"
}
```

---

## 13. Функция - Судья завершить очистку (фаза guessing)
**Endpoint:** `judge_finish_cleanup.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 987654321
```

### Параметры:
```json
{
  "player_id": 2,
  "round_id": 1
}
```

**Может вызвать только судья**

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "guessing",
  "kept_hints": 3
}
```

---

## 14. Функция - Ведущий угадывает (фаза guessing)
**Endpoint:** `leader_guess.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры (вариант 1 - угадать):
```json
{
  "player_id": 1,
  "round_id": 1,
  "pass": false,
  "guess": "лиса"
}
```

### Параметры (вариант 2 - пасовать):
```json
{
  "player_id": 1,
  "round_id": 1,
  "pass": true
}
```

**Может вызвать только ведущий (leader_id)**

### Ответ (успешный):
```json
{
  "ok": true,
  "result": "success",
  "target": "лиса",
  "kept_hints": 3
}
```

**Возможные результаты:**
- `success` - правильное угадывание
- `fail` - неправильное угадывание
- `pass` - пасовал

---

## 15. Функция - Перейти к следующему раунду (фаза result)
**Endpoint:** `round_next.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1
}
```

### Ответ (если есть ещё карты):
```json
{
  "ok": true,
  "round_id": 2
}
```

### Ответ (игра закончена):
```json
{
  "ok": true,
  "status": "finished",
  "score": 3
}
```

---

## 16. Функция - Удалить игру
**Endpoint:** `delete_game.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "game_id": 1
}
```

**Может удалить только создатель игры (первый игрок)**

### Ответ (успешный):
```json
{
  "ok": true,
  "status": "deleted",
  "game_id": 1
}
```

---

## 17. Функция - Отправить сообщение в чат
**Endpoint:** `chat_send.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1,
  "text": "давайте начинать!"
}
```

### Ответ (успешный):
```json
{
  "ok": true,
  "message": {
    "id": 1,
    "player_id": 1,
    "text": "давайте начинать!",
    "time": "2026-01-27T10:30:00"
  }
}
```

---

## 18. Функция - Получить историю чата
**Endpoint:** `chat_list.php`  
**Метод:** POST  
**Content-Type:** application/json  
**Headers:**
```
X-Auth-Token: 123456789
```

### Параметры:
```json
{
  "player_id": 1,
  "after_id": 0,
  "limit": 100
}
```

**Описание параметров:**
- `after_id` - получить сообщения после этого ID (0 = с начала)
- `limit` - количество сообщений (по умолчанию 200)

### Ответ (успешный):
```json
{
  "ok": true,
  "messages": [
    {
      "id": 1,
      "player_id": 1,
      "text": "давайте начинать!",
      "time": "2026-01-27T10:30:00"
    }
  ],
  "last_id": 1
}
```

---

## Сценарий полного тестирования

### Шаг 1: Регистрация двух игроков
```
POST /newuser.php
{"login": "alice", "password": "pass123"}
Ответ: {"token": TOKEN_ALICE}

POST /newuser.php
{"login": "bob", "password": "pass456"}
Ответ: {"token": TOKEN_BOB}
```

### Шаг 2: Создание игры (Alice)
```
POST /create_game.php
Headers: X-Auth-Token: TOKEN_ALICE
{"seats": 5, "cards_limit": 10, "time_for_hint": 60}
Ответ: {"game_id": 1, "player_id": 1}
```

### Шаг 3: Присоединение второго игрока (Bob)
```
POST /join_game.php
Headers: X-Auth-Token: TOKEN_BOB
{"game_id": 1}
Ответ: {"player_id": 2}
```

### Шаг 4: Проверка состояния
```
POST /state.php
Headers: X-Auth-Token: TOKEN_ALICE
{"player_id": 1}
Ответ: состояние игры с двумя игроками
```

### Шаг 5: Начать игру (собрать колоду)
```
POST /start_game.php
Headers: X-Auth-Token: TOKEN_ALICE
{"player_id": 1}
```

### Шаг 6: Начать раунд
```
POST /start_round.php
Headers: X-Auth-Token: TOKEN_ALICE
{"player_id": 1}
Ответ: {"round_id": 1, "leader_id": 1, "judge_id": 2}
```

### Шаг 7: Ведущий выбирает номер (Alice)
```
POST /choose_number.php
Headers: X-Auth-Token: TOKEN_ALICE
{"player_id": 1, "round_id": 1, "target_pos": 3}
```

### Шаг 8: Bob отправляет подсказку
```
POST /submit_hint.php
Headers: X-Auth-Token: TOKEN_BOB
{"player_id": 2, "round_id": 1, "slot": 1, "text": "дикое животное"}
```

### Шаг 9: Bob начинает очистку (он судья)
```
POST /begin_cleanup.php
Headers: X-Auth-Token: TOKEN_BOB
{"player_id": 2, "round_id": 1}
```

### Шаг 10: Bob завершает очистку
```
POST /judge_finish_cleanup.php
Headers: X-Auth-Token: TOKEN_BOB
{"player_id": 2, "round_id": 1}
```

### Шаг 11: Alice угадывает (она ведущая)
```
POST /leader_guess.php
Headers: X-Auth-Token: TOKEN_ALICE
{"player_id": 1, "round_id": 1, "pass": false, "guess": "лиса"}
```

### Шаг 12: Следующий раунд
```
POST /round_next.php
Headers: X-Auth-Token: TOKEN_ALICE
{"player_id": 1}
```

---

## Коды ошибок

| Ошибка | Описание |
|--------|---------|
| `invalid_token` | Токен неверный или просрочен |
| `invalid_player` | Игрок не найден |
| `game_not_found` | Игра не найдена |
| `game_full` | В игре нет свободных мест |
| `game_already_started` | Игра уже началась |
| `round_not_found` | Раунд не найден |
| `wrong_phase` | Неверная фаза раунда для этого действия |
| `only_leader` | Только ведущий может выполнить это действие |
| `only_judge` | Только судья может выполнить это действие |
| `not_ready_yet` | Не все игроки готовы |
| `need_at_least_3_players` | Требуется минимум 3 игрока |
| `game_finished` | Игра уже закончена |
| `token_player_mismatch` | Токен не соответствует игроку |

---


