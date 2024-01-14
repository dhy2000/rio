# rio

My Rust-IO library code for algorithm online-judge problems.

Supported features:

- Read line to string
- Read a single item in one line
- Read an array of items with the same type in one line
- Read tuples from one line, support different types

TODOs:

- Support customized input and output stream
- Support buffered output

Simplified version for copy:

```rust
#[allow(dead_code, unused_macros)]
mod rio {
    use std::{io, str::FromStr};

    pub fn read_line() -> String {
        let mut line = String::new();
        io::stdin().read_line(&mut line).unwrap();
        line.trim().to_owned()
    }

    pub fn read_list<T>() -> Vec<T>
    where
        T: FromStr,
    {
        let mut line = String::new();
        match io::stdin().read_line(&mut line) {
            Ok(bytes) => match bytes {
                0 => vec![],
                _ => line
                    .trim()
                    .split(" ")
                    .map(str::trim)
                    .filter_map(|x| (!x.is_empty()).then(|| x.parse::<T>().ok().unwrap()))
                    .collect::<Vec<T>>(),
            },
            Err(e) => panic!("{}", e),
        }
    }

    pub fn read_one<T>() -> Option<T>
    where
        T: FromStr,
    {
        let mut iter = read_list::<T>().into_iter();
        match iter.next() {
            Some(t) => {
                assert!(iter.next().is_none());
                Some(t)
            }
            None => None,
        }
    }

    #[macro_export]
    macro_rules! read_tuple {
        ( $( $t:ty ),* ) => {
            {
                let mut line = String::new();
                match std::io::stdin().read_line(&mut line) {
                    Ok(bytes) => match bytes {
                        0 => None,
                        _ => {
                            let mut iter = line.trim().split(" ").map(str::trim);
                            let r = (
                                // `$( ... )*` : https://doc.rust-lang.org/book/ch19-06-macros.html
                                $(
                                    iter.next().unwrap().parse::<$t>().ok().unwrap(),
                                )*
                            );
                            assert!(iter.next().is_none());
                            Some(r)
                        }
                    },
                    Err(e) => panic!("{}", e),
                }
            }
        }
    }
}
```