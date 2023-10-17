# Предели

Когато работим с обобщения, често се налага параметрите за типове да ползват
*предели* (bounds), за да *о-пределят* каква функционалност осъществява даденият
тип. Следващият пример използва отличителя `Display` за извеждане на стойност
на екрана и е задължително `T` да бъде ограничен (о-пределен) от `Display`;
сиреч, `T` *трябва* да осъществи `Display`.

```rust,ignore
// Обявяваме функция `printer`, която приема обобщен тип `T`, който трябва да е
// осъществил отличителя `Display`.
fn printer<T: Display>(t: T) {
    println!("{}", t);
}
```

Пределите ограничават обобщението до типове, които отговарят на ограниченията,
иначе казано, които са в пределите на нужната функционалност. Ето:

```rust,ignore
struct S<T: Display>(T);

// Грешка! `Vec<T>` не осъществява `Display`. Това уточнение ще се провали.
let s = S(vec![1]);
```

Друго последствие от о-пределянето е, че обобщените обекти могат да извикват
[методите][methods] на отличителите, упоменати в пределите. Например:

```rust,editable
// Отличител, който осъществява означението: `{:?}`.
use std::fmt::Debug;

trait HasArea {
    fn area(&self) -> f64;
}

impl HasArea for Rectangle {
    fn area(&self) -> f64 { self.length * self.height }
}

#[derive(Debug)]
struct Rectangle { length: f64, height: f64 }
#[allow(dead_code)]
struct Triangle  { length: f64, height: f64 }

// Обобщеният тип `T` трябва да осъществява `Debug`. Независимо какъв е типът,
// следният код ще работи.
fn print_debug<T: Debug>(t: &T) {
    println!("{:?}", t);
}

// `T` трябва да осъществява `HasArea`. Всеки тип, който е в пределите, може да
// извика функцията на `HasArea` `area`.
fn area<T: HasArea>(t: &T) -> f64 { t.area() }

fn main() {
    let rectangle = Rectangle { length: 3.0, height: 4.0 };
    let _triangle = Triangle  { length: 3.0, height: 4.0 };

    print_debug(&rectangle);
    println!("Area: {}", area(&rectangle));

    //print_debug(&_triangle);
    //println!("Area: {}", area(&_triangle));
    // ^ ЗАДАЧА: Разкоментирайте горните редове.
    // | Грешка: Не осъществява ни `Debug`, ни `HasArea`. 
}
```

Забележете, че в някои случаи за по-голяма изразителност или четимост могат да
се изполват условия [`where`][where] за налагане на предели.

### Вижте също:

[`std::fmt`][fmt], [`struct`s][structs] и [`отличители`][traits]

[fmt]: ../hello/print.md
[methods]: ../fn/methods.md
[structs]: ../custom_types/structs.md
[traits]: ../trait.md
[where]: ../generics/where.md
