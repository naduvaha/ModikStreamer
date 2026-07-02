# Modik Streamer by Staili — v0.1.0

Стриминг-мод для Casualties Unknown: подключается к DonationAlerts и превращает каждый донат в
внутриигровое событие. Компаньон-приложение `CasualStreamer.exe` управляет всеми настройками,
интегрируется с OBS (obs-websocket + Browser Source overlay) и общается с BepInEx-плагином через
локальный WebSocket.

Streaming mod for Casualties Unknown: hooks DonationAlerts and turns every donation into an
in-game event. The `CasualStreamer.exe` companion app manages every setting, integrates with OBS
(obs-websocket + Browser Source overlay) and talks to the BepInEx plugin over a local WebSocket.

---

<details>
<summary><b>🇷🇺 Установка</b></summary>

### Что положить и куда

1. **Плагин игры.** Скопируй `ModikStreamer/ModikStreamerByStaili.dll` в папку
   `Casualties Unknown\BepInEx\plugins\`. Нужен уже установленный BepInEx 5 и мод
   `MultyModikByStaili` (для эвента "Босс-паук" — иначе он просто заспавнит стайку обычных пауков).

2. **Компаньон-приложение.** Разархивируй `ModikStreamerClient/` куда угодно (например, рядом с
   игрой). Запусти `CasualStreamer.exe`. Настройки хранятся в `%AppData%\CasualStreamer\`.

### Первый запуск

1. Открой `CasualStreamer.exe` — сразу увидишь 5 вкладок сверху.
2. Перейди в **DonationAlerts → Sign in / Войти**. Откроется браузер — авторизуйся на DA.
3. После разрешения увидишь статус "Signed in" и в правом нижнем углу зелёный маркер DA online.
4. Запусти игру. Плагин подключится к клиенту через ~1 секунду. Второй зелёный маркер (IPC) —
   значит DLL на связи.
5. Нажми "Тест донат 500₽" — должен сработать эвент (по умолчанию 500₽ = звуковая пушка).

### Настройка эвентов

Вкладка **Эвенты / Events**. У каждого из 22 эвентов — чекбокс "включен", слайдер и поле с
минимальной суммой (₽), кнопка "Тест". При донате берётся эвент с самой высокой суммой,
которую покрывает донат.

### Настройка OBS

Вкладка **OBS**:

- В OBS: Tools → WebSocket Server Settings → включи, поставь пароль, порт по умолчанию 4455.
- В CasualStreamer: вбей адрес/порт/пароль → "Подключить" → "Обновить сцены". После этого
  можешь в `%AppData%\CasualStreamer\config.json` прописать per-event `obsActions`
  (переключение сцены, показ источника, включение фильтра).

### Настройка Browser Source overlay

Вкладка **Overlay**:

- Скопируй URL (`http://127.0.0.1:19871/overlay`) → в OBS добавь Sources → Browser →
  URL, 1920×1080, "Прозрачный фон" включён.
- Шаблон HTML редактируется прямо во вкладке. Кнопка "Восстановить дефолт" вернёт исходный.

</details>

<details>
<summary><b>🇷🇺 Список эвентов (по умолчанию)</b></summary>

