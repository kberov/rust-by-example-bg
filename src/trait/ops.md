# Промяна на оператори 

В Ръждьо много от операторите могат да бъдат променяни чрез отличители. Някои
оператори може да се ползват за извършване на различни задачи в зависимост от
подадените им аргументи. Това е възможно, защото операторите са просто
краснопис за извикване на методи. Например `+` в израза `a + b` извиква метода
`add` (ей така `a.add(b)`). този метод `add` е част от отличителя `Add`. Така
операторът `+` може да се ползва от всеки осъществител на отличтеля `Add`.

Списък с отличители като `Add`, които променят оператори може да бъде намерен в
[`core::ops`][ops].

```rust,editable
use std::ops;

struct Foo;
struct Bar;

#[derive(Debug)]
struct FooBar;

#[derive(Debug)]
struct BarFoo;

// The `std::ops::Add` trait is used to specify the functionality of `+`.
// Here, we make `Add<Bar>` - the trait for addition with a RHS of type `Bar`.
// The following block implements the operation: Foo + Bar = FooBar
impl ops::Add<Bar> for Foo {
    type Output = FooBar;

    fn add(self, _rhs: Bar) -> FooBar {
        println!("> Foo.add(Bar) was called");

        FooBar
    }
}

// By reversing the types, we end up implementing non-commutative addition.
// Here, we make `Add<Foo>` - the trait for addition with a RHS of type `Foo`.
// This block implements the operation: Bar + Foo = BarFoo
impl ops::Add<Foo> for Bar {
    type Output = BarFoo;

    fn add(self, _rhs: Foo) -> BarFoo {
        println!("> Bar.add(Foo) was called");

        BarFoo
    }
}

fn main() {
    println!("Foo + Bar = {:?}", Foo + Bar);
    println!("Bar + Foo = {:?}", Bar + Foo);
}
```

### See Also

[Add][add], [Syntax Index][syntax]

[add]: https://doc.rust-lang.org/core/ops/trait.Add.html
[ops]: https://doc.rust-lang.org/core/ops/
[syntax]:https://doc.rust-lang.org/book/appendix-02-operators.html
