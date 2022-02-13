# бинанс-торговля-бот

## Почему?

Этот проект был вдохновлен наблюдением, что все криптовалюты ведут себя примерно одинаково. Когда одна растет, то они все растут, и когда одна падает, они все падают. _Довольно много_. Более того, все монеты следуют примеру Биткойна; разница в их фазовом сдвиге.

Таким образом, если монеты в основном колеблются по отношению друг к другу, кажется разумным обменять растущую монету на падающую монету, а затем торговать обратно, когда соотношение меняется на противоположное.

## Как?

Торговля осуществляется на торговой платформе Binance, которая, конечно же, не имеет рынков для каждой пары альткойнов. Обходной путь для этого — использовать промежуточную валюту, которая дополнит отсутствующие пары. Валютой моста по умолчанию является Tether (USDT), который стабилен по своей конструкции и совместим практически со всеми монетами на платформе.

<p выровнять="по центру">
  Монета A → USDT → Монета B
</p>

Способ, которым бот использует наблюдаемое поведение, заключается в том, чтобы всегда переходить от «сильной» монеты к «слабой» монете, исходя из предположения, что в какой-то момент ситуация изменится. Затем он вернется к исходной монете, в конечном итоге удерживая ее больше, чем изначально. Это делается с учетом торговых сборов.

<div align="центр">
  <p><b>Монета A</b> → USDT → Монета B</p>
  <p>Монета B → USDT → Монета C</p>
  <p>...</p>
  <p>Монета C → USDT → <b>Монета A</b></p>
</div>

Бот переключается между настроенным набором монет при условии, что он не вернется к монете, если только она не будет прибыльной по сравнению с последней удерживаемой суммой. Это означает, что у нас никогда не будет меньше определенной монеты. Риск заключается в том, что одна из монет может внезапно упасть относительно других, привлекая наш обратный жадный алгоритм.

## Настройка Бинанс

- Создайте [учетную запись Binance](https://www.binance.com/en/register?ref=13222128) (включая мою реферальную ссылку, я буду очень признателен, если вы ее воспользуетесь).
- Включить двухфакторную аутентификацию.
- Создайте новый ключ API.
- Получить криптовалюту. Если его символ отсутствует в списке по умолчанию, добавьте его.

## Настройка инструмента

### Установить зависимости Python

Запустите в терминале следующую строку: `pip install -r requirements.txt`.

### Создать пользовательскую конфигурацию

Создайте файл .cfg с именем `user.cfg` на основе `.user.cfg.example`, затем добавьте свои ключи API и текущую монету.

**Файл конфигурации состоит из следующих полей:**

- **api_key** — ключ Binance API, сгенерированный на этапе настройки учетной записи Binance.
- **api_secret_key** — секретный ключ Binance, сгенерированный на этапе настройки учетной записи Binance.
- **current_coin** - это ваша начальная монета. Это должна быть одна из монет из вашего списка поддерживаемых монет. Если вы хотите начать с вашей валюты бриджа, оставьте это поле пустым — бот выберет случайную монету из вашего списка поддерживаемых монет и купит ее.
- **bridge** - выбранная вами валюта бриджа. Обратите внимание, что разные мосты позволяют использовать разные наборы поддерживаемых монет. Например, на Binance может быть пара «конкретная монета/USDT», но не может быть пары «конкретная монета/BUSD».
- **tld** - «com» ​​или «us», в зависимости от вашего региона. По умолчанию «com».
- **hourToKeepScoutHistory** - контролирует, сколько часов значений разведки хранятся в базе данных. По истечении указанного времени информация будет удалена.
- **scout_sleep_time** - контролирует, сколько секунд ожидания между каждым разведчиком.
- **use_margin** - 'да' для использования scout_margin. «нет», чтобы использовать scout_multiplier.
- **scout_multiplier** - Управляет значением, на которое умножается разница между текущим состоянием соотношения монет и предыдущим состоянием соотношения. Для больших значений бот будет ждать получения большей маржи, прежде чем совершить сделку.
- **scout_margin** - Минимальный процент прироста монет за сделку. 0,8 означает скаутский множитель 5 при комиссии 0,1%.
- **strategy** - используемая торговая стратегия. См. [`binance_trade_bot/strategies`](binance_trade_bot/strategies/README.md) для получения дополнительной информации.
- **buy_timeout/sell_timeout** - определяет, сколько минут ждать до отмены лимитного ордера (купить/продать) и вернуться в режим "разведки". 0 означает, что заказ никогда не будет отменен досрочно.
- **scout_sleep_time** - Контролирует, сколько секунд бот должен ждать между анализом текущих цен. Поскольку бот теперь работает с веб-сокетами, это значение должно быть установлено на что-то низкое (например, 1).
