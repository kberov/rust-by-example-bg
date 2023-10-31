# Разопаковане на избори и стойности по подразбиране

Има няколко начина за разгъване на `Option` и връщане на стойност по
подразбиране, ако проверяваната стойност е `None`. За да изберем най-подходящия
начин, трябва да преценим следното:
* дали ни е нужно алчно или лениво изчисление на подадения запасен аргумент?
* дали трябва да запазим първоначалната празна стойност, или да я променим на място?

##  методът `or` – верижен, алчен, не пипа празната стойност

`pub fn or(self, optb: Option<T>) -> Option<T>` може да се изпълнява верижно
(няколко пъти последователно), а запасният му аргумент се изчислява алчно, както
се вижда от следващия пример. Забележете, че понеже аргументът бива оценен
алчно, стойността на подадената на `or` променлива бива преместена.

```rust,editable
#[derive(Debug)] 
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let apple = Some(Fruit::Apple);
    let orange = Some(Fruit::Orange);
    let no_fruit: Option<Fruit> = None;

    let first_available_fruit = no_fruit.or(orange).or(apple);
    println!("first_available_fruit: {:?}", first_available_fruit);
    // first_available_fruit: Some(Orange)

    // `or` премества аргумента си.
    // В горния пример `or(orange)` връща `Some`, така че `or(apple)` не се
    // извиква. Но въпреки това стойността на променливата `apple` бива
    // преместена и не може да се ползва повече.
    // println!("Променливата apple е преместена, така че този ред
    // няма да се компилира: {:?}", apple);
    // ЗАДАЧА: Разкоментирайте горния ред, за да видите грешката при компилиране.
 }
```

##  методът `or_else` – верижен, ленив, не пипа празната стойност

Друга възможност е да ползвате `pub fn or_else<F>(self, f: F) -> Option<T> where F: FnOnce() -> Option<T>,`.
Той също може да бъде извикан верижно, а подаденият му като запасен аргумент,
който е функция или затваряне, бива изчислен лениво. Вижте следния пример.

```rust,editable
#[derive(Debug)] 
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let apple = Some(Fruit::Apple);
    let no_fruit: Option<Fruit> = None;
    let get_kiwi_as_fallback = || {
        println!("Предоставяме киви в запас");
        Some(Fruit::Kiwi)
    };
    let get_lemon_as_fallback = || {
        println!("Предоставяме лимон в запас");
        Some(Fruit::Lemon)
    };

    let first_available_fruit = no_fruit
        .or_else(get_kiwi_as_fallback)
        .or_else(get_lemon_as_fallback);
    println!("first_available_fruit: {:?}", first_available_fruit);
    // Предоставяме киви в запас
    // first_available_fruit: Some(Kiwi)
}
```

##  методът `get_or_insert` – алчен, променя празната стойност на място

За да се подсигурим, че даден `Избор` съдържа стойност, можем да ползваме
`pub fn get_or_insert(&mut self, value: T) -> &mut T`, за да го променим на място,
като му дадем стойност по подразбиране. Забележете, че `get_or_insert`
изчислява запасния аргумент алчно, така че стойността на променливата `apple`
бива преместена:

```rust,editable
#[derive(Debug)]
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let mut моят_плод: Option<Fruit> = None;
    let apple = Fruit::Apple;
    let първият_наличен_плод = моят_плод.get_or_insert(apple);
    println!("първият_наличен_плод е: {:?}", първият_наличен_плод);
    println!("моят_плод е: {:?}", моят_плод);
    // първият_наличен_плод е: Apple
    // my_fruit е: Some(Apple)
    //println!("Стойността на `apple` е преместена: {:?}", apple);
    // ЗАДАЧА: разкоментирайте горния ред, за да видите грешката при
    // компилиране
}
```

## методът `get_or_insert_with` – ленив, променя празната стойност на място

Вместо изрично да подаваме стойност за връщане, можем да подадем затваряне на
`pub fn get_or_insert_with<F>(&mut self, f: F) -> &mut T where F: FnOnce() -> T,`,
както следва:

```rust,editable
#[derive(Debug)] 
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let mut my_fruit: Option<Fruit> = None;
    let get_lemon_as_fallback = || {
        println!("Предоставяме лимон в запас");
        Fruit::Lemon
    };
    let first_available_fruit = my_fruit
        .get_or_insert_with(get_lemon_as_fallback);
    println!("first_available_fruit е: {:?}", first_available_fruit);
    println!("my_fruit е: {:?}", my_fruit);
    // Предоставяме лимон в запас
    // first_available_fruit is: Lemon
    // my_fruit is: Some(Lemon)

    // Ако Option има стойност, тя не се променя и затварянето не се извиква
    let mut my_apple = Some(Fruit::Apple);
    let ше_да_е_ябълка = my_apple.get_or_insert_with(get_lemon_as_fallback);
    println!("ше_да_е_ябълка е: {:?}", ше_да_е_ябълка);
    println!("my_apple не е променена: {:?}", my_apple);
    // Изходът е, както следва. Забележете, че затварянето
    // `get_lemon_as_fallback` не е извикано.
    // ше_да_е_ябълка is: Apple
    // my_apple не е променена: Some(Apple)
}
```

### Вижте също:

[Затваряния][closures], [`get_or_insert`][get_or_insert], [`get_or_insert_with`][get_or_insert_with], [`moved variables`][moved], [`or`][or], [`or_else`][or_else]

[closures]: ../../fn/closures.md
[get_or_insert]: https://doc.rust-lang.org/core/option/enum.Option.html#method.get_or_insert
[get_or_insert_with]: https://doc.rust-lang.org/core/option/enum.Option.html#method.get_or_insert_with
[moved]: https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html
[or]: https://doc.rust-lang.org/core/option/enum.Option.html#method.or
[or_else]: https://doc.rust-lang.org/core/option/enum.Option.html#method.or_else
