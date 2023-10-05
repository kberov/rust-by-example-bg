# Премени за чужди функции 

Ръждьо предоставя възможност за описване на Премени за Чужди Функции (ВЧФ)[^FFI] за
библиотеки, написани на езика C. Чуждите функции трябва да се обявят в именуван
блок `extern`[^extern], означен с атрибут `#[link]`, който съдържа името на
чуждата библиотека.

```rust,ignore
use std::fmt;

// Този външен блок се свързва с библиотеката libm.
#[link(name = "m")]
extern {
    // Това е чужда функция, която изчислява корен квадратен от комплексно
    // число с единична точност.
    fn csqrtf(z: Complex) -> Complex;

    fn ccosf(z: Complex) -> Complex;
}

// Тъй като извикването на чужди функции се смята за опасно, обичайно се пишат
// обгръщащи функции, които извикват чуждите.
fn cos(z: Complex) -> Complex {
    unsafe { ccosf(z) }
}

fn main() {
    // z = -1 + 0i
    let z = Complex { re: -1., im: 0. };

    // Извикването на чужда функция е опасно действие.
    let z_sqrt = unsafe { csqrtf(z) };

    println!("Корен квадратен от {:?} е {:?}", z, z_sqrt);

    // Извикваме безопасно ППВ, обгръщащо опасно действие.
    println!("cos({:?}) = {:?}", z, cos(z));
}

// Най-малко въплъщение на комплексни числа с единична точност.
#[repr(C)]
#[derive(Clone, Copy)]
struct Complex {
    re: f32,
    im: f32,
}

impl fmt::Debug for Complex {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        if self.im < 0. {
            write!(f, "{}-{}i", self.re, -self.im)
        } else {
            write!(f, "{}+{}i", self.re, self.im)
        }
    }
}
```

## Б.пр.

[^FFI]: премяна за чужди функции (ПЧФ) – foreign function interface
  (FFI). приложна програмна премяна (ППП) – application programming
  interface (API)

[^extern]: външен – extern

