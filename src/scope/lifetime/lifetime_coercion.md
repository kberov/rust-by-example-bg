# Принуждение

По-дълъг живот може да бъде принудително сведен към по-къс така, че да работи в
обхват, в който иначе не би работил. Това се постига чрез отгатнато принуждение
към по-късия живот от компилатора на Ръждьо, а също и чрез обявяване на разлика
в животите.
A longer lifetime can be coerced into a shorter one 
so that it works inside a scope it normally wouldn't work in.
This comes in the form of inferred coercion by the Ръждьо compiler,
and also in the form of declaring a lifetime difference:

```rust,editable
// Тук, Ръждьо отгатва възможно най-кратката продължителност на живота. След
// това двете препратки биват сведени принудително към тази продължителност.
fn multiply<'a>(first: &'a i32, second: &'a i32) -> i32 {
    first * second
}

// `<'a: 'b, 'b>` се чете така: живот `'a` е дълъг поне колкото `'b`.
// Тук, приемаме като вход `&'a i32` и връщаме `&'b i32` като резултат от
// принуждението.
fn choose_first<'a: 'b, 'b>(first: &'a i32, _: &'b i32) -> &'b i32 {
    first
}

fn main() {
    let first = 2; // По-дълъг живот
    
    {
        let second = 3; // По-кратък живот
        
        println!("Произведението е {}", multiply(&first, &second));
        println!("{} is the first", choose_first(&first, &second));
    };
}
```
