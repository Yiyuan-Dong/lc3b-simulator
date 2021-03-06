##### p1(basic):

```assembly
add r2, r1, 3
jmp 0x0
```

should have:

```
r2 = 0x3
```

##### p2(ALU):

```assembly
add r1, r1, 0x3
add r1, r1, r1
add r2, r1, 0x7
and r3, r2, 0x8
not r4, r3
shfl r5, r2, 0x2
shfr r6, r2, 0x1
shfrs r7, r4, 0x2
and r7, r7, 0b10011
jmp 0x0
```

should have:

```
r1 = 0x6
r2 = 0xd
r3 = 0x8
r4 = 0xfff7
r5 = 0x34
r6 = 0x6
r7 = 0xfff1
```

##### p3(Mem):

```assembly
add r1, r1, 0x6
shfl r1, r1, 0xc  # r1 = 0x6000
add r2, r2, 0x5
add r3, r3, 0x4
add r4, r1, 0x4  # r4 = 0x6004
stb r2, r1, 0x0  # mem[0x6000] = 0x5
stb r3, r1, 0x1  # mem[0x6001] = 0x4
str r4, r1, 0x2  # memword[0x6002] = 0x6004
sti r1, r1, 0x2  # memword[0x6004] = 0x6000
ldb r5, r1, 0x0  # r5 = 0x5
ldb r6, r1, 0x1  # r6 = 0x4
ldr r7, r1, 0x2  # r7 = 0x0504
ldi r2, r1, 0x2  # r2 = 0x6000
ldr r3, r1, 0x0  # r3 = 0x6004
jmp 0x0
```

should have:

```
r1 = 0x6000
r2 = 0x6000
r3 = 0x0504
r4 = 0x6004
r5 = 0x5
r6 = 0x4
r7 = 0x6004
```

##### p4(Control):

```assembly
lea r1, 0x0  # P = 1  (0x3000)
br 0b000, 0x2  # do not branch  (0x3002)
br 0b001, 0x2  # branch  (0x3004)
jmp r0, 0x0  # should not execute  (0x3006)
lea r2, 0x8  # (0x3008)
jsr 0xC  # jump to LABEL2 (0x300A)
jsrr r2  # jump to LABEL1 (0x300C)
jmp r0, 0x0
jmp r0, 0x0
LABEL1:
lea r3, 0x2  # (0x3012)
ret
jmp r0, 0x0
LABEL2:
lea r4, 0x4  # (0x3018)
ret
add r3, r3, 0x4  # will not execute
```

should have:

```
r1 = 0x3002
r2 = 0x3012
r3 = 0x3016
r4 = 0x301E
```

