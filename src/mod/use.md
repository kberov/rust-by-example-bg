# Обявлението `use`

Обявлението `use` може да бъде използвано за обвързване на пълен път с ново име
за по-лесен достъп. Често се използва така:

```rust,editable,ignore
use crate::deeply::nested::{
    my_first_function,
    my_second_function,
    AndATraitType
};

fn main() {
    my_first_function();
}
```

Може да използвате ключовата дума `as`, за да обвържете *внасяния*[^imports] с
нови имена:

```rust,editable
// Обвързваме пътя `deeply::nested::function` с `other_function`.
use deeply::nested::function as other_function;

fn function() {
    println!("извикахме `function()`");
}

mod deeply {
    pub mod nested {
        pub fn function() {
            println!("извикахме `deeply::nested::function()`");
        }
    }
}

fn main() {
    // Улеснен достъп до `deeply::nested::function`
    other_function();

    println!("Влизане в блока");
    {
        // Това е същото като `use deeply::nested::function as function`.
        // Тази `function()` ще засенчи другата.
        use crate::deeply::nested::function;

        // Обвързвянията от вида `use` имат местен обхват. В този случай
        // засенчаването на `function()` е само в този блок.
        function();

        println!("Напускане на блока");
    }

    function();
}
```

## Б. пр.

[^imports]: внасяне – import. Внасяне (на имена (модули и функции)) в текущия обхват (код или файл).
