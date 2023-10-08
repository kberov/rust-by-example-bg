# Проблемът

Отличител, който е обобщен през своя съдържащ тип, изисква указване на типа –
потребителите на отличителя **трябва** да уточнят всичките му обобщени типове.

В примера долу отличителят `Contains` позволява да се използват обобщените
типове `A` и `B`. След това отличителят бива осъществен за типа `Container`,
като се указва `i32` за `A` и `B`, така че да може да се ползва с `fn
difference()`.

Понеже `Contains` е обобщен, ни се налага изрично да посочим *всичките*
обобщени типове за `fn difference()`. Всъщност ни е нужен някакъв начин да
кажем, че `A` и `B` са предопределени от *входния* тип `C`. Както ще видите в
следващия раздел, _придадените типове_ предоставят точно тази възможност.

```rust,editable
struct Container(i32, i32);

// Отличител, който проверява дали две единици се съхранвяват в друг съдържащ
// тип. Също извлича първата и последната стойност.
trait Contains<A, B> {
    fn contains(&self, _: &A, _: &B) -> bool; // Изисква изрично `A` и `B`.
    fn first(&self) -> i32; // Не изисква изрично `A` или `B`.
    fn last(&self) -> i32;  // Не изисква изрично `A` или `B`.
}

impl Contains<i32, i32> for Container {
    // Истина, ако съхрняваните числа са равни на подадените.
    fn contains(&self, number_1: &i32, number_2: &i32) -> bool {
        (&self.0 == number_1) && (&self.1 == number_2)
    }

    // Взима и връща първото число.
    fn first(&self) -> i32 {
        self.0
    }

    // Взима и връща последното число.
    fn last(&self) -> i32 {
        self.1
    }
}

// `C` съдържа `A` и `B`. В този смисъл е досадно да трябва да изразяваме `A` и
// `B` отново.
fn difference<A, B, C>(container: &C) -> i32
where
    C: Contains<A, B>,
{
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

### See also:

[`struct`][structs] и [`отличители`][traits]

[structs]: ../../custom_types/structs.md
[traits]: ../../trait.md
