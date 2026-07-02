 Number Type Detection (Prime, Even, Odd)

A SystemVerilog-based digital design project that detects whether a 4-bit input number is Prime,Even  or Odd. This repository demonstrates three distinct hardware modeling styles alongside a comprehensive verification environment using SystemVerilog Packages and Interfaces.

 Features & Architecture

This project implements the type detection logic using three structural and behavioral design patterns:
Dataflow Modeling (`primelogic_df`):Built entirely using synthesized NAND gate logic expressions.
Gate-Level Modeling (`primelogic_gl`): Structural gate-level implementation relying exclusively on instantiated NOR gates.
Behavioral Modeling (`primelogic_bh`): Algorithmic implementation using procedural (`always@*`) blocks, (`if-else`) conditionals, and (`for`) loops.

Verification Environment

SystemVerilog Interface: A clean (`connectPorts`) interface to bundle input signals ($N$) and outputs ($P, E, O$).
Object-Oriented Package:Features a reusable (`primelogic_pkg`) package containing class-based gold models (`checkPrime`, `checkEven`, `checkOdd`) utilizing inheritance and virtual functions to automatically cross-verify module outputs against package outputs.

