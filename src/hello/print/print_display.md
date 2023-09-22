# За показване (Display)

`fmt::Debug` рядко показва данните по желания начин. Често е по-добре сами
да нагодим начина на показване. Осъществяването става така:
```rust
// Внасяме модула `fmt`, за да можем да го ползваме (`use`).
use std::fmt;

// Описваме структура, за която ще осъществим `fmt::Display`
// Това е разнородна поредица², именувана `Structure`,
// който съдържа един член³ от тип `i32`.
struct Structure(i32);

// За да използваме означението `{}`, трябва ръчно да осъществим `impl fmt::Display`
// за типa `for Structure`.
impl fmt::Display for Structure {
    // Този отличител изисква да се осъществи метода `fmt` с точно това описание⁴.
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Пишем първия член в подадения изходен поток `f`.
        // write! връща данни от тип `fmt::Result`,
        // който показва дали действието (писане) е успяло или не.
        // Обърнете внимание, че правописът⁵  на `write!`
        // е много подобен на `println!`.
        write!(f, "{}", self.0)
    }
}
```

`fmt::Display` може да е по-изчистен от `fmt::Debug`, но се появяват някои
затруднения за библиотеката `std`. Как би трябвало да се показват неопределени
типове? Например ако `std` осъществеше един единствен начин за показване за
всички `Vec<T>`, какъв щеше да е той? Би ли бил някой от тези двата?

* `Vec<path>`: `/:/etc:/home/username:/bin` (split on `:`)
* `Vec<number>`: `1,2,3` (split on `,`)

Не, защото няма съвършен начин за показване на всички типове и библиотеката
`std` не налага никакъв. `fmt::Display` не е осъществен за `Vec<T>` и за
никакви *обобщени съдържащи типове*[^containers]. В тези случаи (показване на
обобщени съдържащи типове) се налага да ползваме `fmt::Debug`.

Това не е пречка все пак, защото за всеки *съдържащ тип*, който *не е* обобщен,
можем да осъществим `fmt::Display`.

```rust,editable
use std::fmt; // Внасяме `fmt`.

// Структура, съдържаща две числа. Ще изведем `Debug`, за да сравним изгледа 
// с осъществения `Display`.
#[derive(Debug)]
struct MinMax(i64, i64);

// Осъществяваме `Display` за `MinMax`.
impl fmt::Display for MinMax {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Използваме `self.пореден_номер`, за да достъпим всички данни.
        write!(f, "({}, {})", self.0, self.1)
    }
}

// Описваме структура с именувани полета, за да я сравним.
#[derive(Debug)]
struct Point2D {
    x: f64,
    y: f64,
}

// По подобен начин осъществяваме `Display` за `Point2D`.
impl fmt::Display for Point2D {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Показваме само `x` and `y`.
        write!(f, "x: {}, y: {}", self.x, self.y)
    }
}

fn main() {
    let minmax = MinMax(0, 14);

    println!("Сравнение на показването на структури:");
    println!("Display: {}", minmax);
    println!("Debug: {:?}", minmax);

    let big_range =   MinMax(-300, 300);
    let small_range = MinMax(-3, 3);

    println!("Голямата поредица е {big}, а малката е {small}",
             small = small_range,
             big = big_range);

    let point = Point2D { x: 3.3, y: 7.2 };

    println!("Сравнение на показването на точки:");
    println!("Display: {}", point);
    println!("Debug: {:?}", point);

    // Грешка. `Debug` и `Display` са осъществени, но `{:b}`
    // изисква осъществяване на `fmt::Binary`. Следното няма да работи.
    // println!("Как изглежда Point2D, представен двоично: {:b}?", point);
}
```

Осъществихме `fmt::Display`, но не и `fmt::Binary`, и затова не можем да го ползваме.
`std::fmt`предоставя много такива [отличители][traits] и всеки трябва да бъде
осъществен поотделно. Това е описано подробно в [`std::fmt`][fmt].

### Упражнение

След като проверите изхода от горния пример, използвайте структурата `Point2D`
като ръководство, за да добавите нова структура – `Complex` към примера. При
отпечатване изходът трябва да изглежда така:

```txt
Display: 3.3 + 7.2i
Debug: Complex { real: 3.3, imag: 7.2 }
```
## Бел. прев.

[^containers]: обобщени съдържащи типове – generic containers

[^tuples]: разнородна поредица – tuple, вижте [разнородни поредици][tuples]

[^elements]: член – element

[^signature]: описание (на функция/метод) – signature

[^syntax]: правопис – syntax


### Вижте също:

[`derive`][derive], [`std::fmt`][fmt], [`macros`][macros], [`struct`][structs], [`trait`][traits] и [`use`][use]

[derive]: ../../trait/derive.md
[fmt]: https://doc.rust-lang.org/std/fmt/
[macros]: ../../macros.md
[structs]: ../../custom_types/structs.md
[traits]: https://doc.rust-lang.org/std/fmt/#formatting-traits
[use]: ../../mod/use.md
[tuples]: ../../primitives/tuples.md
