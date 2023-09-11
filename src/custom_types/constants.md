# Константи

Ръждьо има два вида константи[^constants], които могат да бъдат обявени във
всеки обхват на видимост, включително глобален. И двата вида изискват изрично
указване на типа.

* `const`: Неменѝма стойност (общият случай).
* `static`: Възможно менѝма (`mut`) променлива със статичен живот[^lifetime] –
  [`'static`][static]. Статичният живот е отгатнат[^inferred] и не е нужно да
  се указва изрично. Достъпването или изменението на менѝма статична променлива е
  *опасно*[^unsafe] ([`unsafe`][unsafe]).

```rust,editable,ignore,mdbook-runnable
// Глобалните се обявяват извън всички други обхвати на видимост.
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

    // Грешка! Не може да се изменя `const`.
    THRESHOLD = 5;
    // ПОПРАВИ ^ Да се коментира този ред
}
```

### Вижте също:

[RFC за `const`/`static`](
https://github.com/rust-lang/rfcs/blob/master/text/0246-const-vs-static.md),
[`'static` lifetime][static]

[static]: ../scope/lifetime/static_lifetime.md
[unsafe]: ../unsafe.md

## Б.пр.

[^constants]: константи, непроменливи – constants

[^lifetime]: живот (на променливите) – lifetime

[^inferred]: отгатнат – inferred

[^unsafe]: опасно – unsafe
