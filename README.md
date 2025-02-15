# `vecbase`



An extremely small, "fast enough" vector search SQLite extension that runs
anywhere! A successor to [`sqlite-vss`](https://github.com/asg017/sqlite-vss)

<!-- deno-fmt-ignore-start -->

> [!IMPORTANT]
> _`vecbase` is a pre-v1, so expect breaking changes!_

<!-- deno-fmt-ignore-end -->

- Store and query float, int8, and binary vectors in `vec0` virtual tables
- Written in pure C, no dependencies, runs anywhere SQLite runs
  (Linux/MacOS/Windows, in the browser with WASM, Raspberry Pis, etc.)
- Store non-vector data in metadata, auxiliary, or partition key columns


## Installing

See [Installing `sqlite-vec`](https://alexgarcia.xyz/sqlite-vec/installation.html)
for more details.

| Language       | Install                                              | More Info                                                                             |                                                                                                                                                                                                    |
| -------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Python         | `pip install sqlite-vec`                             | [`sqlite-vec` with Python](https://alexgarcia.xyz/sqlite-vec/python.html)             | [![PyPI](https://img.shields.io/pypi/v/sqlite-vec.svg?color=blue&logo=python&logoColor=white)](https://pypi.org/project/sqlite-vec/)                                                               |
| Node.js        | `npm install sqlite-vec`                             | [`sqlite-vec` with Node.js](https://alexgarcia.xyz/sqlite-vec/js.html)            | [![npm](https://img.shields.io/npm/v/sqlite-vec.svg?color=green&logo=nodedotjs&logoColor=white)](https://www.npmjs.com/package/sqlite-vec)                                                         |
| Ruby           | `gem install sqlite-vec`                             | [`sqlite-vec` with Ruby](https://alexgarcia.xyz/sqlite-vec/ruby.html)                 | ![Gem](https://img.shields.io/gem/v/sqlite-vec?color=red&logo=rubygems&logoColor=white)                                                                       |
| Go             | `go get -u github.com/asg017/sqlite-vec/bindings/go` | [`sqlite-vec` with Go](https://alexgarcia.xyz/sqlite-vec/go.html)                     | [![Go Reference](https://pkg.go.dev/badge/github.com/asg017/sqlite-vec-go-bindings/cgo.svg)](https://pkg.go.dev/github.com/asg017/asg017/sqlite-vec-go-bindings/cgo)                                              |
| Rust           | `cargo add sqlite-vec`                               | [`sqlite-vec` with Rust](https://alexgarcia.xyz/sqlite-vec/rust.html)                 | [![Crates.io](https://img.shields.io/crates/v/sqlite-vec?logo=rust)](https://crates.io/crates/sqlite-vec)                                                                                          |
| Datasette      | `datasette install datasette-sqlite-vec`             | [`sqlite-vec` with Datasette](https://alexgarcia.xyz/sqlite-vec/datasette.html)       | [![Datasette](https://img.shields.io/pypi/v/datasette-sqlite-vec.svg?color=B6B6D9&label=Datasette+plugin&logoColor=white&logo=python)](https://datasette.io/plugins/datasette-sqlite-vec)          |
| rqlite         | `rqlited -extensions-path=sqlite-vec.tar.gz`         | [`sqlite-vec` with rqlite](https://alexgarcia.xyz/sqlite-vec/rqlite.html)                        | [![rqlite](https://img.shields.io/badge/rqlite-sqlite_extensions-blue)](https://rqlite.io/docs/guides/extensions/)           |
| `sqlite-utils` | `sqlite-utils install sqlite-utils-sqlite-vec`       | [`sqlite-vec` with sqlite-utils](https://alexgarcia.xyz/sqlite-vec/sqlite-utils.html) | [![sqlite-utils](https://img.shields.io/pypi/v/sqlite-utils-sqlite-vec.svg?color=B6B6D9&label=sqlite-utils+plugin&logoColor=white&logo=python)](https://datasette.io/plugins/datasette-sqlite-vec) |
| Github Release |                                                      |                                                                                       | ![GitHub tag (latest SemVer pre-release)](https://img.shields.io/github/v/tag/asg017/sqlite-vec?color=lightgrey&include_prereleases&label=Github+release&logo=github)                              |


## Sample usage

```sql
.load ./vec0

create virtual table vec_examples using vec0(
  sample_embedding float[8]
);

-- vectors can be provided as JSON or in a compact binary format
insert into vec_examples(rowid, sample_embedding)
  values
    (1, '[-0.200, 0.250, 0.341, -0.211, 0.645, 0.935, -0.316, -0.924]'),
    (2, '[0.443, -0.501, 0.355, -0.771, 0.707, -0.708, -0.185, 0.362]'),
    (3, '[0.716, -0.927, 0.134, 0.052, -0.669, 0.793, -0.634, -0.162]'),
    (4, '[-0.710, 0.330, 0.656, 0.041, -0.990, 0.726, 0.385, -0.958]');


-- KNN style query
select
  rowid,
  distance
from vec_examples
where sample_embedding match '[0.890, 0.544, 0.825, 0.961, 0.358, 0.0196, 0.521, 0.175]'
order by distance
limit 2;
/*
┌───────┬──────────────────┐
│ rowid │     distance     │
├───────┼──────────────────┤
│ 2     │ 2.38687372207642 │
│ 1     │ 2.38978505134583 │
└───────┴──────────────────┘
*/
```


