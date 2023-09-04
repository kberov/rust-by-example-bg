# Нишки

Ръждьо дава възможност за _разделяне_[^spawn] на програмите в _самородни_[^native]
за _работната уредба_[^OS] _нишки_[^threads] чрез функцията `spawn`. Аргументът
към тази функция е преместващо [данните] затваряне.

```rust,editable
use std::thread;

const NTHREADS: u32 = 10;

// Това е главната (`main`) нишка.
fn main() {
    // Създаваме вектор, в който да слагаме създадените дъщерни нишки.
    let mut children = vec![];

    for i in 0..NTHREADS {
        // Създаваме друга нишка.
        children.push(thread::spawn(move || {
            println!("this is thread number {}", i);
        }));
    }

    for child in children {
        // Чакаме нишката да приключи. Връща стойност, обгърната в Resul.
        let _ = child.join();
    }
}
```

 Тези нишки _биват насрочени_[^scheduled] за изпълнение от РУ.

## Б.пр.

[^spawn]: разделям в, пораждам, размножавам, създавам (нишка) – spawn

[^native]: самороден, туземец, местен жител – native

[^OS]: работна уредба (РУ) – operating system (OS)

[^threads]: нишки – threads

[^scheduled]: биват насрочени – being scheduled
