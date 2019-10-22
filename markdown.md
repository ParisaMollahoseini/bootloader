# Write a simple boot loader

### Some primary information about boot loader

#### The boot process is:

    *Power on*: the PC starts up and begins executing the BIOS code.
    The BIOS looks for various media such as a floppy disk or hard drive.
    The BIOS loads a 512 byte boot sector from the specified media and begins executing it.
    Those 512 bytes then go on to load the OS itself, or a more complex bootloader.

For MikeOS, we have the **512-byte** bootloader, which we write to a floppy disk image file (a virtual floppy). We can then inject that floppy image into a CD, for PCs that only have CD-ROM drives. Either way, the BIOS loads it as if it was on a floppy, and starts executing it. We have control of the system!

#### Now we should see what we need
  -Knowledge about at least **one** programming language
  -installing **linux**
  -*QEMU* PC emulator and the *NASM* assembler
  -Prepare assembly code
  -Building

## *QEMU* PC emulator and the *NASM* assembler

We should use the following code:

```
sudo apt-get install build-essential qemu nasm
```

## Prepare assembly code

First of all, it reads the line with label **Start** which will set some registers and the final string we want to show on the screen .
We want to print **"This is my cool new OS!"**,so its address should be written in SI register .Finally we call **print_string** function .

In **print_string** function :

In this function 0Eh is put in the AH .(We will need this instruction when we interrupt with (int 10h))

In **repeat** function :

1.Data bytes will be loaded in AL by using the location that SI points.
2.Actually one byte of the whole data(our string) shows one character which will be checked after loading. In this way we can understand whether it is end of the string or not.
3.We use interrupt ***(int 10h)*** here to show the character on the screen.
4.This loading of characters will continue until we read zero which means end of string , and will go to ***.done*** function.  

In **done** function :

It will return to the next line of the address which we have **called** the **print_string** function .


## Building

```
nasm -f bin -o myfirst.bin myfirst.asm
```
Here we assemble the code from our text file into a raw **binary** file of machine-code instructions. With the -f bin flag, we tell NASM that we want a plain binary file .
The -o myfirst.bin part tells NASM to generate the resulting binary in a file called myfirst.bin.


We need a virtual floppy disk image to which we can write our bootloader-sized kernel.
Copy mikeos.flp from the **disk_images/** directory of the MikeOS bundle into your home directory, and rename it ***myfirst.flp***

```
dd status=noxfer conv=notrunc if=myfirst.bin of=myfirst.flp
```

dd will directly copy our kernel to the first sector of the floppy disk image.

### At the end

```
qemu-system-i386 -fda myfirst.flp
```

*By this code we can boot our new OS by the QEMU PC emulator*
