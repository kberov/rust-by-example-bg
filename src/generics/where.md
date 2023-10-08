# Условия „където” (`where`) 

Предел може да бъде описан също и чрез условие `where`[^where_clause] непосредствено преди отварящата фигурна скоба `{`, а не само при първото споменаване на типа. Освен това условията `where` могат да налагат предели на произволни типове, а не само на параметри за типове.

Ето някои случаи, при които условието `where` е полезно:

* Когато обобщени типове и предели се указват поотделно, така кодът е по-ясен:

```rust,ignore
impl <A: TraitB + TraitC, D: TraitE + TraitF> MyTrait<A, D> for YourType {}

// Изразяване на предели чрез условие `where`
impl <A, D> MyTrait<A, D> for YourType where
    A: TraitB + TraitC,
    D: TraitE + TraitF {}
```

* Условието `where` е по-изразително, сравнено с обичайния правопис. В този
  пример блокът за осъществяване (`impl`) не може да бъде изразен направо без
  условие `where`:

```rust,editable
use std::fmt::Debug;

trait PrintInOption {
    fn print_in_option(self);
}

// Тъй като в противен случай би трябвало да изразим това като `T: Debug`
// или да използваме друг непряк подход, това изисква условие `where`: 
impl<T> PrintInOption for T where
    Option<T>: Debug {
    // Искаме `Option<T>: Debug` да бъде нашият предел, защото това отпечатваме
    // Иначе ще се окаже, че ползваме грешен предел.
    fn print_in_option(self) {
        println!("{:?}", Some(self));
    }
}

fn main() {
    let vec = vec![1, 2, 3];

    vec.print_in_option();
}
```

[^where_clause]  условие „където” – `where` clause (б.пр.)

### See also:

[RFC][where], [Структури][struct] и [Отличители][trait]

[struct]: ../custom_types/structs.md
[trait]: ../trait.md
[where]: https://github.com/rust-lang/rfcs/blob/master/text/0135-where.md
