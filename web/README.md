# Setup

- ensure symlinks are on in .git/config

    - Example `config`:

        ```bash
        [core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = true
        ignorecase = true
        ...
        ```

- ensure that wasm/data are treated as binary

    - Example `.gitattributes`:

        ```bash
        *.wasm binary
        *.data binary
        ```