| Сумма ₽ | Id                    | Что происходит |
|---:|---|---|
| 100  | `fx_party_pop`        | Хлопушка (`pop`) у игрока + немного радости |
| 150  | `heal_small`          | Немного стамины/энергии/happiness случайному игроку |
| 200  | `happy_boost`         | +10 happiness всей команде |
| 250  | `firework`            | Салют (мина в воздухе рядом с игроком) |
| 300  | `random_snack`        | Кинуть случайный дешёвый предмет рядом с игроком |
| 400  | `spawn_land_mine`     | 2 мины на карте |
| 500  | `spawn_sound_cannon`  | 1 звуковая пушка |
| 600  | `spawn_turret`        | 1 турель |
| 700  | `weather_rain`        | Мрачная атмосфера (тонировка света + eyeScare) |
| 800  | `full_backpack`       | Рюкзак припасов рядом с игроком |
| 900  | `spawn_gun_mine`      | 2 стреляющих мины |
| 1000 | `spider_swarm`        | Рой пауков вокруг команды |
| 1200 | `spawn_coil`          | 1 электрокатушка |
| 1500 | `extra_enemies_layer` | Волна врагов на слой |
| 2000 | `big_spider_boss`     | Большой босс-паук (из MultyModik) |
| 2500 | `chaos_moodles`       | Хаос настроений у всех |
| 3000 | `earthquake`          | Мощный катаклизм: у каждого игрока пик шока/адреналина/паники + мины/пушмины/пауки по широкому радиусу вокруг эпицентра + доп. волна пауков рядом со случайным игроком |
| 4000 | `boss_wave`           | Босс-волна |
| 5000 | `curse_all`           | Проклятие: у всех игроков сразу шок 90, адреналин 90, паника 15с, ужас +50, happiness −40, стамина/энергия обрезаются до 20, болезнь +25 |
| 7500 | `dark_hour`           | Тёмный час: тьма + пауки + мины |
| 10000 | `apocalypse`         | Всё сразу: DarkHour + BigSpider + swarm + earthquake + chaos |
| —    | `custom_command`      | Ручной триггер из клиента (кнопка "Тест") |

</details>

<details>
<summary><b>🇷🇺 Что если у меня своё DA-приложение?</b></summary>

По умолчанию клиент использует зашитый `client_id` (может быть пустым в этой сборке — тогда
Sign in выдаст ошибку). Чтобы использовать своё приложение:

1. Зайди на https://www.donationalerts.com/application/clients и создай приложение.
2. В поле "Redirect URL" впиши **точно** `http://localhost:64571/da/callback`.
3. В CasualStreamer → **Настройки / Settings** вставь `client_id` и `client_secret` → Сохранить.
4. В DonationAlerts → Sign in — авторизуйся заново.

</details>

<details>
<summary><b>🇷🇺 Полезные пути</b></summary>

- Настройки клиента: `%AppData%\CasualStreamer\config.json`
- OAuth-токены (DPAPI): `%AppData%\CasualStreamer\tokens.bin`
- Кастомный HTML overlay'а: `%AppData%\CasualStreamer\overlay.html`
- Лог клиента: `%AppData%\CasualStreamer\casualstreamer.log`
- Плагин игры: `Casualties Unknown\BepInEx\plugins\ModikStreamerByStaili.dll`
- Настройки плагина: `Casualties Unknown\BepInEx\config\ModikStreamerByStaili.cfg`
  (создаётся при первом запуске игры)

</details>

<details>
<summary><b>🇷🇺 Валюты (RUB / USD / EUR / прочие)</b></summary>

Пороги эвентов заданы в **рублях**. Донаты приходят в своей валюте — клиент автоматически
конвертирует сумму в рубли по настраиваемым курсам, и уже по этой сумме подбирает эвент.

- Курсы редактируются в **Настройки / Settings → Курсы валют**: поля "1 USD = ₽", "1 EUR = ₽" и
  "Неизвестная валюта = ₽ за 1 единицу" (0 = игнорировать неизвестные валюты).
- Остальные валюты (UAH, KZT, BYN и т.д.) со своими курсами лежат в
  `%AppData%\CasualStreamer\config.json` → `currencyRatesToRub`. Туда можно дописать любые ISO-коды.
- Значения по умолчанию: USD=90, EUR=100, UAH=2.3, KZT=0.19, BYN=28, fallback=90.
- В логе донатов рядом с суммой показывается её эквивалент в рублях (`~450₽`).

Пример: донат 5 USD при курсе 90 = 450₽ → сработает эвент с ближайшим порогом ≤ 450₽.

</details>

---

<details>
<summary><b>🇬🇧 Installation (EN)</b></summary>

