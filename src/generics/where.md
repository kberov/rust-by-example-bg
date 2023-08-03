# Условия „където” (`where`) 

Предел може да бъде описан също чрез условие `where`[^where_clause] преди отварящата фигурна скоба `{`, а не само при първото споменаване на тип. Освен това условията  `where` могат да налагат предели на всякакви типове, не само на параметри за типове.

Ето някои случаи, при които условието `where` е полезно:

* Когато се указват обобщени типове и предели поотделно, така е по-ясно:

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

// Понеже иначе трябва да изразим това като напишем `T: Debug` или да ползваме
// някакъв друг заобиколен начин, се налага да ползваме условие `where`: 
impl<T> PrintInOption for T where
    Option<T>: Debug {
    // Искаме `Option<T>: Debug` да бъде нашия предел, защото това отпечатваме
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

[RFC][where], [`struct`][struct], and [`trait`][trait]

[struct]: ../custom_types/structs.md
[trait]: ../trait.md
[where]: https://github.com/rust-lang/rfcs/blob/master/text/0135-where.md
