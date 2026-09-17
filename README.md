# livox-sdk2

[Livox-SDK2](https://github.com/Livox-SDK/Livox-SDK2) v1.2.5 as a nix flake, with the
patches needed to build it outside its original environment.

It fetches Livox's own release — none of their source is redistributed here. What this
repo adds is:

- **`darwin.patch`** — macOS socket fixes: `SO_RCVBUF` is set larger than macOS accepts,
  and the broadcast bind fails as written.
- **`-Werror` removal** — the bundled rapidjson's `RAPIDJSON_DIAG_OFF(foo-bar)` macros
  stringify with spaces under newer clang, producing invalid warning-group names, and
  there is an unused `FastCRC` field. Both are fatal under `-Werror`, and
  `-DCMAKE_CXX_FLAGS=-Wno-error` does not help because `add_compile_options(-Werror)`
  is set deeper in `sdk_core`.
- **`<cstdint>` includes** — two headers rely on them transitively under older libstdc++.
- **samples removed** — they do not build and nothing needs them.

## Use

```nix
{
  inputs.livox-sdk2.url = "github:jeff-hykin/livox-sdk2";

  # then, in your outputs:
  #   buildInputs = [ livox-sdk2.packages.${system}.default ];
}
```

`nix build` gives you the shared library and headers.
