# address

Модель позволяет мерчанту создавать адреса криптовалют и получать их параметры, применяемые для отправки и приема платежей.

Используя методы модели **address** мерчант может:

1. Создать адрес криптовалюты - [address.create](address.md#address_create)
2. Получить  детали криптоадреса мерчанта - [address.get](address.md#address_get)
3. Получить список криптоадресов мерчанта - [address.list](address.md#address_list)
4. Получить список доступных платежных направлений - [address.payways](address.md#address_payways)

## endpoint

Запросы на работу методов модели отправляются на endpoint **`https://api.any.money/`**.

## address.create

Метод создает адрес криптовалюты \(криптокошелек\) и возвращает репрезентацию его параметров.

??? abstract "Входящие параметры метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| callback\_url | no | "[https://any\_money.redirect.com](https://any_money.redirect.com)" | URL для отправки уведомлений о приходе платежа на адрес, [формат уведомлений](add_order.md#callback_repr) |
| comment | no | "Комментарий" | комментарий для криптоадреса, не более 50 символов |
| in\_curr | yes | "BTC" | валюта создаваемого криптоадреса |
| out\_curr | no | "UAH" | валюта зачисления для создаваемого криптоадреса \(если создается адрес с автоконвертацией\) |
| payway | no | "btc" | наименование платежной системы создаваемого криптоадреса |

!!! warning "Внимание!" Для типизированного мерчанта передаваемая платежная система должна совпадать с его типом, а **out\_curr** передавать нельзя так как операция конвертации валют недоступна для такого мерчанта

!!! warning "Внимание!" Процесс создания криптоадреса длится ощутимое время, скорее всего придется вызывать метод **address.get** несколько раз, пока в возвращаемой репрезентации адреса не появится сам криптоадрес \(значение параметра **name**\).

!!! success "Данные ответа метода" [Параметры](add_address.md) созданного криптоадреса

??? failure "Возможные возвращаемые ошибки" **EParamCurrencyInvalid**

## address.get

Метод поиска и получения информации о привязанном к мерчанту криптоадресе.  
Возвращает репрезентацию найденного криптоадреса.

??? abstract "Входящие параметры метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| name | yes\* | "3E8sEHMof39RgSHqSZswintqaXi3fSKYJN" | сам криптоадрес |
| id | yes\* | "844422933266350" | идентификатор криптоадреса |

```text
\*обязательно указать **только один** из параметров **id** или **name**
```

!!! success "Данные ответа метода" [Параметры](add_address.md) криптоадреса

??? failure "Возможные возвращаемые ошибки" **EParamInvalid**не передан ни один из параметров \*\*name\*\* или \*\*id\*\*, передано неверное значение параметра \*\*id\*\* или \*\*name\*\*, переданы одновременно \*\*name\*\* и \*\*id\*\***EParamNotFound**не найден ни один адрес с переданными параметрами**EParamType**переданы данные неверного типа для параметра \*\*id\*\*

## address.list

Метод отображения перечня криптоадресов, связанных с мерчантом. Для каждого приводятся его параметры. Фильтры поиска объединены логическим **И**.

??? abstract "Входящие параметры метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| begin | no | "123456789" | фильтр начала диапазона выборки ответов по дате и времени \(timestamp\). Если **rotate**=**true**, то по дате и времени последнего использования адреса \(timestamp\), а если **rotate**=**false**, то по времени его создания. default="0" |
| count | no | "136" | размер выборки ответов метода \(значение не может превышать 200\). default="20" |
| end | no | "234567890" | фильтр окончания диапазона выборки ответов по дате и времени \(timestamp\). Если **rotate**=**true**, то по дате и времени последнего использования адреса \(timestamp\), а если **rotate**=**false**, то по времени его создания. default="now" |
| first | no | "17" | номер объекта в полученной выборке результатов, с которого начнётся вывод перечня адресов и отсчет параметра **count** |
| in\_curr | no | "BTC" | фильтр криптоадресов по криптовалюте. В ответ метода попадут криптоадреса только заданной криптовалюты |
| is\_autoconvert | no | false | фильтр адресов с автоконвертацией \(значение **true** - в выборку попадут только адреса с автоконвертацией, **false** - без автоконвертации\) |
| name | no | "dsf34s5t" | фильтр криптоадресов по имени адреса, включая частичное совпадение |
| ord\_by | no | "ctime" | фильтр адресов по временнЫм меткам \(“ctime” - время создания, “last\_used” - время последнего использования\) |
| ord\_dir | no | false | направление сортировки по временнЫм меткам |
| out\_curr | no | "USD" | фильтр по валюте зачисления для криптоадресов с автоконвертацией. В ответ метода попадут только криптоадреса с автоконвертацией в заданную валюту |
| rotate | no | false | фильтр адресов, включенных в авторотацию \(значение **true** - в выборку попадут только с включенной авторотацией, **false** - только с выключенной\). default=**false** |

??? info "Особенности обработки пары параметров **begin**, **end**" Аргументу **begin** система **всегда** присвоит меньшее значение из переданной пары, а для **end** - всегда большее. Вне зависимости от того, под каким из имён они были переданы

!!! warning "Внимание!" Для типизированных мерчантов значение **in\_curr** должно быть равно **out\_curr** или оба параметра не передаются, иначе возвратится ошибка

!!! success "Данные ответа метода" **count**размер текущей выборки ответов**data**массив с \[репрезентациями данных\]\(add\_address.md\) криптоадресов**first**номер первого адреса в выборке ответов метода, с него начинается отсчет \*\*count\*\***page**текущая страница ответов**page\_total**всего страниц ответов**total**всего найдено адресов, соответствующих фильтрам запроса

??? failure "Возможные возвращаемые ошибки" **EParamCurrencyInvalid**валюта отсутствует в системе**EParamInvalid**

## address.payways

Метод возвращает имена всех крипто-ПС, для которых мерчант может создать адрес. Если передан **in\_curr**, то в ответе войдут названия **только** тех платежных систем, которые работают с данной валютой.

??? abstract "Входящие параметры метода"

| param | required | example | description |
| ---: | :---: | :--- | :--- |
| in\_curr | no | "BTC" | название валюты |

!!! success "Данные ответа метода" Перечень доступных мерчанту крипто-ПС, в которых есть валюта **in\_curr**

??? failure "Возможные возвращаемые ошибки" **EParamNotFound**не удалось найти ни одной крипто-ПС**EParamPaywayInvalid**недопустимое имя крипто-ПС**EParamCurrencyInvalid**недопустимое имя криптовалюты**EStateCurrencyUnavail**не удалось найти криптовалюту с переданным именем \*\*in\_curr\*\*, или валюта не является криптовалютой
