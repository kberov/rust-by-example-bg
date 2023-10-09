# Разопаковане на избори и стойности по подразбиране

Има няколко начина за разгъване на `Option` и връщане на стойност по
подразбиране, ако проверяваната стойност е `None`. За да изберем най-подходящия
начин, трябва да преценим следното:
* дали искаме алчно или лениво изчисление?
* дали трябва да запазим първоначалната празна стойност или да я променим на място?

##  `or()` може да се използва верижно, изчислява алчно, не пипа празната стойност

`or()` може да се изпълнява верижно и изчислява аргументите си алчно, както се
вижда от следващия пример. Забележете, че понеже аргументите се оценяват алчно,
стойностите на подадените на `or` аругменти биват преместени.

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
    // извиква. Но въпреки това стойностт на променливата `apple` бива
    // преместена и не може да се ползва повече.
    // println!("Variable apple was moved, so this line won't compile: {:?}", apple);
    // ЗАДАЧА: Разкоментирайте горния ред, за да видите грешката при компилиране.
 }
```

##  `or_else()` може да се извиква верижно, изчислява лениво, не пипа празната стойност

Друга възможност е да ползвате `or_else`. Тя също се извиква верижно и оценява
аргументите си лениво. Вижте следния пример.

```rust,editable
#[derive(Debug)] 
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let apple = Some(Fruit::Apple);
    let no_fruit: Option<Fruit> = None;
    let get_kiwi_as_fallback = || {
        println!("Providing kiwi as fallback");
        Some(Fruit::Kiwi)
    };
    let get_lemon_as_fallback = || {
        println!("Providing lemon as fallback");
        Some(Fruit::Lemon)
    };

    let first_available_fruit = no_fruit
        .or_else(get_kiwi_as_fallback)
        .or_else(get_lemon_as_fallback);
    println!("first_available_fruit: {:?}", first_available_fruit);
    // Предоставяме киви като последна възможност
    // first_available_fruit: Some(Kiwi)
}
```

##  `get_or_insert()` Изчислява аргументите си алчно, променя празната стойност на място

За да се подсигурим, че даден `Избор` съдържа стойност, можем да ползваме
`get_or_insert`, за да го променим на място, като му дадем стойност по
подразбиране. Забележете, че `get_or_insert` изчислява параметъра си алчно,
така че стойността на променливата `apple` е преместена:

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

##  `get_or_insert_with()` изчислява аргументите си лениво, променя празната стойност на място

Вместо изрично да подаваме стойност за връщане, можем да подадем затваряне на
`get_or_insert_with`, както следва:

```rust,editable
#[derive(Debug)] 
enum Fruit { Apple, Orange, Banana, Kiwi, Lemon }

fn main() {
    let mut my_fruit: Option<Fruit> = None;
    let get_lemon_as_fallback = || {
        println!("Предоставям лимон за всеки случай");
        Fruit::Lemon
    };
    let first_available_fruit = my_fruit
        .get_or_insert_with(get_lemon_as_fallback);
    println!("first_available_fruit is: {:?}", first_available_fruit);
    println!("my_fruit е: {:?}", my_fruit);
    // Предоставяме лимон в запас
    // first_available_fruit is: Lemon
    // my_fruit is: Some(Lemon)

    // Ако Option има стойност, тя не се променя и зтварянето не се извиква
    let mut my_apple = Some(Fruit::Apple);
    let should_be_apple = my_apple.get_or_insert_with(get_lemon_as_fallback);
    println!("should_be_apple is: {:?}", should_be_apple);
    println!("my_apple is unchanged: {:?}", my_apple);
    // Изходът е както следва. Забележете, че зтварянето
    // `get_lemon_as_fallback` не е извикано.
    // should_be_apple is: Apple
    // my_apple is unchanged: Some(Apple)
}
```

### Вижте също:

[`closures`][closures], [`get_or_insert`][get_or_insert], [`get_or_insert_with`][get_or_insert_with], ,[`moved variables`][moved], [`or`][or], [`or_else`][or_else]

[closures]: https://doc.rust-lang.org/book/ch13-01-closures.html
[get_or_insert]: https://doc.rust-lang.org/core/option/enum.Option.html#method.get_or_insert
[get_or_insert_with]: https://doc.rust-lang.org/core/option/enum.Option.html#method.get_or_insert_with
[moved]: https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html
[or]: https://doc.rust-lang.org/core/option/enum.Option.html#method.or
[or_else]: https://doc.rust-lang.org/core/option/enum.Option.html#method.or_else
