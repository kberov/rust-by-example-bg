# Отличители

Отбелязването на живот в методи на отличители е като при функциите.
Забележете, че за `impl` също може да се указва живот.

```rust,editable
// Структура с отбелязан живот за нея и неин член.
#[derive(Debug)]
struct Borrowed<'a> {
    x: &'a i32,
}

// Отбелязваме живот за `impl`.
impl<'a> Default for Borrowed<'a> {
    fn default() -> Self {
        Self {
            x: &10,
        }
    }
}

fn main() {
    let b: Borrowed = Default::default();
    println!("b is {:?}", b);
}
```

### Вижте също:

[Отличители][trait]


[trait]: ../../trait.md
