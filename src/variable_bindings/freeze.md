# Замразяване

Когато данни са обвързани с променлива неизменяемо, това *ги замразява*.
*Замразени* данни не могат да бъдат променяни, докато непроменимото обвързване
излезе извън видимост.

```rust,editable,ignore,mdbook-runnable
fn main() {
    let mut _mutable_integer = 7i32;

    {
        // Засенчване чрез неменимо обвързване `_mutable_integer`
        let _mutable_integer = _mutable_integer;

        // Грешка! `_mutable_integer` is frozen in this scope
        _mutable_integer = 50;
        // ПОПРАВИ ^ Да се коментира този ред

        // `_mutable_integer` излиза от обхват
    }

    // Ok! `_mutable_integer` не е замразено тук
    _mutable_integer = 3;
}
```
