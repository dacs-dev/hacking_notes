Podemos ejecutar un programa con gdb de la siguiente manera:

```bash
gdb program_name
```

Y obtener el codigo de ensamblador de una forma mas legible con `dissasemble` y la funcion a desensamblar, si apretamos TAB veremos una lista don todas las posibilidades.

```gdb
disassemble main
```

En caso de querer forzar una sintaxis concreta podemos usar
```gdb
set disassembly-flavor intel
# y despues desensamblamos otra vez
disassemble main
```

Ejemplo de vista sin intel syntax: 
```asm
 0x00000000004005bd <+0>:	push   %rbp
   0x00000000004005be <+1>:	mov    %rsp,%rbp
   0x00000000004005c1 <+4>:	sub    $0x10,%rsp
   0x00000000004005c5 <+8>:	mov    %edi,-0x4(%rbp)
   0x00000000004005c8 <+11>:	mov    %rsi,-0x10(%rbp)
   0x00000000004005cc <+15>:	cmpl   $0x2,-0x4(%rbp)
   0x00000000004005d0 <+19>:	jne    0x400623 <main+102>
   0x00000000004005d2 <+21>:	mov    -0x10(%rbp),%rax
   0x00000000004005d6 <+25>:	add    $0x8,%rax
   0x00000000004005da <+29>:	mov    (%rax),%rax
   0x00000000004005dd <+32>:	mov    %rax,%rsi
   0x00000000004005e0 <+35>:	mov    $0x4006c4,%edi
   0x00000000004005e5 <+40>:	mov    $0x0,%eax
   0x00000000004005ea <+45>:	call   0x400490 <printf@plt>
   0x00000000004005ef <+50>:	mov    -0x10(%rbp),%rax
   0x00000000004005f3 <+54>:	add    $0x8,%rax
   0x00000000004005f7 <+58>:	mov    (%rax),%rax
   0x00000000004005fa <+61>:	mov    $0x4006da,%esi
   0x00000000004005ff <+66>:	mov    %rax,%rdi
   0x0000000000400602 <+69>:	call   0x4004b0 <strcmp@plt>
   0x0000000000400607 <+74>:	test   %eax,%eax
   0x0000000000400609 <+76>:	jne    0x400617 <main+90>
   0x000000000040060b <+78>:	mov    $0x4006ea,%edi
   0x0000000000400610 <+83>:	call   0x400480 <puts@plt>
   0x0000000000400615 <+88>:	jmp    0x40062d <main+112>
   0x0000000000400617 <+90>:	mov    $0x4006fa,%edi
   0x000000000040061c <+95>:	call   0x400480 <puts@plt>
   0x0000000000400621 <+100>:	jmp    0x40062d <main+112>
   0x0000000000400623 <+102>:	mov    $0x400701,%edi
   0x0000000000400628 <+107>:	call   0x400480 <puts@plt>
   0x000000000040062d <+112>:	mov    $0x0,%eax
   0x0000000000400632 <+117>:	leave
   0x0000000000400633 <+118>:	ret
```

Ejemplo usando intel syntax:

```nasm
0x00000000004005bd <+0>:	push   rbp
   0x00000000004005be <+1>:	mov    rbp,rsp
   0x00000000004005c1 <+4>:	sub    rsp,0x10
   0x00000000004005c5 <+8>:	mov    DWORD PTR [rbp-0x4],edi
   0x00000000004005c8 <+11>:	mov    QWORD PTR [rbp-0x10],rsi
   0x00000000004005cc <+15>:	cmp    DWORD PTR [rbp-0x4],0x2
   0x00000000004005d0 <+19>:	jne    0x400623 <main+102>
   0x00000000004005d2 <+21>:	mov    rax,QWORD PTR [rbp-0x10]
   0x00000000004005d6 <+25>:	add    rax,0x8
   0x00000000004005da <+29>:	mov    rax,QWORD PTR [rax]
   0x00000000004005dd <+32>:	mov    rsi,rax
   0x00000000004005e0 <+35>:	mov    edi,0x4006c4
   0x00000000004005e5 <+40>:	mov    eax,0x0
   0x00000000004005ea <+45>:	call   0x400490 <printf@plt>
   0x00000000004005ef <+50>:	mov    rax,QWORD PTR [rbp-0x10]
   0x00000000004005f3 <+54>:	add    rax,0x8
   0x00000000004005f7 <+58>:	mov    rax,QWORD PTR [rax]
   0x00000000004005fa <+61>:	mov    esi,0x4006da
   0x00000000004005ff <+66>:	mov    rdi,rax
   0x0000000000400602 <+69>:	call   0x4004b0 <strcmp@plt>
   0x0000000000400607 <+74>:	test   eax,eax
   0x0000000000400609 <+76>:	jne    0x400617 <main+90>
   0x000000000040060b <+78>:	mov    edi,0x4006ea
   0x0000000000400610 <+83>:	call   0x400480 <puts@plt>
   0x0000000000400615 <+88>:	jmp    0x40062d <main+112>
   0x0000000000400617 <+90>:	mov    edi,0x4006fa
   0x000000000040061c <+95>:	call   0x400480 <puts@plt>
   0x0000000000400621 <+100>:	jmp    0x40062d <main+112>
   0x0000000000400623 <+102>:	mov    edi,0x400701
   0x0000000000400628 <+107>:	call   0x400480 <puts@plt>
   0x000000000040062d <+112>:	mov    eax,0x0
   0x0000000000400632 <+117>:	leave
   0x0000000000400633 <+118>:	ret
```