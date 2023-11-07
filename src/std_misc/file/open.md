# `open`

Функцията `open` може да се ползва за отваряне на файл само за четене.

Един `File` владее източник – файловото описание, и се грижи да
затвори файла, когато той бъде освободен (`drop`).

```rust,editable,ignore
use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

fn main() {
    // Създаваме път към желания файл.
    let path = Path::new("hello.txt");
    let display = path.display();

    // Отваряме пътя само за четене. Това връща `io::Result<File>`.
    let mut file = match File::open(&path) {
        Err(why) => panic!("Неуспех при отваряне на {}: {}", display, why),
        Ok(file) => file,
    };

    // Прочитаме съдържанието на файла в низ, връща `io::Result<usize>`.
    let mut s = String::new();
    match file.read_to_string(&mut s) {
        Err(why) => panic!("Неуспех при четене на {}: {}", display, why),
        Ok(_) => print!("{} съдържа:\n{}", display, s),
    }

    // `file` излиза от обхват и "hello.txt" бива затворен
}
```

Ето очаквания успешен изход:

```shell
$ echo "Hello World!" > hello.txt
$ rustc open.rs && ./open
hello.txt contains:
Hello World!
```

(Пробвайте горния пример при различни условия за неуспех: `hello.txt` не
съществува или `hello.txt` е нечетим и т.н.)
