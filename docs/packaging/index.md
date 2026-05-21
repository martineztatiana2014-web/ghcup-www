# Packaging

This is meant for developers of upstream projects (such as GHC, HLS, Cabal or stack), but
is also important for users who want to ship new [new tools](new_tools.md) via GHCup.

## Maintaining a 3rd-party channel

Make sure to understand the metadata format properly. Currently we do not have a yaml schema, so you may need to
consult existing metadata files, e.g. [ghcup-0.1.0.yaml](https://github.com/haskell/ghcup-metadata/blob/develop/ghcup-0.1.0.yaml).
The source of truth is the [GHCupInfo type](https://github.com/haskell/ghcup-hs/blob/master/lib/GHCup/Types.hs) and the related
[JSON instances](https://github.com/haskell/ghcup-hs/blob/master/lib/GHCup/Types/JSON.hs).

GHCup also supports Dhall, for which we do provide a quasi-schema, see [Using Dhall](index.md#using-dhall).

A few caveats:

- version your channel according to the ghcup metadata format version: `<channel-name>-<metadata-version>.yaml`, where `metadata-version` is e.g. `0.0.9`, see [https://github.com/haskell/ghcup-metadata](https://github.com/haskell/ghcup-metadata) for the latest versions (the format is not properly documented, so you need to understand the changes to `GHCup.Types`)
- all channels should be append-only
- for GHC downloads, don't forget to specify the `base-XXX` tag
- do NOT use the `recommended` or `latest` tags, unless your channel is a so-called "base channel" (like vanilla, also see the [explanation here](https://github.com/haskell/ghcup-metadata?tab=readme-ov-file#metadata-variants-distribution-channels))
- if the tarball name in `dlUri` is not properly unique, make sure to specify `dlOutput` with a unique name (otherwise the ghcup cache may break and the user will get hash mismatches)
- please support gpg signatures of your channel (`<channel-name>-<metadata-version>.yaml.sig` alongside the main file)
- please do not advertise pinning of channels (e.g. via git commit in the URL)... it breaks propagating security advisories via `viPreInstall` and `viPostInstall`

## Using Dhall

GHCup now supports using Dhall instead of yaml. GHCup can output the schema:

```sh
ghcup generate dhall-schema
```

Make sure to create a resolved dhall file (with no imports) with the `.dhall` file extension.
GHCup will reject expressions that contain imports. Dhall binary format is supported (`.dhallb` extension) as well.

Also check `ghcup generate --help` for further utilities.

Please note that unlike YAML, we do not guarantee any backwards compatibility for Dhall. You can write
your metadata in Dhall however and then use [`dhall-to-yaml-ng`](https://hackage.haskell.org/package/dhall-yaml) to convert it to YAML.

