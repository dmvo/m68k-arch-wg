## Validation

A focused A/B comparison was run using the existing Zephyr FIFO, LIFO, and
queue test scenarios on both m68000 and m68010 QEMU targets.

Both sides used the same m68k/QEMU port, toolchain, emulator, tests, Twister
options, and `sys_sflist` node-alignment change. The only A/B source delta was
this change (stable patch ID
`34ff1af01978178a172bc86c1b321d2b0cac7a90`). No tests were modified and no
synthetic addresses were used.

| Revision | Configurations | Test cases |
| --- | ---: | ---: |
| Baseline | 0/14 passed; 14 failed | 30 passed; 14 failed; 108 blocked |
| Patched | 14/14 passed | 152/152 passed |

The baseline failures are runtime kernel corruption, including invalid
spinlocks and heap corruption. Twister records the configurations as timeouts
because the fatal error halts Zephyr before the suite can finish. The patched
revision passes every selected configuration and test case with zero retries.

Complete Twister reports, per-platform reports, raw QEMU logs, the exact source
patch, source blob IDs, tool versions, and checksums are archived in the
[m68k A/B evidence bundle](https://github.com/dmvo/m68k-arch-wg/tree/master/evidence/k-queue-one-bit-tagging-m68k-20260816).

Broader regression coverage is left to upstream CI.
