# Броячи

Ключовата дума `enum`[^enum] позволява създаването на тип, който може да
представлява един от няколко различни варианта. Всеки вариант, който може да
бъде действителна[^valid] структура, може да бъде действителен и в брояч
(`enum`).

```rust,editable
// Създаваме `enum`, за да определим някакво събитие в уеб.
// Забележете как името и типът на данните заедно определят варианта:
// `PageLoad != PageUnload` и `KeyPress(char) != Paste(String)`.
// Всеки вариант е различен и независим.
enum WebEvent {
    // Всеки вариант на `enum` може да бъде:
    // празна единица `unit`;
    PageLoad,
    PageUnload,
    // списъчна структура `tuple`;
    KeyPress(char),
    Paste(String),
    // или C-образна стурктура (като тези в езика C).
    Click { x: i64, y: i64 },
}

// Функция, която приема брояч `WebEvent` като аргумент
// и не връща нищо.
fn inspect(event: WebEvent) {
    match event {
        WebEvent::PageLoad => println!("заредихте страницата"),
        WebEvent::PageUnload => println!("напуснахте страницата"),
        // Разлагаме (изваждаме) `c` от варианта на `enum`.
        WebEvent::KeyPress(c) => println!("натиснахте '{}'.", c),
        WebEvent::Paste(s) => println!("поставихте \"{}\".", s),
        // Разлагаме `Click` на `x` и `y`.
        WebEvent::Click { x, y } => {
            println!("натиснахте място с координати x={}, y={}.", x, y);
        },
    }
}

fn main() {
    let pressed = WebEvent::KeyPress('x');
    // `to_owned()` създава *притежаван* `String` от низов отрязък.
    let pasted  = WebEvent::Paste("my text".to_owned());
    let click   = WebEvent::Click { x: 20, y: 80 };
    let load    = WebEvent::PageLoad;
    let unload  = WebEvent::PageUnload;

    inspect(pressed);
    inspect(pasted);
    inspect(click);
    inspect(load);
    inspect(unload);
}

```

## Прякори на типове

Ако ползвате прякор[^alias] на тип, можете да се обръщате към всеки вариант на
брояча чрез неговия прякор. Това може да се окаже полезно, ако името на брояча
е твърде дълго или твърде общо и искате да го промените.

```rust,editable
enum VeryVerboseEnumOfThingsToDoWithNumbers {
    Add,
    Subtract,
}

// Създаваме прякор на типа
type Operations = VeryVerboseEnumOfThingsToDoWithNumbers;

fn main() {
    // Можем да достъпваме всеки вариант на брояча чрез прякора му.
    // По-кратко и удобно е.
    let x = Operations::Add;
}
```

Най-често ще виждате използването на прякори в блоковете за осъществяване
`impl`, където се ползва прякорът `Self`.

```rust,editable
enum VeryVerboseEnumOfThingsToDoWithNumbers {
    Add,
    Subtract,
}

impl VeryVerboseEnumOfThingsToDoWithNumbers {
    fn run(&self, x: i32, y: i32) -> i32 {
        match self {
            Self::Add => x + y,
            Self::Subtract => x - y,
        }
    }
}
```

За да научите повече за броячите и типовите прякори, може да прочетете [отчета
за стабилизация][aliasreport]. Оттогава е стабилна тази способност[^feature] на Ръждьо.

### Вижте също:

[`match`][match], [`fn`][fn] и [`String`][str], ["Type alias enum variants" RFC][type_alias_rfc]

[^enum]: брояч – enum. От enumerator – буквално „изброител” (бел. прев.)  

[^valid]: действителен - valid (бел. прев.)

[^feature]: способност, възможност – feature (бел. прев.)


[c_struct]: https://en.wikipedia.org/wiki/Struct_(C_programming_language)
[match]: ../flow_control/match.md
[fn]: ../fn.md
[str]: ../std/str.md
[aliasreport]: https://github.com/rust-lang/rust/pull/61682/#issuecomment-502472847
[type_alias_rfc]: https://rust-lang.github.io/rfcs/2338-type-alias-enum-variants.html
