# re: Patches/Enhancements

## Atomic Operations
For running linux you need a kernel which treats as l.swa op as memory write op,
e.g. ensure that 0x300 (data page fault) exception handler in arch/openrisc/kernel/entry.S in linux kernel has following check:

> l.sfgeui r6,0x33                   // check opcode for write access

Recent kernels have it, older ones (3.x) may know nothing about l.swa (0x33 op id) and above check contains 0x34 constant.

## Edge-triggered interrupts
Edge-triggered interrupts also need special treatment in linux kernel, ensure that irqchip driver (or just a generic handler in arch/openrisc/kernel/irq.c in older kernels) is configured to acknowledge an interrupt by writing '1' to SR register in PIC (to a bit corresponding to this interrupt).
"opencores,or1k-pic" one does it, "opencores,or1200-pic" does not (it tries to directly clear that bit instead, it wont work).

## Multiply-accumulate (MAC) ops
To use MAC ops in linux there is a need for patched linux kernel, which should save/restore MAC SPRs along with GPRs when switching execution contexts.
