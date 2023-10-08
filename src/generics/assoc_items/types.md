# Придадени типове

Употребата на *придадени типове* подобрява цялостната четимост на кода, като
премества вътрешните типове в отделен отличител като *изходни типове*.
Правописът за описанието на отличителя е следният:

```rust
// `A` и `B` се описват в отличителя с помощта на ключовата дума `type`.
// (Важно: Този `type` тук не е същият като когато се ползва `type` за
// указване на прякор)
trait Contains {
    type A;
    type B;

    // Обновен правопис за достъп до тези типове като обобщения.
    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
}
```

Забележете, че функциите, използващи отличителя `Contains`, вече не трябва да
упоменават `A` или `B` изобщо:

```rust,ignore
// Без използване на придадени типове
fn difference<A, B, C>(container: &C) -> i32 where
    C: Contains<A, B> { ... }

// С използване на придадени типове
fn difference<C: Contains>(container: &C) -> i32 { ... }
```

Да пренапишем примера от предния раздел, като ползваме придадени типове:

```rust,editable
struct Container(i32, i32);

// Отличител, който проверява дали две единици се съхранвяват в друг съдържащ
// тип. Също извлича първата и последната стойност.
trait Contains {
    // Тук обявяваме обобщени типове, които методите ще могат да ползват.
    type A;
    type B;

    fn contains(&self, _: &Self::A, _: &Self::B) -> bool;
    fn first(&self) -> i32;
    fn last(&self) -> i32;
}

impl Contains for Container {
    // Определяме какви типове са `A` и `B`. Ако входният тип е
    // `Container(i32, i32)`, изходните типове са определени като
    // `i32` и `i32`.
    type A = i32;
    type B = i32;

    // `&Self::A` и `&Self::B` също може да се ползват тук.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }
    // Взима и връща първото число.
    fn first(&self) -> i32 { self.0 }

    // Взима и връща последното число.
    fn last(&self) -> i32 { self.1 }
}

fn difference<C: Contains>(container: &C) -> i32 {
    container.last() - container.first()
}

fn main() {
    let number_1 = 3;
    let number_2 = 10;

    let container = Container(number_1, number_2);

    println!(
        "Съдържа ли контейнерът {} и {}: {}",
        &number_1,
        &number_2,
        container.contains(&number_1, &number_2)
    );
    println!("Първо число: {}", container.first());
    println!("Последно: {}", container.last());

    println!("Разликата е: {}", difference(&container));
}
```
