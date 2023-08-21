#  Низове

В Ръждьо има два типа низове: `String` и `&str`.

Типът (`String`) се съхранява като вектор от байтове (`Vec<u8>`), но е
гарантирано, че винаги ще бъде действителна UTF-8 последователност. Низовете се
съхраняват в купа, могат да растат и не завършват с празен байт (\0).

Типът `&str` е отрязък (`&[u8]`), който винаги сочи към действителна UTF-8
последователност, и може да се ползва за „поглеждане (четене)” в стойност от тип
`String`, точно както `&[T]` е изглед[^view] във `Vec<T>`.

```rust,editable
fn main() {
    // ( всички отбелязвания на типове са излишни)
    // Препратка към низ, поместен в статичната памет (само за четене)
    let pangram: &'static str = "Хм, чужд щит, бърз гьон плюс яйце в шкаф.";
    println!("Панграм: {}", pangram);

    // Обхождаме думите наобратно, не се създава нов низ в паметта
    println!("Думите наобратно");
    for word in pangram.split_whitespace().rev() {
        println!("> {}", word);
    }

    // Копираме знаци във вектор, подреждаме по азбучен ред и премахваме
    // повтарящите се
    let mut chars: Vec<char> = pangram.chars().collect();
    chars.sort();
    chars.dedup();

    // Създаваме празен низ с променлива дължина
    let mut string = String::new();
    for c in chars {
        // Добавяме знак в края на низа
        string.push(c);
        // Добавяме низ в края на низа
        string.push_str(", ");
    }

    // Окастрения низ е отрязък от първоначалния низ, сиреч няма ново
    // поместване в паметта.
    let chars_to_trim: &[char] = &[' ', ','];
    let trimmed_str: &str = string.trim_matches(chars_to_trim);
    println!("Използвани знаци: {}", trimmed_str);

    // Поместване на низ в купа
    let alice = String::from("Харесвам кучета");
    // Заделяме нова памет и съхраняваме променения низ там
    let bob: String = alice.replace("кучета", "котки");

    println!("Алис казва: {}", alice);
    println!("Боб казва: {}", bob);
}
```

Още методи на `str`/`String` могат да се водят в модулите [std::str][str] и
[std::string][string].

## Буквални стойности и избягване

Има няколко начина да се пишат низови буквални стойности с особени
знаци[^spec_chars] в тях. Всички биват създадени като `&str`, така че може да
се въвеждат както ви е най-удобно. Също така има няколко начина да се пишат
байтови низови буквални стойности, който се създават като `&[u8; N]`.

Обичайно особените знаци се избягват с обратно наклонена черта: `\`. Така можем
да добавим всякакъв знак към низ, дори непечатаеми и такива, които не знаем как
да въведем. Ако искаме да въведем обратно наклонена черта, я избягваме с още
една пред нея: `\\`.

Низовите или знакови разделители в буквална стойност трябва да се избягват с
обратно наклонена черта: `"\""`, `'\''`.

```rust,editable
fn main() {
    // Можем да ползваме избягване, за да пишем байтове като ползваме
    // шестнадесетичните им стойности…
    let byte_escape = "I'm writing \x52\x75\x73\x74!";
    println!("What are you doing\x3F (\\x3F means ?) {}", byte_escape);

    // …или кодови точки от Уникод.
    let unicode_codepoint = "\u{211D}";
    let character_name = "\"DOUBLE-STRUCK CAPITAL R\"";

    println!("Unicode character {} (U+211D) is called {}",
                unicode_codepoint, character_name );


    let long_string = "String literals
                        can span multiple lines.
                        The linebreak and indentation here ->\
                        <- can be escaped too!";
    println!("{}", long_string);
}
```

Понякога има много знаци за избягване или е много по-удобно да напишем низа
така както е. Тук е мястото да се ползват сурови низови буkвални
стойности[^raw_str].

```rust, editable
fn main() {
    let raw_str = r"Escapes don't work here: \x3F \u{211D}";
    println!("{}", raw_str);

    // If you need quotes in a raw string, add a pair of #s
    let quotes = r#"And then I said: "There is no escape!""#;
    println!("{}", quotes);

    // If you need "# in your string, just use more #s in the delimiter.
    // You can use up to 65535 #s.
    let longer_delimiter = r###"A string with "# in it. And even "##!"###;
    println!("{}", longer_delimiter);
}
```

Want a string that's not UTF-8? (Remember, `str` and `String` must be valid UTF-8).
Or maybe you want an array of bytes that's mostly text? Byte strings to the rescue!

```rust, editable
use std::str;

fn main() {
    // Note that this is not actually a `&str`
    let bytestring: &[u8; 21] = b"this is a byte string";

    // Byte arrays don't have the `Display` trait, so printing them is a bit limited
    println!("A byte string: {:?}", bytestring);

    // Byte strings can have byte escapes...
    let escaped = b"\x52\x75\x73\x74 as bytes";
    // ...but no unicode escapes
    // let escaped = b"\u{211D} is not allowed";
    println!("Some escaped bytes: {:?}", escaped);


    // Raw byte strings work just like raw strings
    let raw_bytestring = br"\u{211D} is not escaped here";
    println!("{:?}", raw_bytestring);

    // Converting a byte array to `str` can fail
    if let Ok(my_str) = str::from_utf8(raw_bytestring) {
        println!("And the same as text: '{}'", my_str);
    }

    let _quotes = br#"You can also use "fancier" formatting, \
                    like with normal raw strings"#;

    // Byte strings don't have to be UTF-8
    let shift_jis = b"\x82\xe6\x82\xa8\x82\xb1\x82\xbb"; // "ようこそ" in SHIFT-JIS

    // But then they can't always be converted to `str`
    match str::from_utf8(shift_jis) {
        Ok(my_str) => println!("Conversion successful: '{}'", my_str),
        Err(e) => println!("Conversion failed: {:?}", e),
    };
}
```

For conversions between character encodings check out the [encoding][encoding-crate] crate.

A more detailed listing of the ways to write string literals and escape characters
is given in the ['Tokens' chapter][tokens] of the Ръждьо Reference.


## Б.пр.

[^view]: изглед – view

[^spec_chars]: особени знаци – special characters

[^raw_str]: сурови низови буkвални стойности – raw string literals

поместване в паметта, заделяне на памет – allocation

панграм – Панграмата ((ж. грамат. р.), наричана още панграм (pangram) (м. грамат. р.), от
гръцки: παν γράμμα, pan gramma, „всяка буква“) е изречение, включващо всички
букви (глифи/глифове) от дадена азбука (например българската, английската
латиница или гръцката азбука).

[str]: https://doc.rust-lang.org/std/str/
[string]: https://doc.rust-lang.org/std/string/
[tokens]: https://doc.rust-lang.org/reference/tokens.html
[encoding-crate]: https://crates.io/crates/encoding
