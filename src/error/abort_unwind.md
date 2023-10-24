# `abort` и `unwind`

Предният раздел показа механизма за обработка на грешки `panic`.
Различните пътища, по които поема кодът, могат да бъдат компилирани условно
в зависимост от настройката за `panic`. Текущите налични стойности са
„размотаване“ (`unwind`) и „преждевременно прекратяване” (`abort`). 


Ако разработим предния пример с лимонадата, можем изрично да ползваме
стратегията за паникьосване, за да насочим програмата по различен път.  

```rust,editable,mdbook-runnable

fn drink(beverage: &str) {
   // Не бива да пиете твърде много сладки напитки.
    if beverage == "lemonade" {
        if cfg!(panic="abort"){ println!("Това не е вашият купон. Беж!!!!");}
        else{ println!("Изплюйте!!!!");}
    }
    else{ println!("Просто ми трябва нещо освежаващо като {}.", beverage); }
}

fn main() {
    drink("water");
    drink("lemonade");
}
```

Ето друг пример, който пренаписва `drink()` и използва изрично ключовата дума `unwind`.

```rust,editable

#[cfg(panic = "unwind")]
fn ah(){ println!("Изплюйте!!!!");}

#[cfg(not(panic="unwind"))]
fn ah(){ println!("Това не е вашият купон. Беж!!!!");}

fn drink(beverage: &str){
    if beverage == "lemonade"{ ah();}
    else{println!("Просто ми трябва нещо освежаващо като {}.", beverage);}
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
    
