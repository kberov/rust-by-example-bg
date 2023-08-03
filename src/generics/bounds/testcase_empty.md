# Проверка: празни предели

Последствие от начина, по който работят пределите е, че дори даден отличител да
не включва никаква функционалност пак може да бъде използван като предел.`Eq` и
`Copy` са такива примери от стандартната библиотека.

> Така *отличителят* (`trait`) е просто *отличителна черта* (`trait`), по която
  *отличаваме* два иначе нямащи видимо нищо общо типа, и тази черта да бъде
  о-предел-ител за тези два типа (б.пр.).

```rust,editable
struct Cardinal;
struct BlueJay;
struct Turkey;

trait Red {}
trait Blue {}

impl Red for Cardinal {}
impl Blue for BlueJay {}

// Тези функции работят само за типове, осъществяващи тези отличители. Това,
// че отличителите са празни, няма никакво значение. 
fn red<T: Red>(_: &T)   -> &'static str { "red" }
fn blue<T: Blue>(_: &T) -> &'static str { "blue" }

fn main() {
    let cardinal = Cardinal;
    let blue_jay = BlueJay;
    let _turkey   = Turkey;

    // `red()` няма да работи за обект BlueJay заради пределите
    println!("A cardinal is {}", red(&cardinal));
    println!("A blue jay is {}", blue(&blue_jay));
    //println!("A turkey is {}", red(&_turkey));
    // ^ ЗАДАЧА: Разкоментирйте този ред.
}
```

### See also:

[`std::cmp::Eq`][eq], [`std::marker::Copy`][copy], and [`trait`s][traits]

[eq]: https://doc.rust-lang.org/std/cmp/trait.Eq.html
[copy]: https://doc.rust-lang.org/std/marker/trait.Copy.html
[traits]: ../../trait.md
