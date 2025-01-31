## G.ERR.02    不要滥用 `expect`，请考虑用 `unwrap_or_` 系列方法代替

**【级别】** 建议

**【描述】**

使用 `expect` 的时候请遵循 `expect` 的语义，不要滥用。参考 ： **P.ERR.05** 。

但是对于一些存在“副作用”的函数，在 遇到 `None` 或 `Err` 时，可能需要返回一些指定的值。这个时候用 `expect` 就不太符合语义。

如果你的用法完全符合 `expect` 语义，那么可以设置 `#![allow(clippy::expect_fun_call]`

**【反例】**

```rust
let foo = Some(String::new());
let err_code = "418";
let err_msg = "I'm a teapot";
foo.expect(&format!("Err {}: {}", err_code, err_msg)); 
// or
foo.expect(format!("Err {}: {}", err_code, err_msg).as_str());  
```

**【正例】**

```rust
let foo = Some(String::new());
let err_code = "418";
let err_msg = "I'm a teapot";
foo.unwrap_or_else(|| panic!("Err {}: {}", err_code, err_msg));  // 你可以根据场景选择性使用 panic! 或者 不 panic!
```

**【例外】**

完全符合 `expect` 语义的使用。

```rust
#![allow(clippy::expect_fun_call]

// 这个配置文件默认会跟随源码出现，所以，必定可以读取到
// 这个配置文件不应该没有被提供，如果万一出现了没有提供的情况，需要 Panic 并提供错误信息方便调试，或者让使用者知道原因
let config = Config::read("some_config.toml").expect("Provide the correct configuration file"); 

// or

fn main() {
    use std::net::IpAddr;
    let _home: IpAddr = "127.0.0.1".parse().expect("Provide the correct Ip addr");
}
```


### 【Lint 检测】

| lint name                                                    | Clippy 可检测 | Rustc 可检测 | Lint Group  | level |
| ------------------------------------------------------------ | ------------- | ------------ | ----------- | ----- |
| [expect_fun_call](https://rust-lang.github.io/rust-clippy/master/#expect_fun_call) | yes           | no           | perf        | warn  |
| [expect_used](https://rust-lang.github.io/rust-clippy/master/#expect_used) | yes           | no           | restriction | allow |

