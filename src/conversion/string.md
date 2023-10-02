# Към и от низове

## Превръщане към String

За да превърнем от всякакъв тип към низ (`String`), трябва просто да осъществим
отличителя [`ToString`] за дадения тип. Вместо да го правим наистина, по-добре
да осъществим [`fmt::Display`][Display], който автоматично предоставя
[`ToString`] и позволява отпечатване на типа, както обсъдихме вече в раздела за
[`print!`][print].

```rust,editable
use std::fmt;

struct Circle {
    radius: i32
}

impl fmt::Display for Circle {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "Circle of radius {}", self.radius)
    }
}

fn main() {
    let circle = Circle { radius: 6 };
    println!("{}", circle.to_string());
}
```

## Разбор на String

Един от най-често срещаните случаи на превръщане е този на низ към число.
Обичайният[^idiomatic] за Ръждьо подход тук е да ползвате функцията [`parse`] и
или да улесните отгатването на типа, или да укажете типа за извличане
посредством правописа[^syntax] „турбориба”[^turbofish]. И двата начина са
показани в следващия пример.

Това ще превърне низа в указания тип, доколкото е осъществен отличителят
[`FromStr`] за този тип. Много типове в стандартната библиотека осъществяват
този отличител. За да имаме това действие за потребителски тип, просто
осъществяваме [`FromStr`] за типа.

```rust,editable
fn main() {
    let parsed: i32 = "5".parse().unwrap();
    let turbo_parsed = "10".parse::<i32>().unwrap();

    let sum = parsed + turbo_parsed;
    println!("Sum: {:?}", sum);
}
```
## Б.пр.

разбор – parsing

правя разбор на нещо – parse

[^idiomatic]: обичаен, собствено присъщ, идиоматичен – idiomatic. Свойствен (в случая – свойствен на
  Ръждьо)  

[^syntax]: правопис, синтаксис – syntax

[^turbofish]: турбориба – turbofish. Изписване `име_на_метод::<Тип>()`; **::<Тип>** (напомня на риба).

[`ToString`]: https://doc.rust-lang.org/std/string/trait.ToString.html
[Display]: https://doc.rust-lang.org/std/fmt/trait.Display.html
[print]: ../hello/print.md
[`parse`]: https://doc.rust-lang.org/std/primitive.str.html#method.parse
[`FromStr`]: https://doc.rust-lang.org/std/str/trait.FromStr.html
