# Структури

Подобно, една `struct` може да се разложи така:

```rust,editable
fn main() {
    struct Foo {
        x: (u32, u32),
        y: u32,
    }

    // Променяйте стойностите в структурата, за да видите какво става
    let foo = Foo { x: (1, 2), y: 3 };

    match foo {
        Foo { x: (1, b), y } => println!("First of x is 1, b = {},  y = {} ", b, y),

        // Можете да разлагате структури и да преименувате променливи. Редът не е важен
        Foo { y: 2, x: i } => println!("y is 2, i = {:?}", i),

        // Също може да пропускате някои променливи:
        Foo { y, .. } => println!("y = {}, we don't care about x", y),
        // Това ще даде грешка: написаното не споменава полето `x`
        //Foo { y } => println!("y = {}", y),
    }

    let faa = Foo { x: (1, 2), y: 3 };

    //  Структура може да се разлага и без `match` блок:
    let Foo { x : x0, y: y0 } = faa;
    println!("Outside: x0 = {x0:?}, y0 = {y0}");
}
```

### Вижте също:

[Structs](../../../custom_types/structs.md)
