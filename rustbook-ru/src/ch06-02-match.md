## Оператор управления потоком выполнения `match`

В Rust есть весьма мощная конструкция `match`, которая позволяет сравнивать значение с серией шаблонов и затем выполнять код, в зависимости от того, какое значение совпало. Шаблоном могут выступать литералы, имена переменных, постановочные значения и многое другое. В главе 18 будет много рассказано о различных типах шаблонов и что каждый из них делает. Мощь `match` выражается в выразительной синтаксической конструкции и того факта, что компилятор убедится, что все возможные случаи обработаны.

Думайте о выражении `match` как о машине для сортировки монет: монеты скользят вниз по дорожке с отверстиями разного размера вдоль неё и каждая монета проваливается в первое отверстие, в которое она подходит. Таким же образом, значения проходят через каждый шаблон в конструкции `match` и в первом шаблоне с совпадающим значением, попадают в соответствующий блок кода, который будет использован во время выполнения.

Поскольку мы только что упомянули монеты, давайте использовать их в качестве примера, используя `match`! Можно написать функцию, которая возьмёт неизвестную монету Соединённых Штатов и по аналогии со счётной машиной определит, какая это монета и вернёт её значение в центах, как показано в листинге 6-3.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-03/src/main.rs:here}}
```

<span class="caption">Листинг 6-3: Перечисление и выражение <code>match</code>, которое использует варианты перечисления в качестве шаблонов</span>

Давайте разберём `match` в функции `value_in_cents`. Сначала пишется ключевое слово `match` за которым следует выражение, которое в данном случае является значением `coin`. Это выглядит очень похоже на выражение, используемое с `if`, но есть большая разница: с `if` выражение должно возвращать логическое значение, а здесь это может быть любой тип. Тип `coin` в этом примере - перечисление типа `Coin`, объявленное в строке 1.

Далее идут ветки `match`. Ветки состоят из двух частей: шаблон и некоторый код. Здесь первая ветка имеет шаблон, который является значением `Coin::Penny`, затем идёт оператор `=>`, который разделяет шаблон и код для выполнения. Код в этом случае - это просто значение `1`. Каждая ветка отделяется от следующего запятой.

Когда выполняется выражение `match`, оно сравнивает полученное значение с образцом каждой ветки по порядку. Если шаблон совпадает со значением, то выполняется код связанный с этим шаблоном. Если этот шаблон не соответствует значению, то выполнение продолжается со следующей ветки, так же как в автомате по сортировке монет. У нас может быть столько веток, сколько нужно: в листинге 6-3 наш `match` состоит из четырёх.

Код, связанный с каждой веткой, является выражением, а полученное значение выражения в соответствующей ветке - это значение, которое возвращается для всего выражения `match`.

Фигурные скобки обычно не используются, если код ветки короткий, как в листинге 6-3, где каждая ветка только возвращает значение. Если необходимо выполнить несколько строк кода в ветке, можно использовать фигурные скобки. Например, следующий код будет выводить «Lucky penny!» каждый раз, когда метод вызывается со значением `Coin::Penny`, но все равно возвращает последнее значение блока со значением `1`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-08-match-arm-multiple-lines/src/main.rs:here}}
```

### Шаблоны привязывающие значения

Есть ещё одно полезное качество у веток выражения <code>match</code>: они могут привязать части значений,  удовлетворяющих шаблону. Этим способом можно извлечь значение из вариантов перечисления.

В качестве примера, давайте изменим один из вариантов перечисления для хранения данных внутри него. С 1999 по 2008 год Соединённые Штаты чеканили 25 центов с различным дизайном на одной стороне для каждого из 50 штатов. Ни одна другая монета не получила дизайна штата, только четверть доллара имела эту дополнительную особенность. Мы можем добавить эту информацию в наш `enum` путём изменения варианта `Quarter` для включения внутрь значения `UsState`, как сделано в листинге 6-4.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-04/src/main.rs:here}}
```

<span class="caption">Листинг 6-4: Перечисление <code>Coin</code>, где вариант <code>Quarter</code> содержит также значение <code>UsState</code></span>

Давайте представим, что наш друг пытается собрать все 50 четвертных для всех штатов. В то время как мы сортируем мелочь по типу монеты, мы также будем печатать имя штата, связанное с каждой четвертной. Таким образом, если её нет у нашего друга, то её можно добавить его в коллекцию.

В выражении match для этого кода, мы добавляем переменную с именем `state` в шаблон, который соответствует значениям варианта `Coin::Quarter`. Когда `Coin::Quarter` совпадает, то переменная `state` будет привязана к значению штата четвертной. Затем мы можем использовать `state` в коде для этой ветки, вот так:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-09-variable-in-pattern/src/main.rs:here}}
```

Если бы мы сделаем вызов как `value_in_cents(Coin::Quarter(UsState::Alaska))`, то `coin` будет иметь значение `Coin::Quarter(UsState::Alaska)`. Когда мы сравниваем это значение с каждой из веток, ни одна из них не совпадёт пока мы не достигнем варианта `Coin::Quarter(state)`. В этой точке привязка для переменной `state` будет иметь значение из перечисления равное `UsState::Alaska`. Можно затем использовать эту привязку в выражении `println!`, получая внутреннее значение состояния из типа `Coin` варианта перечисления `Quarter`.

