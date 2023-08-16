# `abort` и `unwind`

Предният раздел показа `panic`. Различните пътища, по които поема кода, могат
да бъдат компилирани условно в ависимост от настройката за `panic`. Сега
възможните стойности са "разгъване" (`unwind`) и „преждевременно прекратяване”
(`abort`). 


Ако разработим предния пример с лимонадата, можем изрично да ползваме
стратегията на паникьосване, за да насочим програмата по различен път.  

```rust,editable,mdbook-runnable

fn drink(beverage: &str) {
   // You shouldn't drink too much sugary beverages.
    if beverage == "lemonade" {
        if cfg!(panic="abort"){ println!("This is not your party. Run!!!!");}
        else{ println!("Spit it out!!!!");}
    }
    else{ println!("Some refreshing {} is all I need.", beverage); }
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

Ето друг пример, който пренаписва `drink()` и използва изрично ключовата дума `unwind`.

```rust,editable

#[cfg(panic = "unwind")]
fn ah(){ println!("Spit it out!!!!");}

#[cfg(not(panic="unwind"))]
fn ah(){ println!("This is not your party. Run!!!!");}

fn drink(beverage: &str){
    if beverage == "lemonade"{ ah();}
    else{println!("Some refreshing {} is all I need.", beverage);}
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

Стратегията за паникьосване може да бъде зададена на командния ред, като ползваме `abort` или `unwind`.

```console
rustc  lemonade.rs -C panic=abort
```

