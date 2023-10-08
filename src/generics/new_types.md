# Изразът за нов тип

*Изразът за нов тип*[^newtype] дава гаранция по време на компилация, че на програмата е
подаден правилният тип за стойност.

Да речем, че имаме функция за проверка на възраст, която проверява възрастта
в години. На нея **трябва** да ѝ се подаде стойност от тип `Years`. 

```rust, editable
struct Years(i64);

struct Days(i64);

impl Years {
    pub fn to_days(&self) -> Days {
        Days(self.0 * 365)
    }
}


impl Days {
    /// изрязва непълни години
    pub fn to_years(&self) -> Years {
        Years(self.0 / 365)
    }
}

fn old_enough(age: &Years) -> bool {
    age.0 >= 18
}

fn main() {
    let age = Years(5);
    let age_days = age.to_days();
    println!("Old enough {}", old_enough(&age));
    println!("Old enough {}", old_enough(&age_days.to_years()));
    // println!("Old enough {}", old_enough(&age_days));
}
```

Разкоментирайте последното изявлениe `println!`, за да видите, че
предоставеният тип трябва да бъде `Years`.

За да получите стойността на „новия тип” като основен тип, може да ползвате
разнородна поредица или разлагане по следния начин:
```rust, editable
struct Years(i64);

fn main() {
    let years = Years(42);
    let years_as_primitive_1: i64 = years.0; // Разнородно
    let Years(years_as_primitive_2) = years; // Разлагане
}
```

## Б.пр.

[^newtype]: Изразът за нов тип – New Type Idiom/Pattern

### Вижте също:

[`structs`][struct],
["…a Generic Array using Newtype Pattern"](https://rsdlt.github.io/posts/rust-use-newtype-pattern-display-trait-array-generics/)

[struct]: ../custom_types/structs.md
