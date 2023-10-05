# Отличители

Разбира се, отличителите също могат да бъдат обобщени. Тук описваме един, който
осъществява наново отличителя `Drop` като обобщен метод на метода `drop` и
като входен параметър.

```rust,editable
// Некопируеми типове.
struct Empty;
struct Null;

// Обобщен отличител за `T`.
trait DoubleDrop<T> {
    // Обявяваме метод за извикващия тип, който приема допълнителен единичен
    // параметър `T` и не прави нищо с него.
    fn double_drop(self, _: T);
}

// Осъществяваме `DoubleDrop<T>` за всеки обобщен параметър `T` и извикващ `U`.
impl<T, U> DoubleDrop<T> for U {
    // Този метод овладява и двата подадени аргумента, като така освобождава
    // паметта и за двата.
    fn double_drop(self, _: T) {}
}

fn main() {
    let empty = Empty;
    let null  = Null;

    // Освобождаваме `empty` и `null`.
    empty.double_drop(null);

    //empty;
    //null;
    // ^ ЗАДАЧА: Разкоментирйте двата реда.
}
```

### See also:

[`Drop`][Drop], [`struct`][structs] и [`trait`][traits]

[Drop]: https://doc.rust-lang.org/std/ops/trait.Drop.html
[structs]: ../custom_types/structs.md
[traits]: ../trait.md
