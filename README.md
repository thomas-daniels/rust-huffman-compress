huffman-compress2
================

This is a fork of Niklas Fiekas's [huffman-compress](https://github.com/niklasf/rust-huffman-compress), which is no longer maintained.

[Huffman compression](https://en.wikipedia.org/wiki/Huffman_coding)
given a probability distribution over arbitrary symbols.

[![crates.io](https://img.shields.io/crates/v/huffman-compress2.svg)](https://crates.io/crates/huffman-compress2)
[![docs.rs](https://docs.rs/huffman-compress2/badge.svg)](https://docs.rs/huffman-compress2)

Examples
--------

```rust
use std::iter::FromIterator;
use std::collections::HashMap;
use bit_vec::BitVec;
use huffman_compress::{CodeBuilder, Book, Tree};

let mut weights = HashMap::new();
weights.insert("CG", 293);
weights.insert("AG", 34);
weights.insert("AT", 4);
weights.insert("CT", 4);
weights.insert("TG", 1);

// Construct a Huffman code based on the weights (e.g. counts or relative
// frequencies).
let (book, tree) = CodeBuilder::from_iter(weights).finish();

// More frequent symbols will be encoded with fewer bits.
assert!(book.get("CG").map_or(0, |cg| cg.len()) <
        book.get("AG").map_or(0, |ag| ag.len()));

// Encode some symbols using the book.
let mut buffer = BitVec::new();
let example = vec!["AT", "CG", "AT", "TG", "AG", "CT", "CT", "AG", "CG"];
for symbol in &example {
    book.encode(&mut buffer, symbol);
}

// Decode the symbols using the tree.
let decoded: Vec<&str> = tree.decoder(&buffer).collect();
assert_eq!(decoded, example);
```

Documentation
-------------

[Read the documentation](https://docs.rs/huffman-compress)

Serialization
-------------

Enable the `serde` feature for (de)serialization of the `Book` and `Tree` structs.

Changelog
---------

* 0.7
  - Update to `bit-vec` 0.8.
  - Implement serde support.
  - Switch to Rust 2021 edition.
  - **huffman-compress2 fork starts here**
* 0.6.1
  - Fix deprecation warning and remove `#[deny(warnings)]` (a future
    compatibility hazard in libraries).
* 0.6.0
  - Update to `bit-vec` 0.6.
* 0.5.0
  - Update to `bit-vec` 0.5.
* 0.4.0
  - Renamed `Tree::decoder()` to `Tree::unbounded_decoder()` to avoid
    surprises. A new `Tree::decoder()` takes the maximum number of symbols to
    decode.
  - No longer reexporting `Saturating` from num-traits.
* 0.3.2
  - Preallocate arena space for Huffman tree.
* 0.3.1
  - Update num-traits to 0.2 (semver compatible).
* 0.3.0
  - Introduce `CodeBuilder`.
  - Changes tie breaking order.
* 0.2.0
  - Allow initialization from iterators without creating a `HashMap`. Thanks
    @mernen.
  - Require `K: Ord` instead of `K: Hash + Eq` for symbols and switch `Book`
    internals from `HashMap` to `BTreeMap`.
  - Specify stability guarantees.
* 0.1.1
  - Expose more methods on `Book`.
* 0.1.0
  - Initial release.

License
-------

huffman-compress is dual licensed under the [Apache 2.0](http://www.apache.org/licenses/LICENSE-2.0)
and [MIT](http://opensource.org/licenses/MIT) license, at your option.
