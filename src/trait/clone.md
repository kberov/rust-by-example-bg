# Clone

Когато се обработват данни, поведението по подразбиране е да бъдат прехвърлени
(`move`) при присвояване или извикване на функции. Все пак понякога искаме да
копираме дaнните.

Отличителят [`Clone`][clone] ни помага точно за това. Обикновено ползваме
метода `.clone()`, описан в отличителя `Clone`.

```rust,editable
// Единична структура без данни
#[derive(Debug, Clone, Copy)]
struct Unit;

// Разнородна поредна структура с данни, осъществяваща отличителя `Clone`
#[derive(Clone, Debug)]
struct Pair(Box<i32>, Box<i32>);

fn main() {
    // Създаваме обект от тип `Unit`
    let unit = Unit;
    // Копираме `unit`, няма данни за местене.
    let copied_unit = unit;

    // И двата обекта `Unit` могат да се ползват независимо един от друг
    println!("original: {:?}", unit);
    println!("copy: {:?}", copied_unit);

    // Създаваме данни от тип `Pair`
    let pair = Pair(Box::new(1), Box::new(2));
    println!("original: {:?}", pair);

    // Местим `pair` в `moved_pair`, има преместване на данни.
    let moved_pair = pair;
    println!("moved: {:?}", moved_pair);

    // Грешка! `pair` е загубил данните си.
    //println!("original: {:?}", pair);
    // ЗАДАЧА ^ Разкоментирайте този ред.

    // Клонираме `moved_pair` в `cloned_pair` (включително с данните).
    let cloned_pair = moved_pair.clone();
    // Освобождаваме първоначалния чифт с помощта на `std::mem::drop`.
    drop(moved_pair);

    // Грешка! `moved_pair` вече е освободен.
    //println!("copy: {:?}", moved_pair);
    // ЗАДАЧА ^ Разкоментирайте този ред.

    // Резултатът от .clone() все още може да се ползва!
    println!("clone: {:?}", cloned_pair);
}
```

[clone]: https://doc.rust-lang.org/std/clone/trait.Clone.html
