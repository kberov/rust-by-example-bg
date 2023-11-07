# Други (потребителски) типове за ключ

Всеки тип, въплъщаващ отличителите `Eq` и `Hash`, може да бъде ключ в `HashMap`. 
Такива са:

* `bool` (макар и не много полезен, защото има само два възможни ключа);
* `int`, `uint` и всичките им разновидности;
* `String` и `&str` (за напреднали: можете да имате `HashMap` с ключове от тип
  `String` и да извиквате `.get()`, като му подавате `&str`).

Забележете, че `f32` и `f64` *не* осъществяват `Hash`, най-вероятно заради
[грешките][floating], свързани с определяне на точността след десетичната
запетая, които биха направили използването им като ключове ужасно ненадеждно.

Всички видове сбирки[^collection] осъществяват `Eq` и `Hash`, ако съдържаните
от тях типове въплъщават `Eq` и `Hash`. Например `Vec<T>` ще въплъщава `Hash`,
ако `T` въплъщава `Hash`.

Можем лесно да въплътим `Eq` и `Hash` за потрбителски тип с един ред само:
`#[derive(PartialEq, Eq, Hash)]`

Компилаторът ще свърши останалото. Ако искате да управлявате подробностите,
можете да осъществите `Eq` и/или `Hash` сами. Това ръководство не
покрива особеностите по осъществяване на `Hash`.

За да си поиграем с използване на структура като ключ в `HashMap`, нека
направим простичка уредба за удостоверяване на потребител:

```rust,editable
use std::collections::HashMap;

// Eq изисква вашият тип да е производен на PartialEq.
#[derive(PartialEq, Eq, Hash)]
struct Account<'a>{
    username: &'a str,
    password: &'a str,
}

struct AccountInfo<'a>{
    name: &'a str,
    email: &'a str,
}

type Accounts<'a> = HashMap<Account<'a>, AccountInfo<'a>>;

fn try_logon<'a>(accounts: &Accounts<'a>,
        username: &'a str, password: &'a str){
    println!("Username: {}", username);
    println!("Password: {}", password);
    println!("Attempting logon...");

    let logon = Account {
        username,
        password,
    };

    match accounts.get(&logon) {
        Some(account_info) => {
            println!("Успешно влизане!");
            println!("Име: {}", account_info.name);
            println!("Е-поща: {}", account_info.email);
        },
        _ => println!("Не успяхте да влезете!"),
    }
}

fn main(){
    let mut accounts: Accounts = HashMap::new();

    let account = Account {
        username: "j.everyman",
        password: "password123",
    };

    let account_info = AccountInfo {
        name: "John Everyman",
        email: "j.everyman@email.com",
    };

    accounts.insert(account, account_info);

    try_logon(&accounts, "j.everyman", "psasword123");

    try_logon(&accounts, "j.everyman", "password123");
}
```
## Б. пр.


[^collection]: сбирка, набор, колекция – collection

[hash]: https://en.wikipedia.org/wiki/Hash_function
[floating]: https://en.wikipedia.org/wiki/Floating_point#Accuracy_problems
