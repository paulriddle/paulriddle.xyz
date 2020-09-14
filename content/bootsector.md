+++
title = "Writing a game in boot sector"
+++

# Writing a game in boot sector

Not for any practical reason really. It's just that I've seen a few people do
it, one of them gave a couple of talks about it, another wrote two books on the
topic and I decided to try writing my own. At this point I don't have any
particular game in mind but it's about the journey and not the destination
anyway and it's not like I'm going to actually *boot* my game on real hardware.
Most of my stuff is using UEFI in non-legacy mode anyway. So like any reasonable
person I'm going to use QEMU.

First step is to make QEMU boot my image and instead of loading some operating
system, write some text to the screen and loop forever. For that install `qemu`
and `nasm`. I want to write two strings: 'Hello, world!' followed by 'Goodbye!'.

```nasm
; filename: game.asm
[org 0x7c00]  ; start writing code in the boot sector

mov ax, 0x0   ; set video mode
int 0x10      ; think of it as function call interruput(ax)

mov bx, HELLO_MSG
call print_string

mov bx, GOODBYE_MSG
call print_string

jmp $         ; jump forever

print_string:
  push bx
  mov ah, 0xe ; BIOS tele-type output, every call to
              ; int 0x10 will print char in ax
  loop:
    mov al, [bx]  ; load char
    test al, al
    jz break      ; break if al == 0
    int 0x10
    add bx, 1
    jmp loop
  break:
    pop bx
    ret

; Data, two null-terminated ASCII strings
HELLO_MSG:
  db 'Hello, World!', 0
GOODBYE_MSG:
  db 'Goodbye', 0

; Padding and magic number
times 510 - ($-$$) db 0
dw 0xaa55
```

All BIOS programming is done in 16-bit real mode, meanining that
maximum address I can reference is `0xffff` and instead of working with
registers called `rax`, `rbx`, `rdx`, and so on, I have to work with `ax`, `bx`,
and `dx`, respectively.

The boot sector starts at `0x7c00`. Nobody but old people care why. `[org
0x7c00]` seems to be NASM specific assembly directive, I don't know how to do
that in GAS or MASM. `print_string` prints whatever it is stored in `bx`.

The `$` refers to the current address, so `jmp $` will jump to itself. `times
510 - ($-$$) db 0` is padding with `0` the remaining part. I don't have to do
it, but it makes things cleaner. Here is how it works: first, I'm subtracting
from `510` instead of `512`, even though the boot sector is 512 bytes, because
the last two bytes of the boot sector have to be `0x55 0xaa`, so that BIOS knows
it's a boot sector. From the SeaBIOS code:
```c
/* src/std/disk.h */
#define MBR_SIGNATURE 0x55aa
/* src/boot.c */
if (checksig) {
    struct mbr_s *mbr = (void*)0;
    if (GET_FARVAR(bootseg, mbr->signature) != MBR_SIGNATURE) {
        printf("Boot failed: not a bootable disk\n\n");
        return;
    }
}
```
It is written as `0x55aa` even though the actual signature is `0xaa 0x55`
because of little-endianness.

The `($-$$)` evaluates to however many bytes I have written so far, `$$` being
the `org`, which I set to `0x7c00`. And `db 0` is the value I'm writing.

Here is how to compile and run:
```fish
nasm -f bin game.asm -o bootsector.bin
qemu-system-i386 -monitor stdio -drive file=bootsector.bin,format=raw
```
The option `-monitor stdio` is useful for quitting by typing the `q` command. Otherwise the
qemu window might capture your cursor and you'll have to gain back the control
with `Ctrl+Alt+g`.

Now I'd like to fill the whole screen with some background color. Let's say
pink. For that I'll have to switch to the VGA 256 colors mode.

```diff
-mov ax, 0x0   ; set video mode
+mov ax, 0x13  ; set VGA video mode
 int 0x10      ; think of it as function call interruput(ax)

+mov ax, 0xa000
+mov es, ax
+
+mov bx, 0
+bgloop:
+  mov BYTE [es:bx], 0x5
+  inc bx
+  cmp bx, 65535  ; 2**16 - 1
+  jb bgloop      ; jump until overflow
+
 mov bx, HELLO_MSG
 call print_string
```

`es` is a segment register and cannot be set directly. So I'm settings it via
`ax` to `0xa000`, which by the nature of segment register will actually set it
to `0xa000 * 0x10 = 0xa0000`. After that I'm able to write to the video memory
by using `bx` as an offset:
```nasm
mov BYTE [es:bx], 0x5 ; set to pink
```
Running it:

![Pink background hello world](/pink.png)

Now draw a line somewhere in the middle:

```diff
+%define WIDTH 320
+%define HEIGHT 200
+%define ROW 64
+
 [org 0x7c00]  ; start writing code in the boot sector

 mov ax, 0x13  ; set VGA video mode
@@ -13,6 +17,13 @@ bgloop:
   cmp bx, 65535  ; 2**16 - 1
   jb bgloop      ; jump until overflow

+mov bx, WIDTH * ROW
+lineloop:
+  mov BYTE [es:bx], 0xf ; white color
+  inc bx
+  cmp bx, WIDTH * ROW + WIDTH
+  jb lineloop
+
 mov bx, HELLO_MSG
 call print_string
```

Result:

![White line somewhere in the middle](/line.png)

To be continued.

### References
---
0. [Writing a Simple Operating System - from Scratch (pdf)](https://www.cs.bham.ac.uk/~exr/lectures/opsys/10_11/lectures/os-dev.pdf), by Nick Blundell
1. Tsoding's stream series where he builds [github.com/tsoding/pinpog](https://github.com/tsoding/pinpog): [twitch.tv collection](https://www.twitch.tv/videos/441661946?collection=VAcjkyTlqRVXuA&filter=collections&sort=time)
2. Relevant wiki pages:
    * [Boot sector](https://en.wikipedia.org/wiki/Boot_sector)
    * [Booting](https://en.wikipedia.org/wiki/Booting)
    * [SeaBIOS](https://en.wikipedia.org/wiki/SeaBIOS)
    * [Mode 13h](https://en.wikipedia.org/wiki/Mode_13h)
    * [Code page 437](https://en.wikipedia.org/wiki/Code_page_437)
    * [BIOS - osdev](https://wiki.osdev.org/BIOS)
    * [Real mode - osdev](https://wiki.osdev.org/Real_Mode)
3. Master Boot Record, a musician: [masterbootrecord.bandcamp.com](https://masterbootrecord.bandcamp.com/)
4. Writing an OS in Rust: [os.phil-opp.com](https://os.phil-opp.com/)
5. [Boot_Sector_Graphical_Programming_-_Tutorial - XlogicX](https://xlogicx.net/Boot_Sector_Graphical_Programming_-_Tutorial.html)
---
