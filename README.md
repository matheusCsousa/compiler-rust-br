# compiler-rust-br

A compiler written in Rust for a minimal Portuguese-syntax language. Source files (`.br`) are compiled directly to x86-64 Linux executables via NASM assembly.

## How it works

```
source.br → Tokenizer → Parser (AST) → Constructor → a.asm → nasm → ld → output
```

1. **Tokenizer** — converts source text into tokens (`principal`, `imprimir`, `sair`, literals)
2. **Parser** — builds an AST, validates syntax, panics with Portuguese error messages on failure
3. **Constructor** — walks the AST and emits x86-64 NASM assembly using Linux syscalls
4. **nasm + ld** — assembles and links the final binary (called `output`)

## Language syntax

Entry point is always `principal()`. Two built-in functions:

```
principal() {
    imprimir("Olá")
    imprimir("Matheus")
    sair(69)
}
```

| Function | Description |
|---|---|
| `imprimir("text")` | Prints a string to stdout (syscall `write`) |
| `sair(N)` | Exits with code N (syscall `exit`) |

## Dependencies

- Rust + Cargo
- `nasm`
- `ld` (GNU binutils)

On Arch/Manjaro:
```bash
sudo pacman -S rust nasm binutils
```

## Build & Run

```bash
cargo build --release
./target/release/compiler-rust-hydro test.br
./output
```

The compiler also leaves `a.asm` in the working directory for inspection.

## Project Structure

```
.
├── src/
│   ├── main.rs           # CLI, pipeline orchestration, nasm/ld invocation
│   ├── tokenizer.rs      # Lexer — produces Vec<Token>
│   ├── parser.rs         # Recursive descent parser — produces AST
│   └── constructor.rs    # Code generator — emits x86-64 NASM
├── test.br               # Example source file
├── a.asm                 # Generated assembly (after compilation)
└── Cargo.toml
```
