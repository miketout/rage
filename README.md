# rage: Rust implementation of age

age is a simple, secure and modern encryption tool with small explicit keys, no
config options, and UNIX-style composability. The format specification is at
[age-encryption.org/v1](https://age-encryption.org/v1).

rage is a Rust implementation of the age tool. It is pronounced like the Japanese
[らげ](https://translate.google.com/#view=home&op=translate&sl=ja&tl=en&text=%E3%82%89%E3%81%92)
(with a hard g).

To discuss the spec or other age related topics, please email
[the mailing list](https://groups.google.com/d/forum/age-dev) at
age-dev@googlegroups.com. age was designed by
[@Benjojo12](https://twitter.com/Benjojo12) and
[@FiloSottile](https://twitter.com/FiloSottile).

The reference interoperable Golang implementation is available at
[filippo.io/age](https://filippo.io/age).

## Usage

```
Usage:
  rage -r RECIPIENT [-a] [-o OUTPUT] [INPUT]
  rage --decrypt [-i IDENTITY] [-o OUTPUT] [INPUT]

Positional arguments:
  INPUT                      Path to a file to read from.

Optional arguments:
  -h, --help                 Print this help message and exit.
  -V, --version              Print version info and exit.
  -d, --decrypt              Decrypt the input.
  -p, --passphrase           Encrypt with a passphrase instead of recipients.
  --max-work-factor WF       Maximum work factor to allow for passphrase decryption.
  -a, --armor                Encrypt to a PEM encoded format.
  -r, --recipient RECIPIENT  Encrypt to the specified RECIPIENT. May be repeated.
  -i, --identity IDENTITY    Use the private key file at IDENTITY. May be repeated.
  -o, --output OUTPUT        Write the result to the file at path OUTPUT.

INPUT defaults to standard input, and OUTPUT defaults to standard output.

RECIPIENT can be:
- An age public key, as generated by rage-keygen ("age1...").
- An SSH public key ("ssh-ed25519 AAAA...", "ssh-rsa AAAA...").
- A path or HTTPS URL to a file containing age recipients, one per line
  (ignoring "#" prefixed comments and empty lines).

IDENTITY is a path to a file with age identities, one per line
(ignoring "#" prefixed comments and empty lines), or to an SSH key file.
Multiple identities may be provided, and any unused ones will be ignored.
```

### Multiple recipients

Files can be encrypted to multiple recipients by repeating `-r/--recipient`.
Every recipient will be able to decrypt the file.

```bash
$ rage -o example.png.age -r age1uvscypafkkxt6u2gkguxet62cenfmnpc0smzzlyun0lzszfatawq4kvf2u \
    -r age1ex4ty8ppg02555at009uwu5vlk5686k3f23e7mac9z093uvzfp8sxr5jum example.png
```

### Passphrases

Files can be encrypted with a passphrase by using `-p/--passphrase`. By default
rage will automatically generate a secure passphrase.

```bash
$ rage -p -o example.png.age example.png
Type passphrase (leave empty to autogenerate a secure one): [hidden]
Using an autogenerated passphrase:
    kiwi-general-undo-bubble-dwarf-dizzy-fame-side-sunset-sibling
$ rage -d example.png.age >example.png
Type passphrase: [hidden]
```

### SSH keys

As a convenience feature, rage also supports encrypting to `ssh-rsa` and
`ssh-ed25519` SSH public keys, and decrypting with the respective private key
file. (`ssh-agent` is not supported.)

```
$ cat ~/.ssh/id_ed25519.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIZDRcvS8PnhXr30WKSKmf7WKKi92ACUa5nW589WukJz str4d@internet.arpa
$ rage -r "ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIIZDRcvS8PnhXr30WKSKmf7WKKi92ACUa5nW589WukJz" example.png > example.png.age
$ rage -d -i ~/.ssh/id_ed25519 example.png.age > example.png
```

`ssh-rsa` support is currently behind the `unstable` feature flag.

Note that SSH key support employs more complex cryptography, and embeds a public
key tag in the encrypted file, making it possible to track files that are
encrypted to a specific public key.

## Installation

On macOS or Linux, you can use Homebrew:

```
brew tap str4d.xyz/rage https://str4d.xyz/rage
brew install rage
```

On Windows, Linux, and macOS, you can use the
[pre-built binaries](https://github.com/str4d/rage/releases).

If your system has Rust 1.37+ installed (either via `rustup` or a system
package), you can build directly from source:

```
cargo install rage
```

> Note: previously the `rage` suite of tools was provided in the `age` Rust
> crate. This is no longer the case; `age` now only contains the Rust library.

Help from new packagers is very welcome.

### Feature flags

- `mount` enables the `rage-mount` tool, which can mount age-encrypted TAR or
  ZIP archives as read-only. It is currently only usable on Unix systems, as it
  relies on `libfuse`.

- `unstable` enables in-development functionality. Anything behind this feature
  flag has no stability or interoperability guarantees.

## License

Licensed under either of

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or
   http://www.apache.org/licenses/LICENSE-2.0)
 * MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally
submitted for inclusion in the work by you, as defined in the Apache-2.0
license, shall be dual licensed as above, without any additional terms or
conditions.

