+++
title = "Writing a game in boot sector"
+++

```
Not for any practical reason really. It's just that I've seen a few people do
it, one of them gave a couple of talks about it, another wrote two books on the
topic and I decided to try writing my own. At this point I don't have any
particular game in mind but it's about the journey and not the destination
anyway and it's not like I'm going to actually *boot* my game on real hardware.
Most of my stuff is using UEFI in non-legacy mode anyway. So like any reasonable
person I'm going to use QEMU.
```

First step is to make QEMU boot my image and instead of loading some operating
system, write some text to the screen and loop forever. For that install `qemu`
and `nasm`. I want to write two strings: 'Hello, world!' followed by 'Goodbye!'.

```nasm
[org 0x7c00]

mov ah, 0x0
mov al, 0x0
int 0x10

mov bx, HELLO_MSG
call print_string

mov bx, GOODBYE_MSG
call print_string

jmp $

print_string:
  push bx
  mov ah, 0xe ; BIOS tele-type output
  loop:
    mov al, [bx]
    test al, al
    jz break
    int 0x10
    add bx, 1
    jmp loop
  break:
    pop bx
    ret

; Data
HELLO_MSG:
  db 'Hello, World!', 0
GOODBYE_MSG:
  db 'Goodbye', 0

; Padding and magic number
times 510 - ($-$$) db 0
dw 0xaa55
```

Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor
incididunt ut labore et dolore magna aliqua. Vestibulum mattis ullamcorper velit
sed ullamcorper. Donec et odio pellentesque diam volutpat commodo sed. Fermentum
posuere urna nec tincidunt praesent semper. Pellentesque diam volutpat commodo
sed egestas egestas. Feugiat nisl pretium fusce id velit ut tortor. Quis blandit
turpis cursus in hac habitasse. Id leo in vitae turpis massa sed elementum.
Augue neque gravida in fermentum et sollicitudin ac orci phasellus. Et egestas
quis ipsum suspendisse ultrices gravida. Vitae elementum curabitur vitae nunc
sed velit dignissim sodales. Venenatis tellus in metus vulputate eu. Ut eu sem
integer vitae justo. Nulla at volutpat diam ut venenatis tellus. Non tellus orci
ac auctor augue mauris. Sem viverra aliquet eget sit amet tellus cras adipiscing
enim. Ut consequat semper viverra nam libero justo laoreet sit amet. Tincidunt
vitae semper quis lectus nulla at volutpat diam ut. Pharetra sit amet aliquam id
diam maecenas ultricies mi. Ac orci phasellus egestas tellus rutrum.

Non blandit massa enim nec dui nunc mattis. Pellentesque elit eget gravida cum
sociis natoque. Amet cursus sit amet dictum sit amet justo donec enim. Malesuada
pellentesque elit eget gravida cum sociis natoque. Cursus turpis massa tincidunt
dui. Ullamcorper eget nulla facilisi etiam dignissim diam. Ultrices mi tempus
imperdiet nulla malesuada. Dolor sit amet consectetur adipiscing elit duis. In
mollis nunc sed id. Eget arcu dictum varius duis at. Maecenas sed enim ut sem.
Nisl tincidunt eget nullam non nisi. Non blandit massa enim nec dui nunc mattis
enim.

Dignissim sodales ut eu sem integer vitae justo. Donec ultrices tincidunt arcu
non. Aliquam vestibulum morbi blandit cursus risus at ultrices mi. Amet
consectetur adipiscing elit duis tristique sollicitudin nibh sit. Sed egestas
egestas fringilla phasellus faucibus scelerisque eleifend. Nulla facilisi etiam
dignissim diam quis enim lobortis. Aliquam eleifend mi in nulla posuere
sollicitudin aliquam ultrices. Diam maecenas ultricies mi eget mauris pharetra
et. Mauris pharetra et ultrices neque ornare aenean. Dolor sed viverra ipsum
nunc aliquet bibendum. Pretium nibh ipsum consequat nisl vel pretium lectus. Ac
auctor augue mauris augue neque gravida. Tortor posuere ac ut consequat semper
viverra. Semper risus in hendrerit gravida rutrum. Sit amet massa vitae tortor.
In est ante in nibh mauris. Diam volutpat commodo sed egestas egestas. Tincidunt
tortor aliquam nulla facilisi cras fermentum odio eu feugiat. Vitae suscipit
tellus mauris a diam maecenas sed enim. Felis imperdiet proin fermentum leo vel
orci porta.

Sit amet aliquam id diam maecenas ultricies mi eget. Non arcu risus quis varius.
Nisl nisi scelerisque eu ultrices vitae auctor. Felis donec et odio pellentesque
diam volutpat commodo. Nunc sed blandit libero volutpat sed. Sodales ut eu sem
integer vitae justo eget magna fermentum. Ultrices mi tempus imperdiet nulla
malesuada pellentesque elit eget gravida. Neque egestas congue quisque egestas
diam in. In dictum non consectetur a erat nam at lectus urna. Pellentesque elit
ullamcorper dignissim cras. Ultrices sagittis orci a scelerisque purus semper
eget. Egestas integer eget aliquet nibh.

Lorem dolor sed viverra ipsum nunc aliquet bibendum enim. Suspendisse sed nisi
lacus sed viverra tellus in hac. Adipiscing elit duis tristique sollicitudin
nibh sit. Cras sed felis eget velit. Orci phasellus egestas tellus rutrum tellus
pellentesque. Tortor id aliquet lectus proin nibh nisl. Cras sed felis eget
velit aliquet sagittis. Est ultricies integer quis auctor elit sed vulputate mi.
Aenean pharetra magna ac placerat vestibulum. Morbi enim nunc faucibus a
pellentesque. Facilisis mauris sit amet massa vitae tortor condimentum lacinia.
Nisl suscipit adipiscing bibendum est ultricies. Accumsan sit amet nulla
facilisi. Viverra orci sagittis eu volutpat odio facilisis. Non curabitur
gravida arcu ac tortor dignissim convallis aenean et. Est sit amet facilisis
magna. Lectus urna duis convallis convallis tellus. Ullamcorper morbi tincidunt
ornare massa eget.

### References
--------------------------------------------------------------------------------
[Writing a Simple Operating System -- from
Scratch](https://www.cs.bham.ac.uk/~exr/lectures/opsys/10_11/lectures/os-dev.pdf),
by Nick Blundell

Tsoding's stream series where he builds
[pinpog](https://github.com/tsoding/pinpog):
[twitch.tv](https://www.twitch.tv/videos/441661946?collection=VAcjkyTlqRVXuA&filter=collections&sort=time)

Relevant wikipedia pages:

  * [Boot sector](https://en.wikipedia.org/wiki/Boot_sector)

Master Boot Record, a musician: https://masterbootrecord.bandcamp.com/
