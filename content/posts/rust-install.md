---
title: "Install rust on ubuntu 20.04"
date: 2023-07-01
draft: false
description: "透過 rustup 來安裝 rust"
tags: ["rust", "rustup"]
categories: ["rust"]
---

# Install rust on ubuntu 20.04

## 透過 rustup 來安裝 rust

此安裝方式是官方所推薦。rustup 是負責安裝及管理 Rust 的工具。在 Rust 開發環境中，所有工具都安裝在 ~/.cargo/bin 目錄中。您也可以在這裡找到 Rust 工具鏈，包括 rustc、cargo 以及 rustup。

進行安裝前可以透過以下指令來確認是否已經安裝了 rust:
```bash
$ rustc --version
```

如果顯示出 rustc 版本，則表是在你的環境中已安裝好了 rust:
```bash
rustc 1.70.0 (90c541806 2023-05-31)
```


### 下載 rustup

先確認你的 ubuntu 已安裝了 curl。

```bash
$ curl --version
curl 7.68.0 (x86_64-pc-linux-gnu) libcurl/7.68.0 OpenSSL/1.1.1f zlib/1.2.11 brotli/1.0.7 libidn2/2.2.0 libpsl/0.21.0 (+libidn2/2.2.0) libssh/0.9.3/openssl/zlib nghttp2/1.40.0 librtmp/2.3
Release-Date: 2020-01-08
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtmp rtsp scp sftp smb smbs smtp smtps telnet tftp 
Features: AsynchDNS brotli GSS-API HTTP2 HTTPS-proxy IDN IPv6 Kerberos Largefile libz NTLM NTLM_WB PSL SPNEGO SSL TLS-SRP UnixSockets
```

若已安裝了 curl，再使用以下指令來下載 rustup:

```bash
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

```terminal
info: downloading installer

Welcome to Rust!

This will download and install the official compiler for the Rust
programming language, and its package manager, Cargo.

Rustup metadata and toolchains will be installed into the Rustup
home directory, located at:

  /home/egs/.rustup

This can be modified with the RUSTUP_HOME environment variable.

The Cargo home directory is located at:

  /home/egs/.cargo

This can be modified with the CARGO_HOME environment variable.

The cargo, rustc, rustup and other commands will be added to
Cargo's bin directory, located at:

  /home/egs/.cargo/bin

This path will then be added to your PATH environment variable by
modifying the profile files located at:

  /home/egs/.profile
  /home/egs/.bashrc
  /home/egs/.zshenv

You can uninstall at any time with rustup self uninstall and
these changes will be reverted.

Current installation options:


   default host triple: x86_64-unknown-linux-gnu
     default toolchain: stable (default)
               profile: default
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
>1
```

下載完成，選擇 1) 進行安裝程序

```terminal
info: profile set to 'default'
info: default host triple is x86_64-unknown-linux-gnu
info: syncing channel updates for 'stable-x86_64-unknown-linux-gnu'
info: latest update on 2023-06-01, rust version 1.70.0 (90c541806 2023-05-31)
info: downloading component 'cargo'
info: downloading component 'clippy'
info: downloading component 'rust-docs'
 13.5 MiB /  13.5 MiB (100 %)   6.7 MiB/s in  1s ETA:  0s
info: downloading component 'rust-std'
 27.3 MiB /  27.3 MiB (100 %)   7.0 MiB/s in  3s ETA:  0s
info: downloading component 'rustc'
 64.3 MiB /  64.3 MiB (100 %)   6.7 MiB/s in  9s ETA:  0s
info: downloading component 'rustfmt'
info: installing component 'cargo'
info: installing component 'clippy'
info: installing component 'rust-docs'
 13.5 MiB /  13.5 MiB (100 %)   7.4 MiB/s in  1s ETA:  0s
info: installing component 'rust-std'
 27.3 MiB /  27.3 MiB (100 %)  16.1 MiB/s in  1s ETA:  0s
info: installing component 'rustc'
 64.3 MiB /  64.3 MiB (100 %)  18.3 MiB/s in  3s ETA:  0s
info: installing component 'rustfmt'
info: default toolchain set to 'stable-x86_64-unknown-linux-gnu'

  stable-x86_64-unknown-linux-gnu installed - rustc 1.70.0 (90c541806 2023-05-31)


Rust is installed now. Great!

To get started you may need to restart your current shell.
This would reload your PATH environment variable to include
Cargo's bin directory ($HOME/.cargo/bin).

