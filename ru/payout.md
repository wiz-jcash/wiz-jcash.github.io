# payout

Используя методы модели **payout** мерчант может:

1. Создать ордер выплаты - метод [payout.create](payout.md#payout_create)
2. Рассчитать предварительные параметры ордера выплаты - метод [payout.calc](payout.md#payout_calc)
3. Получить информацию по выбранной операции выплаты - метод [payout.get](payout.md#payout_get)

## endpoint

Запросы на работу методов модели отправляются на endpoint **`https://api.any.money/`**.

## payout.create

Метод создает ордер операции выплаты и возвращает его репрезентацию.

Если параметр **in\_curr** не передан, то его значение будет принято равным значению **out\_curr**.

??? abstract "Входящие параметры метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| amount | yes | "128" | сумма выплаты |
| callback\_url | no | "[https://any\_money.redirect.com](https://any_money.redirect.com)" | url-адрес для уведомлений про изменения состояния ордера, [формат уведомлений](add_order.md#order_repr) |
| externalid | yes | "123" | уникальный идентификатор, заданный мерчантом |
| in\_curr | no | "USD" | название валюты списания |
| out\_curr | yes | "UAH" | название валюты зачисления |
| payway | yes | "anycash" | платежная система, через которую проводится выплата |

В перечне входящих данных метода в зависимости от используемой платежной системы \(**payway**\) в запросе **необходимо** передать следующие значения:

??? info "Обязательные параметры для указанных платёжных систем" **advcash, nixmoney, qiwi, payeer, perfect, webmoney**

!!! warning "Внимание!" Для типизированных мерчантов передаваемая платежная система должна совпадать с типом мерчанта, а значение **in\_curr** должно быть равно **out\_curr** или отсутствовать, иначе будут возвращены ошибки

!!! warning "Внимание!" Сразу после поступления запроса создается ордер и вызывается его обработка, а после выполнения метода **деньги** будут **списаны с баланса**

!!! success "Данные ответа метода" [репрезентация ордера](add_order.md) операции выплаты

??? failure "Возможные возвращаемые ошибки" **EParamAmountFormatInvalid**неверное значение или формат параметра \*\*amount\*\***EParamAmountTooBig**\*\*amount\*\* больше максимальной суммы операции**EParamAmountTooSmall**\*\*amount\*\* меньше минимальной суммы операции**EParamCurrencyInvalid**валюта отсутствует в Any.Money**EParamFieldInvalid**передан невалидный параметр \*\*callback\_url\*\***EParamInvalid**

## payout.calc

Метод расчета параметров выплаты с учётом суммы.

Если параметр **in\_curr** не передан, то будет произведен расчет выплаты в валюте **out\_curr** и снятие средств с баланса **out\_curr** с учетом комиссии для переданной платежной системы **payway**.

??? abstract "Входящие параметры метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| amount | yes | "238" | сумма выплаты |
| in\_curr | no | "UAH" | название валюты списания |
| out\_curr | yes | "RUB" | название валюты зачисления |
| payway | yes | "anycash" | платежная система, через которую проводится выплата |

!!! warning "Внимание!" Для типизированных мерчантов передаваемая платежная система должна совпадать с типом мерчанта, а значение **in\_curr** должно быть равно **out\_curr** или отсутствовать, иначе будут возвращены ошибки

!!! success "Данные ответа метода" Параметры ордера выплаты **account\_amount**сумма списания за вычетом комиссии**in\_amount**сумма списания**in\_fee\_amount**сумма комиссии в валюте списания**orig\_amount**оригинальная сумма выплаты, которая была заказана при создании ордера**out\_amount**сумма зачисления**out\_fee\_amount**сумма комиссии в валюте зачисления**rate**курс обмена валют, если проводилась конвертация. В формате \`"rate": \["25.30301", "1"\]\`

??? failure "Возможные возвращаемые ошибки" **EParamAmountFormatInvalid**неправильный формат параметра \*\*amount\*\***EParamAmountTooBig**\*\*amount\*\* больше максимальной суммы операции выплаты**EParamAmountTooSmall**\*\*amount\*\* меньше минимальной суммы операции выплаты**EParamCurrencyInvalid**валюта отсутствует в системе**EParamInvalid**

## payout.get

Метод получения репрезентации параметров ордера выплаты по его идентификатору \(**externalid**\).

??? abstract "Входящий параметр метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| externalid | yes | "221" | уникальный идентификатор ордера, заданный мерчантом |

!!! success "Данные ответа метода" [репрезентация ордера](add_order.md) операции выплаты

??? failure "Возможные возвращаемые ошибки" **EParamInvalid**передано неверное значение параметра \*\*externalid\*\***EParamNotFound**не найден ордер c соответствующим \*\*externalid\*\***EParamType**формат переданного \*\*externalid\*\* не соответствует ожидаемому
