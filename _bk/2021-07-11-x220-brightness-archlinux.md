---
layout: post
---

The brightness adjustment function keys don't work out of the box.

Adding the following to the kernel parameters makes it work.

```
acpi_osi='!Windows 2012'
```
