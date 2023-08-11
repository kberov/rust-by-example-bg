# Производни

Компилаторът може да предоставя просто осъществяване за някои отличители чрез
[атрибута][attribute] `#[derive]`. Тези отличители пак могат да бъдат
осъществени, ако е необходимо по-сложно поведение.

Ето списък с отличители, от които можем да създаваме [производни][^derive]:
* Отличители за сравнение:
  [`Eq`][eq], [`PartialEq`][partial-eq], [`Ord`][ord], [`PartialOrd`][partial-ord].
* [`Clone`][clone], за създаване на `T` от `&T` чрез копиране.
* [`Copy`][copy], да се направи даден тип "копируем" вместо "преместваем".
* [`Hash`][hash], за изчисляване на хеш-сума от `&T`.
* [`Default`][default], за създаване на празен обект от даден тип данни.
* [`Debug`][debug], за форматиране на стойност чрез обозначението `{:?}`.
 
```rust,editable
// `Centimeters` – разнороден списък, който може да бъде сравняван с друг
#[derive(PartialEq, PartialOrd)]
struct Centimeters(f64);

// `Inches` – разнороден списък, който може да бъде отпечатван
#[derive(Debug)]
struct Inches(i32);

impl Inches {
    fn to_centimeters(&self) -> Centimeters {
        let &Inches(inches) = self;

        Centimeters(inches as f64 * 2.54)
    }
}

// `Seconds` – разнороден списък без допълнителни атрибути
struct Seconds(i32);

fn main() {
    let _one_second = Seconds(1);

    // Грешка: `Seconds` не може да се печати; не осъществява отличителя `Debug`
    //println!("One second looks like: {:?}", _one_second);
    // ЗАДАЧА ^ Разкоментирайте този ред

    // Грешка: `Seconds` не може да бъде сравняван; Не съществява отличителя `PartialEq`
    //let _this_is_true = (_one_second == _one_second);
    // ЗАДАЧА ^ Разкоментирайте този ред

    let foot = Inches(12);

    println!("One foot equals {:?}", foot);

    let meter = Centimeters(100.0);

    let cmp =
        if foot.to_centimeters() < meter {
            "smaller"
        } else {
            "bigger"
        };

    println!("One foot is {} than one meter.", cmp);
}

```
Б.пр.

[^derive]: производни – типове, които наследяват способности от отличители използвайки атрибута `#[derive]`. 

### See also:
[`derive`][derive]

[attribute]: ../attribute.md
[eq]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
[partial-eq]: https://doc.rust-lang.org/std/cmp/trait.PartialEq.html
[ord]: https://doc.rust-lang.org/std/cmp/trait.Ord.html
[partial-ord]: https://doc.rust-lang.org/std/cmp/trait.PartialOrd.html
[clone]: https://doc.rust-lang.org/std/clone/trait.Clone.html
[copy]: https://doc.rust-lang.org/core/marker/trait.Copy.html
[hash]: https://doc.rust-lang.org/std/hash/trait.Hash.html
[default]: https://doc.rust-lang.org/std/default/trait.Default.html
[debug]: https://doc.rust-lang.org/std/fmt/trait.Debug.html
[derive]: https://doc.rust-lang.org/reference/attributes.html#derive
