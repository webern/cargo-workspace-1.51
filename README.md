
# Cargo Workspace Behavior Change

This repo demonstrates a change in the way cargo workspaces handle dependencies between 1.50 and 1.51.
Before 1.51, attempting to build a dependency with `--package` fails if it is not a workspace member.
After 1.51 this becomes possible.

## Workspace Description

- `notws` is a directory that contains one library package named `dep_1`.
- `ws` is a directory that contains a workspace-only `Cargo.toml` file and a package that is a member of the workspace, `ws_member_1`.
- `ws_member_1` has `dep_1` as a dependency using `path`.

```
├── notws
│   └── dep_1
│       ├── Cargo.toml
│       └── src
│           └── lib.rs
└── ws
    ├── Cargo.lock
    ├── Cargo.toml
    └── ws_member_1
        ├── Cargo.toml
        └── src
            └── main.rs
```

## Repro

If I try to build `dep_1` from the workspace context with 1.50, it fails to find the package.
This seems reasonable given that `dep_1` is a dependency, but not a workspace member.

```sh
rustup default 1.50.0
cargo build --manifest-path ./ws/Cargo.toml --package dep_1
# error: package ID specification `dep_1` matched no packages
```

This works with 1.51.0:

```sh
rustup default 1.51.0
cargo build --manifest-path ./ws/Cargo.toml --package dep_1
```

