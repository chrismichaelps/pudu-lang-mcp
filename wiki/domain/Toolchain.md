---
type: domain
tags: [domain]
---

# Toolchain

The installed `pudu` executable and its standard library, located once at start-up
([[decisions/ADR-0003-toolchain-subprocess]]). A missing executable does not stop the server:
documentation keeps working and compiler tools answer a tool error saying how to install Pudu.

## Referenced by

[[domain/_MOC]] · [[seams/Toolchain]] · [[architecture/LANGUAGE]]
