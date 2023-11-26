---
layout: post
title: Binary Patching
---


Binary patching is an intimidating sounding topic. It is very low level and requires lots of fiddly little knowledge.

# Replacing and Deleting Code

Our first example is trying to patch a simple constant change.

```C
int foo(int x){
    return x + 1;
}

int foo_patched(int x){
    return x + 2;
}
```

```bash
echo "
int foo(int x){
    return x + 1;
} " > /tmp/foo.c
gcc -O1 /tmp/foo.c -c -o /tmp/foo.o
objdump -d -F /tmp/foo.o # -F shows offsets which helps us find the right place to patch
```

```asm
0000000000000000 <foo> (File Offset: 0x40):
   0:   f3 0f 1e fa             endbr64 
   4:   8d 47 01                lea    0x1(%rdi),%eax
   7:   c3                      ret    
```

Looking at it, I suspect that 0x1 corresponds to the 1 in the binary.
We can confirm by using the assembler to change that instruction and look at the output binary

```bash
echo "lea    0x2(%rdi),%eax" | as -o /tmp/toy - 
objdump -d /tmp/toy
```

We can then patch using `xxd`

```bash
echo "0000044: 8d4702" > /tmp/foopatchfile
xxd -r /tmp/foopatchfile /tmp/foo.o
objdump -d /tmp/foo.o
```

Using C compiler to generate helpful assembly.

The basic questions are:

1. Where to read info you need
2. Where writes need to go
3. What cannot be clobbered.

C compilers do not compile program fragments. They must be in function bodies. Nevertheless it can be very illuminating

If your variables need to read from registers, make them arguments to functions.
Make globals for things that need to be read from memory or use varargs to see how to read from the stack.
If your variables need to be written to registers, make them arguments to called functions.

In this way, you can get a starting point of decent assembly code you can manipulate.

Suggestions on how to be more careful
Save the objdump. Edit it to match your expectations. Do your patching, then diff this objdump with your intended objdump.

Suggestions on maintenance: Add a section to the patched binary documenting your edit.

## Tools

Ghidra makes for a much nicer experience

Hex editors mean you don't have to fiddle with `xxd`. VS Code has one for example.

You can also use `xxd` to dump the entire hex file, edit it, and then rebinarize it.

```bash
xxd -s 0x40 -l 8 /tmp/foo.o > /tmp/foo.o.hex
nano /tmp/foo.o.hex
```

## Fusing out a password check

```C
int patch_fun(char *passwd){
    if(strcmp(passwd, "MyGoodPassword7") == 0){
        return 0;
    }
    else{
        return -1;
    }
}

int patch_fun(char *passwd){
    if(0 == 0){
        return 0;
    }
    else{
        return -1;
    }
}


int main(){
    char* password = "MyGoodPassword";
    return patch_fun(password);
}
```

## Patching a Call

```C
int ret_3() { return 3; }

int ret_5() { return 5; }

int main() {
  return ret_5();
}

int main_patched() {
  return ret_3();
}
```

## Changing a Type

This is a nightmare.

```C

int foo(int x){
  if(x > 0){
    return 2;
  }
  else{
    return 1;
  }
}

int foo(unsigned int x){
  if(x > 0){
    return 2;
  }
  else{
    return 1;
  }
}

int main() {
  return foo(-1);
}
```

# Adding Code

We could make this harder. Now there won't be code to twiddle. Really we are adding functionality.

```bash
echo "
int foo(int x){
    return x;
}

int foo_patched(int x){
    return x + 1;
} 

int main(){
    return foo(5);
}
" > /tmp/add1.c
gcc /tmp/add1.c -O1 -g -o /tmp/add1
objdump -d /tmp/add1
```

```

Disassembly of section .text:

0000000000000000 <foo>:
   0:   f3 0f 1e fa             endbr64 
   4:   89 f8                   mov    %edi,%eax
   6:   c3                      ret    

0000000000000007 <foo_patched>:
   7:   f3 0f 1e fa             endbr64 
   b:   8d 47 01                lea    0x1(%rdi),%eax
   e:   c3                      ret    
```

We can see that there is more code in `foo_patched` than `foo`. We can't just manipulate some bytes to overwrrite instructions of change constants

```bash
objdump --help
```

```bash
readelf -a /tmp/add1
```

```bash
echo "
// re
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <elf.h>

int main() {
    FILE *file = fopen(\"/tmp/add2\", \"r+b\");
    if (!file) {
        perror(\"Error opening file\");
        return 1;
    }

    // Read the ELF header
    Elf64_Ehdr ehdr;
    fread(&ehdr, sizeof(ehdr), 1, file);

    // Check if it's a valid ELF file
    assert(memcmp(ehdr.e_ident, ELFMAG, SELFMAG) == 0);
    // Check for 64-bit ELF
    assert (ehdr.e_ident[EI_CLASS] == ELFCLASS64);

    // Loop through program headers to find the first PT_LOAD segment
    for (int i = 0; i < ehdr.e_phnum; i++) {
        fseek(file, ehdr.e_phoff + i * ehdr.e_phentsize, SEEK_SET);
        Elf64_Phdr phdr;
        fread(&phdr, sizeof(phdr), 1, file);

        if (phdr.p_type == PT_LOAD && phdr.p_flags == (PF_R | PF_X)) {
            // Increase the segment size by a fixed amount, e.g., 512 bytes
            phdr.p_memsz += 512;
            phdr.p_filesz += 512;

            // Write back the modified program header
            fseek(file, ehdr.e_phoff + i * ehdr.e_phentsize, SEEK_SET);
            fwrite(&phdr, sizeof(phdr), 1, file);
            break;
        }
    }

    fclose(file);
    return 0;
} " > /tmp/re.c
cp /tmp/add1 /tmp/add2
gcc /tmp/re.c -o /tmp/re
/tmp/re
```

