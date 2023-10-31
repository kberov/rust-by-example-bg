# Съчетатели: `map`

`match` върши работа за обработка на избори (`Option`). Ако обаче го ползвате много,
може да ви се види досадно, особено за действия, които работят само
с определени входни данни. В тези случаи можем да ползваме
[_съчетатели_][combinators][^combinators] за управление на програмния поток
като отделни части.

`Option` има вграден метод, наречен `map()` (съпоставяне, сравнение), който е
съчетател за простите сравнения `Some -> Some` и `None -> None`. Повече
извиквания на `map()` може да се наредят верижно, така че изходът от първия да
извиква следващия, за постигане на повече гъвкавост.

В следния пример функцията `process()` в много сбит вид замества всички функции,
използвани преди това.
 
```rust,editable
#![allow(dead_code)]

#[derive(Debug)] enum Food { Apple, Carrot, Potato }

#[derive(Debug)] struct Peeled(Food);
#[derive(Debug)] struct Chopped(Food);
#[derive(Debug)] struct Cooked(Food);

// Белим нещо за ядене. Ако няма нищо за белене, връщаме `None`.
// Иначе връщаме обеленото.
fn peel(food: Option<Food>) -> Option<Peeled> {
    match food {
        Some(food) => Some(Peeled(food)),
        None       => None,
    }
}

// Нарязваме. Ако няма какво, връщаме `None`.
// Иначе връщаме нарязаното.
fn chop(peeled: Option<Peeled>) -> Option<Chopped> {
    match peeled {
        Some(Peeled(food)) => Some(Chopped(food)),
        None               => None,
    }
}

// Готвим. Тук показваме как вместо `match` за обработка на
// различните случаи се ползва `map()`.
fn cook(chopped: Option<Chopped>) -> Option<Cooked> {
    chopped.map(|Chopped(food)| Cooked(food))
}

// Една функция за белене, рязане и готвене – всичко това последователно.
// Навръзваме няколко извиквания на `map()`, за да опростим кода.
fn process(food: Option<Food>) -> Option<Cooked> {
    food.map(|f| Peeled(f))
        .map(|Peeled(f)| Chopped(f))
        .map(|Chopped(f)| Cooked(f))
}

// Проверяваме има ли храна, преди да се опитаме да ядем!
fn eat(food: Option<Cooked>) {
    match food {
        Some(food) => println!("Мммм… Обичам {:?}!", food),
        None       => println!("О, не! Не става за ядене."),
    }
}

fn main() {
    let apple = Some(Food::Apple);
    let carrot = Some(Food::Carrot);
    let potato = None;

    let cooked_apple = cook(chop(peel(apple)));
    let cooked_carrot = cook(chop(peel(carrot)));
    // Сега да опитаме по-простичко изглеждащата `process()`.
    let cooked_potato = process(potato);

    eat(cooked_apple);
    eat(cooked_carrot);
    eat(cooked_potato);
}
```
## Б. пр.

[^combinators]: съчетатели, съединители – combinators. Съчетателите са функции
  от по-високо ниво, които прилагат функции и по-рано описани съчетатели, за да
  произведат някакъв изход от подадените им данни. Те може да се ползват за
  управление на програмния поток като отделни части.

### Вижте също:

[затваряния][closures], [`Option`][option], [`Option::map()`][map]

[combinators]: https://doc.rust-lang.org/reference/glossary.html#combinator
[closures]: ../../fn/closures.md
[option]: https://doc.rust-lang.org/std/option/enum.Option.html
[map]: https://doc.rust-lang.org/std/option/enum.Option.html#method.map