### Совпадение шаблона для `Option<T>`

В предыдущем разделе мы хотели получить внутреннее значение `T` для случая `Some` при использовании `Option<T>`; мы можем обработать тип `Option<T>` используя `match`, как уже делали с перечислением `Coin`! Вместо сравнения монет мы сравним варианты `Option<T>`, так что работа выражения `match` остаётся такой же.

Допустим, мы хотим написать функцию, которая принимает `Option<i32>` и если есть значение внутри, то добавляет 1 к существующему значению. Если значения нет, то функция должна возвращать значение `None` и не пытаться выполнить операции.

Такую функцию довольно легко написать благодаря выражению `match`, что будет выглядеть как в листинге 6-5.

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:here}}
```

<span class="caption">Листинг 6-5: Функция, которая использует выражение <code>match</code> с типом <code>Option<i32></code></span>

Давайте рассмотрим первое выполнение `plus_one` более подробно. Когда мы вызываем `plus_one(five)`, то переменная `x` в теле `plus_one` будет иметь значение `Some(5)`. Затем мы сравниваем её с каждой веткой match выражения.

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

Значение `Some(5)` не соответствует шаблону `None`, поэтому мы продолжаем со следующей ветки.

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:second_arm}}
```

Совпадает ли `Some(5)` с шаблоном `Some(i)`? Да, это так! У нас такой же вариант. Тогда переменная `i` привязывается к значению, содержащемуся внутри `Some`, поэтому `i` получает значение `5`. Затем выполняется код в данной ветке, поэтому мы добавляем 1 к значению `i` и создаём новое значение `Some` со значением `6` внутри.

Теперь давайте рассмотрим второй вызов `plus_one` в листинге 6-5, где `x` является `None`. Мы входим в выражение `match` и сравниваем с первым рукавом.

```rust,ignore
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/listing-06-05/src/main.rs:first_arm}}
```

Оно совпадает! Там нет значения для добавления к нему, поэтому программа останавливается и возвращает значение `None` с правой стороны `=>`. Так как первый рукав совпал, то никакие другие рукава не сравниваются.

Комбинирование `match` и перечислений полезно во многих ситуациях. Вы увидите этот шаблон в коде Rust `match` с перечислением, привязка переменной к данным внутри, а затем выполнение кода на его основе. Сначала это немного сложно, но как только вы привыкнете то захотите чтобы такая возможность была бы во всех языках. Это неизменно любимый пользователями приём.

### Совпадение по шаблону является полным

Есть ещё один аспект выражения `match`, который необходимо обсудить. Рассмотрим версию нашей функции `plus_one`, которая имеет ошибку и не будет компилироваться:

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/src/main.rs:here}}
```

Мы не обработали вариант `None`, поэтому этот код вызовет дефект в программе. К счастью, Rust знает и умеет ловить такой случай. Если мы попытаемся скомпилировать такой код, мы получим ошибку компиляции:

```console
{{#include ../listings/ch06-enums-and-pattern-matching/no-listing-10-non-exhaustive-match/output.txt}}
```

Rust знает, что мы не обработали все возможные случаи, и даже знает какие образцы мы забыли! Сравнение по шаблону в Rust является *полными и исчерпывающими* (exhaustive): мы должны обработать до конца все возможные варианты, чтобы код был корректным. Особенно в случае `Option<T>`, когда Rust не позволит нам забыть явно обработать случай `None` и защитит нас от предположения, что у нас есть значение, хотя мы могли бы иметь null, таким образом допуская ошибку на миллиард долларов, рассмотренную ранее.

### Заполнитель `_`

В Rust также есть шаблон, который можно использовать, когда не хочется перечислять все возможные значения. Например, `u8` может иметь допустимые значения от 0 до 255. Если мы только заботимся о значениях 1, 3, 5 и 7 и не хотим перечислять 0, 2, 4, 6, 8, 9 вплоть до 255, то к счастью нам это не нужно. Вместо этого можно использовать специальный шаблон `_`:

```rust
{{#rustdoc_include ../listings/ch06-enums-and-pattern-matching/no-listing-11-underscore-placeholder/src/main.rs:here}}
```

Шаблон с заполнителем `_` будет соответствовать любому значению. Поместив его после наших других веток, ветка с заполнителем `_` будет соответствовать всем возможным случаям, которые не были указаны ранее. Так как `()` это просто значение единичного типа, то в случае `_` ничего не произойдёт. В результате можно сказать, что мы хотим ничего не делать для всех возможных значений, которые мы не обработали в  списке перед `_` заполнителем.

Тем не менее, выражение `match` может быть несколько многословным в ситуации, в которой важен только <code>один</code> из случаев. Для этой ситуации Rust предоставляет `if let` выражение.

Подробнее о шаблонах и сопоставлении можно найти в [главе 18](ch18-00-patterns.html).