```bash
readelf -l /tmp/add1 > /tmp/add1.elf
readelf -l /tmp/add2 > /tmp/add2.elf
diff -C 5 /tmp/add1.elf /tmp/add2.elf
```

Ok, so now we need to jump to the code and then jump back.

<https://c9x.me/x86/html/file_module_x86_id_147.html>

```bash
echo "jmp  . + 0x16" | as -o /tmp/toy - 
objdump -d /tmp/toy
```

We can add patch code after `.fini` at 0x1151, which let's pad t 0x1154 just for good measure (alignmnent).

```bash
objdump -d /tmp/add1
"

0x112D: EB 16
0x1154: 8d 47 01 
        EB back
"
```

### Dynamic Interposition

Some libraries are dynamically linked. These can be patched by using the dynamic linking mechanisms

`LD_PRELOAD`
GOT PLT

<https://github.com/advanced-microcode-patching/shiva>

gdb
ptrace. Ptrace is the mechanism gdb uses.

### Code Caves

Clobber unused code. Maybe require patching that code too. Big enough binaries are full of junk. Get a symbol dump and maybe ask chatgpt.
Maybe just maybe there's a pile of nops somewhere. Look for them
Superoptimize some code that is

Elf padding <https://github.com/draperlaboratory/VIBES/blob/2116e6cbbc7b5881c8d55ea9e7377c0e9170543d/tools/vibes-patch/lib/patcher.ml#L217>
Add new segment

Virus techniques tend to be applicable, but we're using them for good right? Viruses need to find ways to add exectuable code too. We don't care about detection.

You tend to want space closer rather than farther from your patch point

Make the file relinkable via that weird project?

## Add Bounds Check

```C
int patch_fun(int a[],int i){
    return a[i];
}

int patch_fun(int a[],int i){
    if(i < 3 && i >= 0){
        return a[i];
    }
    return -1;
}

int main(){
    int x[] = {5,4,3};
    return patch_fun(x, 3);
}
```

## Null Check

```C
#include <stddef.h>


int patch_fun(int *x){
    return *x;
}

int patch_fun(int *x){
    if(x == NULL){
        return -1;
    }
    return *x;
}

int main(){
    int x = 5;
    return patch_fun(&x);
}
```

# Checking

diff readelf

what can go wrong?

models of loading

# Resources

<https://program-repair.org/index.html>

<https://codeflaws.github.io/> Nice chart of small patches
![](https://codeflaws.github.io/images/dtable-1.png)

<https://en.wikipedia.org/wiki/ROM_hacking>
<https://en.wikipedia.org/wiki/Game_Genie>

<https://github.com/joxeankoret/diaphora> diaphora diffing tool
[A Method to Evaluate CFG Comparison Algorithms](http://cfgsim.cs.arizona.edu/qsic14.pdf)

<https://github.com/GJDuck/e9patch>

[Reassembly is Hard: A Reflection on Challenges and Strategies](https://softsec.kaist.ac.kr/~sangkilc/papers/kim-usenix23.pdf)

<https://twitter.com/peter_a_goodman/status/1503016499824537600?s=20&t=1Z4ew6rnGnFiMTSrQJSmKw> goodman on binary rewriting
binrec - lift program merge lifted bytecode into debloated
[egalito](https://ethv.net/static/egalito.pdf)
BOLT
lifting bits/grr

{% raw %}

```python

code = '''
int fact(int x){
  int acc = 1;
  while(x > 0){
    acc *= x;
    x--;
  }
  return acc;
}
'''
def fragment(x, header, invars, outvars):
  return f"""
    vod cb();
    {header}
    void fragment(uint64_t r0, uint64_t r1, uint64_t r2, uint64_t r3, uint64_t r4){{ //varargs
          // inavrs
          {invars}
          // patch code
          {x}
          // stack outvars{outvars}
          cb({outvars});
          return;
    }}
  """
# Hmm. could verify the permutation
def permute(asm, perm):
  # simultaneous replacement
  for k in perm.keys():
    asm = asm.replace(k, "TEMPYTEMPY"+k)
  for k,i in perm.items():
    asm = asm.replace("TEMPYTEMPY"+k, i)
  return asm

def clip_ret(asm):

def used_regs(asm, regs):
  return [reg for reg in regs if asm.find(reg) != -1]


  re.match
import tempfile
import subprocess
import angr #, monkeyhex
import os
with tempfile.NamedTemporaryFile(suffix=".c") as fp:
  with tempfile.TemporaryDirectory() as mydir:
    fp.write(code.encode())
    fp.flush()
    fp.seek(0)
    print(fp.readlines())
    print(fp.name)
    print(mydir)
    outfile = mydir + "/fact"
    print(outfile)
    print(subprocess.run(["gcc",  "-g",  "-c","-O1", "-o",  outfile, fp.name], check=True))
    print(os.listdir(mydir))
```

{% endraw %}

<https://gcc.gnu.org/onlinedocs/gcc/Common-Function-Attributes.html#index-patchable_005ffunction_005fentry-function-attribute>

Adding nops in places that are meant to be patcjable using inline asm

<https://github.com/endrazine/wcc> making executable relocatable

<https://ofrak.com/docs/reference/ofrak/core/elf/load_alignment_modifier.html>

<https://github.com/rems-project/linksem>