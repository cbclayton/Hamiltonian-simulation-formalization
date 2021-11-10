# Hamiltonian Simulation Formulation

As our final project, we will formalize the *Hamiltonian simulation algorithm* in Coq.

### Team

In alphabetical order by last name:
* Connor Clayton
* Yi (Ethan) Lee
* Manasi Shingane

## What is Hamiltonian simulation?

Hamiltonian simulation methods are a widely studied area and have been used to efficiently simulate physical quantum systems 
and to construct quantum algorithms that rely on Hamiltonian dynamics. A Hamiltonian H(t) of a system is an operator that represents the total energy of a given quantum system. We also require that 
H(t) is Hermitian to ensure the energies are real valued. The eigenvalue for a specific eigenstate of H(t) represent the energy of the state represented by the eigenstate. The ground state of a 
Hamiltonian corresponds to the eigenstate with the smallest eigenvalue. This represents the state with the lowest energy level. Finding the ground state of a Hamiltonian is a particularly useful problem 
used in many quantum applications (i.e. quantum verification, quantum algorithms, etc.) and is generally difficult for even quantum computer. "Simulating" a Hamiltonian can refer to finding the ground state
of a Hamiltonian, but can also refer to describing a quantum system at a given time t. In quantum mechanics, a Hamiltonian can be used describe the time evolution of the wave function through the Schrodinger equation. Namely, we can describe a wave function,
![phi-t](http://chart.apis.google.com/chart?cht=tx&chl={\quad}|\phi(t)\rangle{\quad})
through the Schrodinger equation:
![schrodinger-eq](http://chart.apis.google.com/chart?cht=tx&chl={\quad}i\hbar|\phi'(t)\rangle=H(t)|\phi(t)\rangle{\quad}), 
where ![hbar](http://chart.apis.google.com/chart?cht=tx&chl=\hbar) is Planck's constant. Given the intial wave function at time
![teq0](http://chart.apis.google.com/chart?cht=tx&chl={\quad}t=0{\quad}),
we can solve this differential equation to find the wave function at any later time t. 

Hamiltonians can be time independent, or time dependent. In this work, we will only consider time-independent Hamiltonians. For time independent hamiltonians, the solution of the Schrodinger equation is
![sol-ti](http://chart.apis.google.com/chart?cht=tx&chl={\quad}|\phi(t)\rangle=U(t)|\phi(t)\rangle{\quad})
where the unitary
![sol-U](http://chart.apis.google.com/chart?cht=tx&chl={\quad}e^{-iHt/\hbar}{\quad}). 
A Hamiltonian H is efficiently simulatable if for any t > 0, &epsilon; > 0, there exists a polynomial-sized quantum circuit C such that
![sol-circuit](http://chart.apis.google.com/chart?cht=tx&chl={\quad}\mid|C-H(t)\mid|<\varepsilon{\quad}). In our work, we will formall prove 
properites of Hamiltonians and their efficient simulation. 

## Relevant works

Notable work on formally verified quantum computing using Coq has been done before, notably in [this library](https://rand.cs.uchicago.edu/vqc/) by Robert Rand, [SQIR/VOQC](https://github.com/inQWIRE/SQIR), and [QWIRE](https://github.com/inQWIRE/QWIRE). The first library implements the basics of quantum computation including qubits, measurements, and small quantum circuits and the latter three libraries implement many more advanced features. We expect `matrix.v` in QWIRE to be especially helpful as it proves many linear algebra fundamentals.

To the best of our knowledge, formal verification of Hamiltonian simulation semantics is new. Our main contribution will be to prove theorems on the semantics of both commuting and non-commuting Hamiltonians, which will involve formulating a Coq representaion of the exponentiation of a matrix.

## Syntax

Hamiltonian evolutions can be specified using a simple domain-specific language.
We first describe its syntax through an example, then we present its formal grammar.

### Example

The following is a valid Hamiltonian simulation program:

```
Site
    fock "F1"
    qubit "Q1"
    qubit "Q2"
    qubit "Q3" ;
Hamiltonian
    ( "H1" : R1 , "Q1" > X * "Q2" > Z + "Q3" > Y )
    ( "H2" : R1 , "Q2" > Y )
    ( "H3" : R1 , "F1" > c )
```

A program contains two sections: *Site* and *Hamiltonian*.
In our *Site* section, variables are declared. In this example, we have `F1` of type fock, as well as `Q1`, `Q2`, and `Q3` of type qubit.
In the *Hamiltonian* section, the desired physical evolution is specified.
In our example, we have three Hamiltonians, <!-- google charts LaTeX workaround; you hate to see it -->
* ![H1](http://chart.apis.google.com/chart?cht=tx&chl={\quad}H_1=\mathsf{I}{\otimes}\mathsf{X}{\otimes}\mathsf{Z}{\otimes}\mathsf{I}%2B\mathsf{I}{\otimes}\mathsf{I}{\otimes}\mathsf{I}{\otimes}\mathsf{Y}{\quad})
* ![H2](http://chart.apis.google.com/chart?cht=tx&chl={\quad}H_2=\mathsf{I}{\otimes}\mathsf{I}{\otimes}\mathsf{Y}{\otimes}\mathsf{I}{\quad})
* ![H3](http://chart.apis.google.com/chart?cht=tx&chl={\quad}H_3=c{\otimes}\mathsf{I}{\otimes}\mathsf{I}{\otimes}\mathsf{I}{\quad})

which are applied for one unit of time each.

### Formal grammar

* `A`: identifier
* `z`: complex number
* `r`: real number
* `t`: positive real number.

```
Type := "qubit" | "fock"
Operator := Id | X | Y | Z | a | c
Declaration := (T A)*
Scalar := S_1 + S_2 | S_1 * S_2 | S_1 - S_2 | S_1 / S_2 | exp(S) | cos(S) | sin(S) | z | r
TIH := M_1 + M_2 | M_1 * M_2 | S * M | A.O
TIH_Sequence := (A : t, M)*
Program := "Site" Declaration; "Hamiltonian" TIH_Sequence
```

TIH stands for time-independent Hamiltonian.

## Project Goals

First, we will implement the formal syntax, following how `Imp` is parsed in the [Software Foundations textbook](https://softwarefoundations.cis.upenn.edu/plf-current/index.html).
We then either prove some facts on Hamiltonian evolution semantics, or implement compilation to quantum circuits. Both if time permits.

### Semantics

We aim to prove when different Hamiltonians have the same semantics, that is, when they have the same effect on any given state.
In particular, if `H_1` and `H_2` commute, then `(H_1: t_1) (H_2, t_2)` and `(H_2: t_2) (H_1, t_1)` have the same semantics. 

One challenge will be representing matrix exponentials. We plan to define this symbolically (since the formal definition requires an infinite sum) and state valid rewrite rules that respect Schrödinger's equation.

### Compilation

Another goal of this project is to implement and prove facts about Hamiltonian compilation. Given some universal gate set `G` and a Hamiltonian `H`, this involves constructing a program `P : list G` such that `P` and `H` have the same effect on a quantum state.
If time permits, we may attempt to compile the Hamiltonian into a popular quantum programming language such as [OpenQASM](https://github.com/Qiskit/openqasm),
so that it can be run on a simulator or a real quantum computer.
This involves printing out the list of gates in the right syntax and [has been done](https://github.com/inQWIRE/SQIR/tree/main/examples/shor) for Shor's algorithm for integer factorization.
<!-- Ethan: How about the correctness of compilation? Has the formal semantics of qasm been defined anywhere? -->

The method of compilation will be via *trotterization*. Trotterization decomposes the unitary induced by a Hamiltonian into a product of local terms. 
We can then analyze its properties by, for example, proving the error bound on trotterization for non-commuting Hamiltonians.
