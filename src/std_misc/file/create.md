# `create`

Функцията `create` отваря файл само за писане. Ако файлът вече съществува,
старото му съдържание бива унищожено. Иначе се създава нов файл.

```rust,ignore
static LOREM_IPSUM: &str =
    "Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
";

use std::fs::File;
use std::io::prelude::*;
use std::path::Path;

fn main() {
    let path = Path::new("lorem_ipsum.txt");
    let display = path.display();

    // Отваряме файл само за писане. Връща `io::Result<File>`.
    let mut file = match File::create(&path) {
        Err(why) => panic!("Неуспех при създаване на {}: {}", display, why),
        Ok(file) => file,
    };

    // Пишем низа от `LOREM_IPSUM` във `file`. Връща `io::Result<()>`.
    match file.write_all(LOREM_IPSUM.as_bytes()) {
        Err(why) => panic!("Неуспех при писане във {}: {}", display, why),
        Ok(_) => println!("Успешно записахме в {}", display),
    }
}
```

Ето очаквания успешен изход:

```shell
$ rustc create.rs && ./create
successfully wrote to lorem_ipsum.txt
$ cat lorem_ipsum.txt
Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse
cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non
proident, sunt in culpa qui officia deserunt mollit anim id est laborum.
```

(Както и в предния пример, пробвайте различни условия за неуспех.)

Може да ползваме структурата [`OpenOptions`], за да настроим как да се отваря файл.

[`OpenOptions`]: https://doc.rust-lang.org/std/fs/struct.OpenOptions.html
