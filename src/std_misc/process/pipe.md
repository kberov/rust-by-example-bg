# Тръби

Структурата `std::Child` представлява работещ дъщерен процес и предлага
ръкохватките[^handle] `stdin`, `stdout` и `stderr` за взаимодействие със самия процес
през тръби[^pipes].

```rust,ignore
use std::io::prelude::*;
use std::process::{Command, Stdio};

static PANGRAM: &'static str =
"the quick brown fox jumped over the lazy dog\n";

fn main() {
    // Изпълняваме командата `wc`
    let process = match Command::new("wc")
                                .stdin(Stdio::piped())
                                .stdout(Stdio::piped())
                                .spawn() {
        Err(why) => panic!("Неуспех при изпълнение на `wc`: {}", why),
        Ok(process) => process,
    };

    // Пишем низ в стандартния входен поток (`stdin`) на `wc`.
    //
    // `stdin` е от тип `Option<ChildStdin>`, но понеже знаем, че това пускане
    // има такъв, можем направо да го разгърнем (`unwrap`).
    match process.stdin.unwrap().write_all(PANGRAM.as_bytes()) {
        Err(why) => panic!("Неуспех при писане в \
            стандартния входен поток на `wc`: {}", why),
        Ok(_) => println!("Изпратихме всички-букви на `wc`"),
    }

    // `stdin` не живее след горните извиквания и затова бива освободен, а
    // тръбата е затворена.
    //
    // Това е много важно. Иначе `wc` няма да започне да обработва входа, който
    // току-що ѝ изпратихме.

    // Полето `stdout` също е от тип `Option<ChildStdout>` и трябва да бъде разгърнато.
    let mut s = String::new();
    match process.stdout.unwrap().read_to_string(&mut s) {
        Err(why) => panic!("Неуспех при четенето \
            на стандартния изход от `wc`: {}", why),
        Ok(_) => print!("`wc` отговори с:\n{}", s),
    }
}
```

## Б. пр.

[^handle]: ръкохватка – handle

[^pipes] тръба – pipe; тръбопровод – pipeline (Виж https://en.wikipedia.org/wiki/Pipeline_(Unix))
