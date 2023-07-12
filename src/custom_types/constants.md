# Константи

Ръждьо им два вида константи[^constants], които могат да бъдат обявени във
всеки обсег на видимост, включително глобален. И двата вида изискват изрично
указване на типа.

* `const`: Неменѝма стойност (общият случай).
* `static`: Възможно менѝма (`mut`) променлива със статична трайност[^lifetime] – [`'static`][static].
  Статичната трайност е отгатната[^inferred] и не е нужно да се указва изрично. Достъпването или промяната на менѝма статична променлива е *опасно*[^unsafe] ([`unsafe`][unsafe]).

```rust,editable,ignore,mdbook-runnable
// Глобалните се обявяват извън всички други области на видимост.
static LANGUAGE: &str = "Ръждьо";
const THRESHOLD: i32 = 10;

fn is_big(n: i32) -> bool {
    // Достъп до стойността на константа в някоя функция
    n > THRESHOLD
}

fn main() {
    let n = 16;

    // Достъп до константа в главната нишка
    println!("Това е {}", LANGUAGE);
    println!("Прагът е {}", THRESHOLD);
    println!("{} е {}", n, if is_big(n) { "голям" } else { "малък" });

    // Грешка! Не може да се променя `const`.
    THRESHOLD = 5;
    // ЗА ПОПРАВКА ^ Да се коментира този ред
}
```

### Вижте също:

[The `const`/`static` RFC](
https://github.com/rust-lang/rfcs/blob/master/text/0246-const-vs-static.md),
[`'static` lifetime][static]

[static]: ../scope/lifetime/static_lifetime.md
[unsafe]: ../unsafe.md

[^constants]: константи, непроменливи – constants

[^lifetime]: дълготрайност, трайност, живот, времетраене – lifetime (да се избере едно –
  най-удачното и да  се ползва само то – бел. прев.)

[^inferred]: отгатнат – inferred

[^unsafe]: опасно – unsafe
