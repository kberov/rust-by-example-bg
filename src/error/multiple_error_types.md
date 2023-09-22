# Множество типове грешки 

Предишните примери бяха все много удобни.
Типовете `Result` си взаимодействаха с други `Result`, а типовете `Option` – с
други `Option`.

Понякога се налага `Option` да взаимодейства с `Result` или `Result<T, Error1>`
да взаимодейства с `Result<T, Error2>`. В тези случаи искаме да управляваме
различните типове грешки така, че да са съставими и лесни  за взаимодействие.

В следващия код две извиквания на `unwrap` създават различни типове грешки.
`Vec::first` връща `Option`, докато `parse::<i32>` връща стойост от тип
`Result<i32, ParseIntError>`:

```rust,editable,ignore,mdbook-runnable
fn double_first(vec: Vec<&str>) -> i32 {
    let first = vec.first().unwrap(); // Generate error 1
    2 * first.parse::<i32>().unwrap() // Generate error 2
}

fn main() {
    let numbers = vec!["42", "93", "18"];
    let empty = vec![];
    let strings = vec!["tofu", "93", "18"];

    println!("The first doubled is {}", double_first(numbers));

    println!("The first doubled is {}", double_first(empty));
    // Грешка 1: входният вектор е празен

    println!("The first doubled is {}", double_first(strings));
    // Грешка 2: от първата стойност не може да се извлече число
}
```

В следващите раздели ще видим  няколко стратегии за справяне с тези проблеми.
