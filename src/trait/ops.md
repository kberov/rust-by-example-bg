# Промяна на оператори 

В Ръждьо много от операторите могат да бъдат променяни чрез отличители. Някои
оператори може да се ползват за извършване на различни задачи в зависимост от
подадените им аргументи. Това е възможно, защото операторите са просто
краснопис за извикване на методи. Например `+` в израза `a + b` извиква метода
`add` (ей така `a.add(b)`). Този метод `add` е част от отличителя `Add`. Така
операторът `+` може да се ползва от всеки осъществител на отличтеля `Add`.

Списък с отличители като `Add`, които променят оператори, може да бъде намерен в
[`core::ops`][ops].

```rust,editable
use std::ops;

struct Foo;
struct Bar;

#[derive(Debug)]
struct FooBar;

#[derive(Debug)]
struct BarFoo;

// Отличителят `std::ops::Add` се използва за указване на функционалността на `+`.
// Тук създаваме `Add<Bar>` – отличител за добавяне с RHS от тип `Bar`.
// Следващият блок осъществява операцията: Foo + Bar = FooBar
impl ops::Add<Bar> for Foo {
    type Output = FooBar;

    fn add(self, _rhs: Bar) -> FooBar {
        println!("> Извикахме Foo.add(Bar)");

        FooBar
    }
}

// Като разменим типовете, в крайна сметка осъществяваме некомутативно добавяне.
// Тук създаваме `Add<Foo>` – отличител за добавяне с RHS от тип `Foo`.
// Този блок осъществява операцията: Bar + Foo = BarFoo
impl ops::Add<Foo> for Bar {
    type Output = BarFoo;

    fn add(self, _rhs: Foo) -> BarFoo {
        println!("> Извикахме Bar.add(Foo)");

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