To configure your current shell, run:
source "$HOME/.cargo/env"
```

### 設定 PATH 環境變數

安裝完成後，使用 source 設定 PATH 環境變數，方可順利執行 rustc 

```bash
$ source $HOME/.cargo/env
```

### 驗證是否安裝成功

```bash
$ rustc --version
rustc 1.70.0 (90c541806 2023-05-31)
```

### 更新 rustup
若之前已安裝過早期的版本，可利用以下指令來進行版本更新：
```bash
$ rustup update
```

### 解除安裝 Rust

要解決 rust，直接移除 rustup 即可。

```bash
$ rustup self uninstall
```


### 安裝編譯器

Rust 需要一個鏈接器程序將編譯後的輸出連接到一個檔案中。在 build-essential pacakge 中的 GNU Compiler Collection(gcc) 編譯器集合中有內含了一個 linker，所以先確認你的環境中是否已安裝了 gcc。

```bash
$ gcc --version
gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

如果要安裝 gcc，可透過以下步驟：

- 先更新 Apt package index:

```bash
$ sudo apt update
```

- 升級已過期的套件:

```bash
$ sudo apt upgrade
```

- 安裝 build-essential package:

```bash
$ sudo apt install build-essential
```

- 確認是否已安裝好 gcc

```terminal
$ gcc --version
gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0
Copyright (C) 2019 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```


## 使用 rustup

### 切換Rust版本：

```bash
$ rustup default <toolchain>
```
- 切換成nightly：

```bash
$ rustup default nightly
```

- 切換成最新的穩定版：

```bash
$ rustup default stable
```

- 切換成指定版本：

```bash
$ rustup default 1.60.0
```

移除Rust：

```bash
$ rustup self uninstall
```

## 用 rustc 編譯 Rust 程式
通常會透過 cargo 來編譯 Rust 專案，而非直接使用 rustc 。不過我們先用最單純的方式完成 Hello World 這個第一支 rust 程式。

```bash
$ touch main.rs
```

main.rs 的內容如下：

```rust
fn main() {
    println!("Hello World!");
}
```

rustc 是 Rust 的編譯器程式，若要編譯剛才寫好的 main.rs，可以使用以下指令：

```bash
$ rustc main.rs
```

編譯成功後，會産生一個 main binary file，執行它就會顯示 Hello World!

```terminal
$ ./main 
Hello, world!
```



## Cargo的使用方式

### 用Cargo建立Rust程式專案

使用Cargo建立Rust程式專案的方式非常簡單，指令如下：

```bash
$ cargo new <專案名稱>
```

預設會建立出可執行的應用程式專案，這種專案可以製作出給作業系統執行的程式執行檔。由於我們的 Hello World 本身就應該要是可以直接執行的程式，所以要使用這種類型的程式專案來開發。在習慣上，我們會加上 --bin 參數，確定建立出來的程式專案是可執行的應用程式專案。
指令如下：

```bash
$ cargo new --bin <專案名稱>
```

如果要建立出可以被用來相依進其它專案中作為函式庫使用的函式庫程式專案，則是需要加上--lib參數。
指令如下：

```bash
$ cargo new --lib <專案名稱>
```

所以，要使用Cargo建立出名為hello_world的可執行應用程式專案。
指令如下：

```bash
$ cargo new --bin hello_world
```

如此一來便會在當前工作目錄下產生出一個 hello_world 目錄，裡面已經有了 Cargo.toml 這個專案設定檔和存放 Rust 程式原始碼的 src 目錄，且這個 src 目錄中已經建立了 main.rs 這個 Rust 程式碼檔案。

```terminal
$ tree .
.
├── Cargo.toml
└── src
    └── main.rs
```

用文字編輯器打開　main.rs，更可以發現檔案中已經內建了我們在上一節寫好的　Hello World　程式碼！這是　Cargo　建立可執行應用程式專案會預先填寫的程式，如此一來新建出來的程式專案也可以直接被編譯執行了。我們先來看看Cargo.toml這個設定檔吧！

TOML(Tom's Obvious, Minimal Language)是一種設定檔的格式，比YAML(YAML Ain't Markup Language)還要更簡潔優雅，Cargo選用了TOML作為其設定檔的格式。預設產生出來的Cargo.toml長成底下這個樣子：

