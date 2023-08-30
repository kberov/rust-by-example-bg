# `read_lines`

## Наивен подход

Това може да бъде приличен първи опит за четене на редове от файл.

```rust,norun
use std::fs::read_to_string;

fn read_lines(filename: &str) -> Vec<String> {
    let mut result = Vec::new();

    for line in read_to_string(filename).unwrap().lines() {
        result.push(line.to_string())
    }

    result
}
```

Тъй като методът `lines()` връща повторител върху редовете във файла, можем
също да направим съответствия и да съберем резултатите, като така представим
нещата като по-сбит и ясен израз.

```rust,norun
use std::fs::read_to_string;

fn read_lines(filename: &str) -> Vec<String> {
    read_to_string(filename) 
        .unwrap() // Паника в случай на грешки при четене от файл.
        .lines() // Разделяме низа в повторител върху низови отрязъци.
        .map(String::from) // Правим всеки отрязък на низ.
        .collect() // Събираме ги заедно във вектор.
}
```

Забележете, че и в двата примера се налага да превърнем препратката `&str`,
върната от `lines()` във владимия тип `String` с помощтта на `.to_string()` и
съответно `String::from`.

## По-ефикасен подход

Тук предаваме владението на отворения `File` на структура от тип `BufReader`.
`BufReader` използва вътрешен _склад_[^buffer], за да намалим
междинното[^intermediate] заделяне на памет[^allocations].

Също така обновяваме[^update] `read_lines` да връща повторител вместо да заделя
памет за нови обекти от тип `String` за всеки ред.

```rust,no_run
use std::fs::File;
use std::io::{self, BufRead};
use std::path::Path;

fn main() {
    // Файлът hosts.txt трябва да съществува в текущия път.
    if let Ok(lines) = read_lines("./hosts.txt") {
        // Поглъща повторителя. Връща Option<String>.
        for line in lines {
            if let Ok(ip) = line {
                println!("{}", ip);
            }
        }
    }
}

// Изходът е обгърнат в `Result`, за да разреши намиране на съвпадения при
// грешки. Връща повторител(`Iterator`) от `BufReader` на редовете във файла.
fn read_lines<P>(filename: P) -> io::Result<io::Lines<io::BufReader<File>>>
where P: AsRef<Path>, {
    let file = File::open(filename)?;
    Ok(io::BufReader::new(file).lines())
}
```

Running this program simply prints the lines individually.
```shell
$ echo -e "127.0.0.1\n192.168.0.1\n" > hosts.txt
$ rustc read_lines.rs && ./read_lines
127.0.0.1
192.168.0.1
```

(Note that since `File::open` expects a generic `AsRef<Path>` as argument, we define our
generic `read_lines()` method with the same generic constraint, using the `where` keyword.)

This process is more efficient than creating a `String` in memory with all of the file's
contents. This can especially cause performance issues when working with larger files.

## Б.пр.

[^buffer]: склад – buffer

[^intermediate]: междинно – intermediate

[^allocations]: заделяне на памет – allocation

[^update]: обновявам – update
