# Зависимости при разработка 

Понякога ни трябват зависимости само за тестове или примери, или за проверка на
бързодействието. Такива зависимости се добавят в `Cargo.toml` в раздел
[dev-dependencies]. Тези зависимости не биват предадени на други пакети, които
зависят от този пакет.

Един такъв пример е
[`pretty_assertions`](https://docs.rs/pretty_assertions/1.0.0/pretty_assertions/index.html),
който разширява стандартните макроси `assert_eq!` и `assert_ne!`, за да предостави
разноцветни разлики.  

Файл `Cargo.toml`:

```toml
# Стандартните данни за коша не са показани.
[dev-dependencies]
pretty_assertions = "1"
```

Файл `src/lib.rs`:

```rust,ignore
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;
    use pretty_assertions::assert_eq; // Кош за ползване само в тестове. Не
    // може да се ползва в нетестов код.

    #[test]
    fn test_add() {
        assert_eq!(add(2, 3), 5);
    }
}
```

## See Also
Документацията на [Карго][cargo] за указване на зависимости.

[cargo]: http://doc.crates.io/specifying-dependencies.html
