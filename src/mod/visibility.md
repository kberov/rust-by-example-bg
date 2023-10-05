# Видимост

По подразбиране единиците в един модул имат частна видмост, но това може да се
промени чрез използване на променителя `pub`. Само общите единици (обозначени с
`pub`) могат да се достъпят отвън – код, който е извън обхвата на модула.

```rust,editable
// Модул с име `my_mod`
mod my_mod {
    // Единиците в модулите имат частна видимост по подразбиране
    // (не са видими отвън).
    fn private_function() {
        println!("извикахме `my_mod::private_function()`");
    }

    // Използваме променителя `pub` да отменим подразбираната видимост.
    pub fn function() {
        println!("извикахме `my_mod::function()`");
    }

    // Единиците могат да достъпват други единици в същия модул, дори да са частни.
    pub fn indirect_access() {
        print!("извикахме `my_mod::indirect_access()`, която извиква\n> ");
        private_function();
    }

    // Модулите могат също да бъдат вложени
    pub mod nested {
        pub fn function() {
            println!("called `my_mod::nested::function()`");
        }

        #[allow(dead_code)]
        fn private_function() {
            println!("извикахме `my_mod::nested::private_function()`");
        }

        // Функции, обявени чрез изписването `pub(in път)`, са видими само в
        // подадения път. `път` трябва да бъде родителски или прародителски модул
        pub(in crate::my_mod) fn public_function_in_my_mod() {
            print!("извикахме `my_mod::nested::public_function_in_my_mod()`, \
            която извика\n> ");
            public_function_in_nested();
        }

        // Функции, обявени чрез синтаксиса `pub(self)`, са видими само
        // в текущия модул, което е същото като да ги оставим частни.
        pub(self) fn public_function_in_nested() {
            println!("извикахме `my_mod::nested::public_function_in_nested()`");
        }

        // Функции, обявени чрез синтаксиса `pub(super)`,
        //са видими само в родителския модул.
        pub(super) fn public_function_in_super_mod() {
            println!("извикахме `my_mod::nested::public_function_in_super_mod()`");
        }
    }

    pub fn call_public_function_in_my_mod() {
        print!("извикахме `my_mod::call_public_function_in_my_mod()`, \
        която извиква\n> ");
        nested::public_function_in_my_mod();
        print!("> ");
        nested::public_function_in_super_mod();
    }

    // pub(crate) прави функциите видими само в текущия кош.
    pub(crate) fn public_function_in_crate() {
        println!("извикахме `my_mod::public_function_in_crate()`");
    }

    // Вложените модули следват същите правила за видимост.
    mod private_nested {
        #[allow(dead_code)]
        pub fn function() {
            println!("извикавхме `my_mod::private_nested::function()`");
        }

        // Частните родителски единици продължават да ограничават видимостта на
        // дъщерните единици, дори да са обявени като видими в по-широк обхват.
        #[allow(dead_code)]
        pub(crate) fn restricted_function() {
            println!("извикахме \
            `my_mod::private_nested::restricted_function()`");
        }
    }
}

fn function() {
    println!("извикахме `function()`");
}

fn main() {
    // Модулите ни позволяват да различаваме единици с еднакви имена.
    function();
    my_mod::function();

    // Общите единици, включително тези от вложените модули, могат да се
    // достъпят извън родителския модул.
    my_mod::indirect_access();
    my_mod::nested::function();
    my_mod::call_public_function_in_my_mod();

    // Единиците, обявени чрез `pub(crate)`, могат да се извикат отвсякъде
    // в същия кош.
    my_mod::public_function_in_crate();

    // Единиците, обявени чрез `pub(in path)`, могат да се извикват
    // само в указания модул
    // Грешка! Функцията `public_function_in_my_mod` е частна
    //my_mod::nested::public_function_in_my_mod();
    // ЗАДАЧА ^ Разкоментирйте този ред

    // Частните единици за даден модул не могат да се достъпят пряко,
    // дори да са вложени в общ модул:
    // Грешка! `private_function` е частна
    //my_mod::private_function();
    // ЗАДАЧА ^ Разкоментирйте този ред

    // Грешка! `private_function` е частна
    //my_mod::nested::private_function();
    // ЗАДАЧА ^ Разкоментирйте този ред

    // Грешка! `private_nested` е частен модул
    //my_mod::private_nested::function();
    // ЗАДАЧА ^ Разкоментирйте този ред

    // Грешка! `private_nested` е частен модул
    //my_mod::private_nested::restricted_function();
    // ЗАДАЧА ^ Разкоментирйте този ред
}
```

