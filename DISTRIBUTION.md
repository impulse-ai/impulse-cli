# Distribution

This repository is published by Impulse AI. The `impulse` CLI's source
lives in a private engineering repository; this repository distributes
built binaries only.

No `LICENSE` file is published yet. By default that means all rights
are reserved — downloading and running this binary for your own use is
fine, but redistribution or modification rights haven't been explicitly
granted. Contact Impulse AI if you need a different license for your
use case.

No credentials, API keys, or tokens are ever included in a release
archive. `impulse auth login` stores them locally on your machine,
after installation.

## Verifying a download

Every release publishes a `checksums.txt` (SHA-256) alongside the
archives:

    shasum -a 256 -c checksums.txt --ignore-missing

See [README.md](README.md) for installation and usage instructions.
