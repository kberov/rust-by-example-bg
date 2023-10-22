# `impl Черта`

`impl Черта` може да се ползва на две места:

1. като тип за аргумент
2. като тип за връщане

## Като тип за аргумент

Ако функцията ви е обобщена чрез някакакъв отличител, но нямате нищо против
уточняването на типа, може да опростите обявлението на функцията, като
използвате правописа `impl Черта` за тип на аргумента.

За пример да разгледаме следния код:

```rust,editable
fn parse_csv_document<R: std::io::BufRead>(src: R)
    -> std::io::Result<Vec<Vec<String>>> {
    src.lines()
        .map(|line| {
            // За всеки ред в източника
            line.map(|line| {
                // Ако редът е прочетен успешно, се обработва,
                // ако ли не – връща грешка
                line.split(',') // Цепи реда по запетайки
                    .map(|entry| String::from(entry.trim()))
                    // Маха водещи и следващи празноти
                    .collect() // Събира всички низове в реда във Vec<String>
            })
        })
        .collect() // Събира всички редове във Vec<Vec<String>>
}
```

`parse_csv_document` е обобщена и това ѝ позволява да приема всякакъв тип,
осъществяващ BufRead. Такива са `BufReader<File>` и `[u8]`. Не е важно какво
точно е типът `R`. `R` се ползва само за да бъде обявен типът на `src`, така че
функцията може да бъде написана и така:

```rust,editable
fn parse_csv_document(src: impl std::io::BufRead)
    -> std::io::Result<Vec<Vec<String>>> {
    src.lines()
        .map(|line| {
            // За всеки ред в източника
            line.map(|line| {
                // Ако редът е прочетен успешно, се обработва,
                // ако ли не – връща грешка
                line.split(',') // Цепи реда по запетайки
                    .map(|entry| String::from(entry.trim()))
                    // Маха водещи и следваши празноти
                    .collect() // Събира всички низове в реда във Vec<String>
            })
        })
        .collect() // Събира всички редове във Vec<Vec<String>>
}
```

Забележете, че използвайки `impl Черта` като тип за аргумент, не можете
изрично да кажете каква форма на функцията използвате. По-точно,
`parse_csv_document::<std::io::Empty>(std::io::empty())` няма да работи с
втория пример.


## Като тип за връщане

Ако вашата функция връща тип, който осъществява `MyTrait`, можете да опишете
типа за връщане така: `-> impl MyTrait`. Това може доста да опрости обявленията
на функциите ви.

```rust,editable
use std::iter;
use std::vec::IntoIter;

// Тази функция съчетава два вектора `Vec<i32>` и връща повторител за
// съчетанието. Вижте колко е сложен типът за връщане!
fn combine_vecs_explicit_return_type(
    v: Vec<i32>,
    u: Vec<i32>,
) -> iter::Cycle<iter::Chain<IntoIter<i32>, IntoIter<i32>>> {
    v.into_iter().chain(u.into_iter()).cycle()
}

// Това е същата функция, но типът ѝ за връщане ползва `impl Черта`.
// Вижте колко по-проста е!
fn combine_vecs(
    v: Vec<i32>,
    u: Vec<i32>,
) -> impl Iterator<Item=i32> {
    v.into_iter().chain(u.into_iter()).cycle()
}

fn main() {
    let v1 = vec![1, 2, 3];
    let v2 = vec![4, 5];
    let mut v3 = combine_vecs(v1, v2);
    assert_eq!(Some(1), v3.next());
    assert_eq!(Some(2), v3.next());
    assert_eq!(Some(3), v3.next());
    assert_eq!(Some(4), v3.next());
    assert_eq!(Some(5), v3.next());
    println!("all done");
}
```

Още повече, че някои типове в Ръждьо не могат да бъдат написани изрично.
Например, всяко затваряне има свой собствен безименен тип. Преди появата на
правописа `impl Черта` трябваше да се задели място на купа̀, за да се
върне затваряне. Но сега е възможно да се направи статично, ето така:
```rust,editable
// Връща функция, която добавя `y` към входа си
fn make_adder_function(y: i32) -> impl Fn(i32) -> i32 {
    let closure = move |x: i32| { x + y };
    closure
}

fn main() {
    let plus_one = make_adder_function(1);
    assert_eq!(plus_one(2), 3);
}
```

Можете също да пишете `impl Черта`, за да върнете повторител, който
ползва затварянията `map` или `filter`. Това прави употребата на `map` и
`filter` по-лесна. Понеже типовете на затварянията нямат имена, не можете да
напишете изрично името на типа за връщане, ако функцията ви връща повторител
със затваряне. Но с помощта на `impl Черта` това е лесно:

```rust,editable
fn double_positives<'a>(numbers: &'a Vec<i32>) -> impl Iterator<Item = i32> + 'a {
    numbers
        .iter()
        .filter(|x| x > &&0)
        .map(|x| x * 2)
}

fn main() {
    let singles = vec![-3, -2, 2, 3];
    let doubles = double_positives(&singles);
    assert_eq!(doubles.collect::<Vec<i32>>(), vec![4, 6]);
}
```
