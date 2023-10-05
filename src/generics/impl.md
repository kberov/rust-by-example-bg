# Осъществяване

Подобно на функциите осъществяванията изискват внимание, за да останат обобщени.

```rust
struct S; // Точен тип`S`
struct GenericVal<T>(T); // Обобщен тип `GenericVal`

// Осъществяваме GenericVal, където изрично указваме типовите параметри:
impl GenericVal<f32> {} // Указваме `f32`
impl GenericVal<S> {} // Указваме `S`, описан по-горе

// `<T>` Трябва да предхожда типа, за да остане обобщен GenericVal
impl<T> GenericVal<T> {}
```

```rust,editable
struct Val {
    val: f64,
}

struct GenVal<T> {
    gen_val: T,
}

// Осъществяваме Val
impl Val {
    fn value(&self) -> &f64 {
        &self.val
    }
}

// Осъществяваме GenVal за обобщен тип `T`
impl<T> GenVal<T> {
    fn value(&self) -> &T {
        &self.gen_val
    }
}

fn main() {
    let x = Val { val: 3.0 };
    let y = GenVal { gen_val: 3i32 };

    println!("{}, {}", x.value(), y.value());
}
```

### See also:

[функции, връщащи препратки][fn], [`impl`][methods] и [`struct`][structs]


[fn]: ../scope/lifetime/fn.md
[methods]: ../fn/methods.md
[specialization_plans]: https://blog.rust-lang.org/2015/05/11/traits.html#the-future
[structs]: ../custom_types/structs.md