1. **Game plugin.** Copy `ModikStreamer/ModikStreamerByStaili.dll` into
   `Casualties Unknown\BepInEx\plugins\`. Requires BepInEx 5 and (for the "Big Spider boss"
   event) the `MultyModikByStaili` mod — otherwise that event falls back to spawning a small
   spider swarm.

2. **Companion app.** Unpack `ModikStreamerClient/` anywhere, run `CasualStreamer.exe`. Settings
   live under `%AppData%\CasualStreamer\`.

### First run

1. Open `CasualStreamer.exe` and hit **DonationAlerts → Sign in**. A browser tab opens.
2. Approve access. The status bar should switch to "Signed in".
3. Launch the game. Within ~1 second the plugin's IPC dot in the CasualStreamer status bar
   should go green (DLL connected).
4. Press "Тест донат 500₽" to fire a synthetic donation → should trigger an in-game event.

### Events tab

22 events, each with an enable checkbox, minimum-amount slider (RUB), text field and a Test
button. A donation picks the enabled event with the highest threshold it covers.

### Currencies (RUB / USD / EUR / others)

Thresholds are stored in **RUB**. Non-RUB donations are converted to RUB using editable rates
before matching. Set them in **Settings → Currency rates** (USD, EUR, and a fallback rate for
unknown currencies; 0 = ignore). Additional currencies live in
`%AppData%\CasualStreamer\config.json` under `currencyRatesToRub`. Defaults: USD=90, EUR=100,
UAH=2.3, KZT=0.19, BYN=28, fallback=90. The donation log shows the RUB equivalent (`~450₽`).

### OBS integration

- In OBS: Tools → WebSocket Server Settings → enable, set a password, keep port 4455.
- In CasualStreamer → **OBS**: host/port/password → Connect → Refresh scenes. Per-event OBS
  actions can be edited under `%AppData%\CasualStreamer\config.json` (`events[eventId].obsActions`).

### Browser Source overlay

- CasualStreamer → **Overlay** → copy the URL → in OBS add Sources → Browser → 1920×1080.
- The HTML template is editable in the tab. "Восстановить дефолт" restores the shipped one.

</details>

<details>
<summary><b>🇬🇧 Bringing your own DonationAlerts app (EN)</b></summary>

By default the client uses the built-in `client_id`. If it's empty in this build (or you want
to use your own app):

1. https://www.donationalerts.com/application/clients → register a new app.
2. Set Redirect URL to **exactly** `http://localhost:64571/da/callback`.
3. Paste `client_id` + `client_secret` into CasualStreamer → **Settings** → Save.
4. Sign in again from the DonationAlerts tab.

</details>

<details>
<summary><b>🇬🇧 Paths (EN)</b></summary>

- Client settings: `%AppData%\CasualStreamer\config.json`
- OAuth tokens (DPAPI): `%AppData%\CasualStreamer\tokens.bin`
- Overlay HTML override: `%AppData%\CasualStreamer\overlay.html`
- Log: `%AppData%\CasualStreamer\casualstreamer.log`
- Plugin: `Casualties Unknown\BepInEx\plugins\ModikStreamerByStaili.dll`
- Plugin config: `Casualties Unknown\BepInEx\config\ModikStreamerByStaili.cfg`

</details>

---

## Architecture (at a glance)

```
[DonationAlerts]  --wss-->  [CasualStreamer.exe]  --ws-->  [ModikStreamerByStaili.dll]
                                     |                              |
                                     +--obs-websocket-->  [OBS]     +--> in-game events
                                     +--http SSE ------->  Browser Source overlay
```

- OAuth2 authorization code with loopback (`http://localhost:64571/da/callback`) — no server
  needed, the client hosts the callback itself.
- Centrifugo v2 WebSocket for realtime donations.
- Fleck-based WebSocket on `ws://127.0.0.1:19870/game` between client and DLL.
- HttpListener + Server-Sent Events on `http://127.0.0.1:19871/overlay` for OBS Browser Source.
- Every port is configurable in CasualStreamer → Settings.
