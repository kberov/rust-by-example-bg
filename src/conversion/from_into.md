# `From` и `Into`

Отличителите [`From`] и [`Into`] са същностно свързани и тази връзка е част от
начина, по който са осъществени. Ако можете да превърнете тип А в тип Б, то
би трябвало лесно да превърнете и тип Б в А.

## `From` – От

Отличителят [`From`] дава възможност даден тип да опише как да създаде себе си
от друг тип. Така имаме прост начин за превръщане между няколко различни типа.
Има множество осъществявания на този отличител в стандартната библиотека за
превръщане от един към друг на първични и често използвани типове.

Ето как лесно да превърнем `str` в `String`

```rust
let my_str = "hello";
let my_string = String::from(my_str);
```

Може да опишем наше собствено превръщане за наш си тип.

```rust,editable
use std::convert::From;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl From<i32> for Number {
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let num = Number::from(30);
    println!("My number is {:?}", num);
}
```

## `Into` – Къмто/Във

Отличителят [`Into`] е просто обратното на отличителя  `From`. Това означава,
че ако сте осъществили `From` за вашия тип, `Into` ще извика вашето
осъществяване, когато е нужно. 

Когато използваме отличителя `Into` обикновено се налага да  указваме типа към
който превръщаме, тъй като компилаторът често не може да го определи. Но това е нищо,
имайки предвидвид какво получаваме безплатно.

```rust,editable
use std::convert::Into;

#[derive(Debug)]
struct Number {
    value: i32,
}

impl Into<Number> for i32 {
    fn into(self) -> Number {
        Number { value: self }
    }
}

fn main() {
    let int = 5;
    // Try removing the type annotation
    let num: Number = int.into();
    println!("My number is {:?}", num);
}
```

[`From`]: https://doc.rust-lang.org/std/convert/trait.From.html
[`Into`]: https://doc.rust-lang.org/std/convert/trait.Into.html
