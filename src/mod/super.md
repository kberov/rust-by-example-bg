# `super` и `self`

Ключовите думи `super` и `self` могат да се ползват в пътя, за да се премахнат
неясноти при достъпа до единици от даден модул и да се предотврати ненужно
точно изписване на пътища.

```rust,editable
fn function() {
    println!("извикахме `function()`");
}

mod cool {
    pub fn function() {
        println!("извикахме `cool::function()`");
    }
}

mod my {
    fn function() {
        println!("извикахме `my::function()`");
    }
    
    mod cool {
        pub fn function() {
            println!("извикахме `my::cool::function()`");
        }
    }
    
    pub fn indirect_call() {
        // Да достъпим всички функции от този обхват с име `function`!
        print!("извикахме `my::indirect_call()`, that\n> ");
        
        // Ключовата дума `self` се отнася до текущия модул – в този случай `my`.
        // Извикването `self::function()` и извикването `function()` са едно и
        // също, защото се отнасят до една и съща функция.
        self::function();
        function();
        
        // Можем да ползваме `self` и за да достъпим друг модул в `my`:
        self::cool::function();
        
        // Ключовата дума `super` се отнася до родителския обхват
        // (извън модула `my`).
        super::function();
        
        // Това ще създаде прякор root_function за `cool::function` в обхвата
        // на целия *кош*. В този случай обхватът на коша е най-външният обхват.
        {
            use crate::cool::function as root_function;
            root_function();
        }
    }
}

fn main() {
    my::indirect_call();
}
```
