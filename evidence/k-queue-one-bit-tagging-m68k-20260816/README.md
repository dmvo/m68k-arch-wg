# k_queue one-bit tagging: focused m68k A/B evidence

This directory contains a fresh focused A/B run of the existing Zephyr FIFO,
LIFO, and queue tests on the m68000 and m68010 QEMU targets.

## Result

| Revision | Configurations | Test cases |
| --- | ---: | ---: |
| Baseline: public `sys_sflist` backing | 0/14 passed; 14 failed | 30 passed; 14 failed; 108 blocked |
| Patched: queue-local one-bit tagging | 14/14 passed | 152/152 passed |

There were no retries, build errors, filtered configurations, modified tests,
or synthetic addresses.

The baseline boots and starts the tests, then corrupts runtime state. Examples
include [heap corruption in `kernel.fifo.timeout`](logs/baseline/m68000/kernel.fifo.timeout.log)
and [an invalid spinlock in `kernel.queue`](logs/baseline/m68000/kernel.queue.log).
Zephyr halts after the fatal error, so Twister reports the affected
configuration as `Timeout`. The patched run completes every configuration
and test case on both CPUs.

The per-configuration counts are in [matrix.csv](matrix.csv).

## Scope

Platforms:

- `qemu_m68k/qemu_virt_m68k_m68000`
- `qemu_m68k/qemu_virt_m68k_m68010`

Existing scenarios:

- `kernel.fifo`
- `kernel.fifo.timeout`
- `kernel.fifo.usage`
- `kernel.lifo`
- `kernel.lifo.usage`
- `kernel.queue`
- `kernel.queue.minimallibc`

The exact invocation is recorded in
[provenance/twister-command.txt](provenance/twister-command.txt).

## Exact A/B delta

The m68k port was taken from preview reference
`bc2212da3aa1904736f2b10c253d323cb1bca294`. Its queue-related changes were
excluded from the common source state. Both sides used the same port,
toolchain, QEMU, tests, Twister options, and the same public `sys_sflist`
alignment change.

- Baseline queue sources are the upstream base blobs from
  `c5dffcb7c9da611b40a828f79fc6a8405416c1ed`.
- Patched queue sources are the exact blobs from staging commit
  `59e3bdcc64c0b3e08b55d2e86c2480a7ecb5a4b4`.
- The stable patch ID is
  `34ff1af01978178a172bc86c1b321d2b0cac7a90`.

The complete source patch is [patches/kqueue.patch](patches/kqueue.patch).
The common `sys_sflist` alignment change is
[patches/sflist-alignment.patch](patches/sflist-alignment.patch). Exact Git
blob IDs are listed in [provenance/source-blobs.txt](provenance/source-blobs.txt).

## Artifacts

- [reports/baseline](reports/baseline) and
  [reports/patched](reports/patched): Twister JSON, test plans, xUnit reports,
  platform reports, and full Twister logs.
- [logs/baseline](logs/baseline) and [logs/patched](logs/patched): raw QEMU
  handler logs for every platform/scenario pair.
- [provenance](provenance): source IDs, exact command, tool versions, cache
  statistics, and a machine-readable manifest.
- [SHA256SUMS](SHA256SUMS): checksums for every other file in this directory.

Broader non-m68k regression coverage is intentionally left to upstream CI.
