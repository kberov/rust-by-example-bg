# Проверка: разясняване на единичния тип

Можем да разгледаме един полезен начин за превръщане на мерни единици[^unit],
като осъществим `Add` с помощта на параметър от призрачен тип. Отличителят
`Add` е разгледан по-долу:

```rust,ignore
// Този строеж предполага: `Self + RHS = Изход`, където RHS (ДС - Дясна
// страна) е по подразбиране Self, ако не е уточнена в осъществяването.
pub trait Add<RHS = Self> {
    type Изход;

    fn add(self, rhs: RHS) -> Self::Изход;
}

// `Изход` трябва да бъде `T<U>` така че `T<U> + T<U> = T<U>`.
impl<U> Add for T<U> {
    type Изход = T<U>;
    ...
}
```

Цялото – осъществено:

```rust,editable
use std::marker::PhantomData;
use std::ops::Add;

/// Създаваме празни броители, като единични типове.
#[derive(Debug, Clone, Copy)]
enum Инч {}
#[derive(Debug, Clone, Copy)]
enum Мм {}

/// `Дължина` е тип с параметър от призрачен тип `Единица`,
/// и не е обобщен през типа `Дължина` (сиреч `f64`).
///
/// `f64` вече осъществява отличителите `Clone` и `Copy`.
#[derive(Debug, Clone, Copy)]
struct Дължина<Единица>(f64, PhantomData<Единица>);

/// Отличителят `Add` описва поведението на оператора `+`.
impl<Единица> Add for Дължина<Единица> {
    type Output = Дължина<Единица>;

    // add() връща нова структура `Дължина`, съдържаща сбора.
    fn add(self, rhs: Дължина<Единица>) -> Дължина<Единица> {
        // `+` извиква осъществяването на отличителя `Add` за `f64`.
        Дължина(self.0 + rhs.0, PhantomData)
    }
}

fn main() {
    // Описва `one_foot` с призрачен тип `Инч`.
    let one_foot: Дължина<Инч> = Дължина(12.0, PhantomData);
    // `one_meter` има параметър от призрачен тип `Мм`.
    let one_meter: Дължина<Мм> = Дължина(1000.0, PhantomData);

    // `+` извиква метода `add()`, който осъществихме за `Дължина<Единица>`.
    //
    // Понеже `Дължина` осъществява `Copy`, `add()` не поглъща
    // `one_foot` и `one_meter`, а ги копира в `self` и `rhs`.
    let two_feet = one_foot + one_foot;
    let two_meters = one_meter + one_meter;

    // Събирането работи.
    println!("една стъпка + one_foot = {:?} ин", two_feet.0);
    println!("един метър + one_meter = {:?} мм", two_meters.0);

    // Безсмислените действия не минават, както и трябва:
    // Грешка по време на компилиране: несъответствие в типовете.
    // expected `Дължина<Инч>`, found `Дължина<Мм>`
    //let one_feter = one_foot + one_meter;
}
```
## Б.пр.

[^unit] Игра на думи: мерна единица, единичен тип – unit. Вижте [Unit_type в
 Wikipedia](https://en.wikipedia.org/wiki/Unit_type) 

### See also:

[Borrowing (`&`)], [Bounds (`X: Y`)], [enum], [impl & self],
[Overloading], [ref], [Traits (`X for Y`)] и [TupleStructs].

[Borrowing (`&`)]: ../../scope/borrow.md
[Bounds (`X: Y`)]: ../../generics/bounds.md
[enum]: ../../custom_types/enum.md
[impl & self]: ../../fn/methods.md
[Overloading]: ../../trait/ops.md
[ref]: ../../scope/borrow/ref.md
[Traits (`X for Y`)]: ../../trait.md
[TupleStructs]: ../../custom_types/structs.md
[std::marker::PhantomData]: https://doc.rust-lang.org/std/marker/struct.PhantomData.html