``` Cargo.toml
[package]                                                                       
name = "hello_world"
version = "0.1.0"
edition = "2021"
 
# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html
 
[dependencies]
```

第一行的 [package] 代表其和底下的敘述所組成的區域是用來撰寫專案的設定。最後一行的 [dependencies] 則是代表其和底下的敘述所組成的區域為這個專案需要使用到哪些「crate」(板條箱)，「crate」是Rust對於套件的特別稱呼。將crate加進 Cargo.toml 檔案中的的 [dependencies] 區域，Cargo在建置專案的時候就會自動把相依的套件下載下來編譯。這個 Hello World 程式專案並不需要用到任何相依套件，因此我們不用動到這個區域的設定，讓它繼續維持什麼都沒有的狀態即可。

用 Cargo 建立出的程式專案來開發Rust程式，專案的檔案目錄結構需要依循 Cargo 訂定規則。Rust 的程式碼，也就是那些 *.rs 檔案，通通都是放在 src 目錄中。至於其它跟 Rust 的程式碼無直接關係的檔案，例如Cargo.toml、README、LICENSE 等等的設定檔和文件，則是放在專案根目錄下，與 Rust 程式碼放置的位置區分開來。


## 用 Cargo 來編譯和執行專案

### 用 Cargo 編譯專案

將終端機的工作目錄移動到Cargo專案根目錄，執行以下指令：

```terminal
$ cargo build
   Compiling hello_world v0.1.0 (/home/egs/cal-data/tech-test/rust/hello_world)
    Finished dev [unoptimized + debuginfo] target(s) in 0.33s
```

編譯成功後，Rust 的程式執行檔會存放在專案根目錄的 target/debug 目錄中。但我們在開發階段通常不會直接從這個目錄中把執行檔案找出來執行。

```terminal
$ tree .
.
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target
    ├── CACHEDIR.TAG
    └── debug
        ├── build
        ├── deps
        │   ├── hello_world-e22d40e86553def7
        │   └── hello_world-e22d40e86553def7.d
        ├── examples
        ├── hello_world
        ├── hello_world.d
        └── incremental
            └── hello_world-2j7rtrfivr93b
                ├── s-gmfa83b3xo-1cjgjtc-1z77vak65s0ob
                │   ├── 1jus6o66igae3ze0.o
                │   ├── 2tso0xryp4hk7njb.o
                │   ├── 30mjrv4l6r2a11zg.o
                │   ├── 3f3n7fk5va7dfy43.o
                │   ├── 3xvbrrewyhi5p5s1.o
                │   ├── 4xr9u3m81u6vkx31.o
                │   ├── dep-graph.bin
                │   ├── query-cache.bin
                │   └── work-products.bin
                └── s-gmfa83b3xo-1cjgjtc.lock

9 directories, 18 files
```

### 用Cargo執行專案

執行以下指令：

```bash
$ cargo run
```

就可以看到程式專案的執行結果了！

```terminal
$ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.00s
     Running `target/debug/hello_world`
Hello, world!
```

如果專案沒有事先使用cargo build進行編譯的話，直接使用cargo run也會先幫您進行編譯。


### 用Cargo檢查專案有沒有問題

執行以下指令：

```bash
$ cargo check
```

這個指令會編譯專案，但不會產生出最終的目的檔(執行檔)，可以快速驗證Rust的程式碼是否能通過編譯。

### 用Cargo發佈程式

在程式專案完結的時候，會需要進行最終的程式編譯輸出。與開發階段不同的是，作為產品輸出的程式應該要透過編譯器進行一些優化，並且移除掉開發階段時用來偵錯(debug)的功能和訊息，確保正式版本的程式能在最佳狀態下運作(高效能、小體積)。

和編譯專案的方式一樣，都是使用 cargo build 指令，只是需要加上 --release 參數。

```bash
$ cargo build --release
```

加上 --release 參數編譯出來的程式會放置在專案根目錄的 target/release 目錄中，提交給客戶或是部署到正式環境的程式就是在這邊！

### 用Cargo測試程式

開發程式時，除了要想辦法把功能完成之外，也要另外有個方式去驗證寫出來的程式是不是能正確執行。Cargo 有提供測試專案的指令：

```bash
$  cargo test
```

### 清理Cargo程式專案

刪除 Cargo 在檢查、編譯或是測試程式專案時所產生出來的暫存檔案和執行檔案。

```bash
$ cargo clean
```

這個指令會把程式專案下的整個 target 目錄刪除。
