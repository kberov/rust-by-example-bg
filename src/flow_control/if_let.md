# if let

В някои случаи, когато сравняваме броячи, `match` е неудобен. Например:

```rust
// Създаваме променлива `optional` от тип `Option<i32>`
let optional = Some(7);

match optional {
    Some(i) => {
        println!("Това енаистина дълъг ред и `{:?}`", i);
        // ^ Трябват ни два отстъпа, само за да извадим `i` от брояча за избор.
    },
    _ => {},
    // ^ Нали `match` е изчерпателен. Изглежда само пилеем код.
};

```

`if let` е по-чист в този случай и също позволява различни възможности за
обработка на грешки:

```rust,editable
fn main() {
    // Всички са от тип `Option<i32>`
    let number = Some(7);
    let letter: Option<i32> = None;
    let emoticon: Option<i32> = None;

    // Конструкцията `if let` се чете така:
    // Ако `let` разложи `number` в `Some(i)`, изпълни този блок (`{}`).
    if let Some(i) = number {
        println!("Matched {:?}!", i);
    }

    // Ако трябва да укажете какво се случва иначе, използвайте `else`:
    if let Some(i) = letter {
        println!("Matched {:?}!", i);
    } else {
        // Разлагането не успя. Влизаме в случая за обработка на грешката.
        println!("Didn't match a number. Let's go with a letter!");
    }

    // Предоставяме друг вид неуспех.
    let i_like_letters = false;

    if let Some(i) = emoticon {
        println!("Matched {:?}!", i);
    // Разлагането не успя. Оценяваме условието `else if`, за да видим даля
    // влизаме в другото условие:
    } else if i_like_letters {
        println!("Не уцелихме число. Да пробваме с буква!");
    } else {
    // Условието бе оценено като false. Това е разклонението по
    // подразбиране:
        println!(" Не харесвам букви. Да ползваме емотикон :)!");
    }
}
```

По същия начин `if let` може да се ползва за сравнение нв всяка стойност на
брояч:

```rust,editable
// Примерен брояч
enum Foo {
    Bar,
    Baz,
    Qux(u32)
}

fn main() {
    // Създаваме примерни променливи
    let a = Foo::Bar;
    let b = Foo::Baz;
    let c = Foo::Qux(100);
    
    // Променливата a съвпада с Foo::Bar
    if let Foo::Bar = a {
        println!("a is foobar");
    }
    
    // Variable b does not match Foo::Bar
    // So this will print nothing
    if let Foo::Bar = b {
        println!("b is foobar");
    }
    
    // Променливата 'c' съвпада с Foo::Qux, който има стойност, подобна на Some()
    // от предишния пример
    if let Foo::Qux(value) = c {
        println!("c is {}", value);
    }

    // Обвързването чрез @ също работи с `if let`
    if let Foo::Qux(value @ 100) = c {
        println!("c is one hundred");
    }
}
```

Допълнителна полза е, че `if let` ни позволява да сравняваме непараметризирани
варианти на броячи. Това е вярно дори в случаите, когато броячът не осъществява
или не *извежда*[^derive] `PartialEq`. В тези случаи, дори ако `if Foo::Bar ==
a` не се компилира, защото вариантите на брояча не могат да бъдат сравнявани,
`if let` ще си работи.

Искате ли предизвикателство? Поправете следния пример като използвате `if let`:

```rust,editable,ignore,mdbook-runnable
// Този брояч нарочно нито осъществява нито извежда PartialEq.
// Ето защо  сравнението Foo::Bar == a дава грешка.
enum Foo {Bar}

fn main() {
    let a = Foo::Bar;

    // Променливата a съвпада с Foo::Bar
    if Foo::Bar == a {
    // ^-- това причинява грешка по време на компилация. Използвайте `if let`.
        println!("a is foobar");
    }
}
```

[^derive] извежда – [derive][derive]

### See also:

[`enum`][enum], [`Option`][option], and the [RFC][if_let_rfc]

[derive]: ../../trait/derive.md
[enum]: ../custom_types/enum.md
[if_let_rfc]: https://github.com/rust-lang/rfcs/pull/160
[option]: ../std/option.md
