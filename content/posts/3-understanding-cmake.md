+++
date = '2026-04-03T20:07:40+07:00'
title = 'Understanding CMake'
tags = ['cmake', 'build systems']
+++

## TL;DR

* Build systems exist to manage complexity as projects scale (multiple files, machines, and languages)
* CMake is a **meta-build system** that focuses on *targets*, not files
* `compile_commands.json` reveals what actually gets compiled and how
* `find_package` is **path-driven**, not magic — location and structure matter

---

## Why I Started Looking Into This

At first, compiling code feels simple.

You just run:

```sh
gcc main.c
```

Or:

```sh
javac Main.java
```

That works — until it doesn’t.

As the project grows:

* more files
* more dependencies
* more engineers
* different machines

Things start breaking in subtle ways.

I thought:

> Why not just use shell scripts?

But that quickly becomes painful:

* hard to debug
* no clear dependency graph
* slow and messy at scale

---

## Initial Questions

**Question:** Why do we even need a build system?

Because compilers alone don’t scale with project complexity.

**Question:** Why does Go feel different?

The Go toolchain already acts as a **complete build system**:

* dependency management
* compilation
* caching

So it hides a lot of complexity that tools like CMake expose.

---

## Mental Model

A simple way to think about it:

* **Compiler (gcc, javac)** → compiles one unit
* **Build system** → manages:

  * dependencies
  * order of compilation
  * multi-module projects

CMake goes one step further:

> It doesn’t build directly — it **generates build systems**

---

## Key Concepts (Discovered)

### Targets

CMake revolves around **targets**, not files.

```cmake
add_executable(app main.cpp)
add_library(lib light.cpp)
```

Everything is attached to a target:

* include paths
* compile flags
* dependencies

---

### Commands

Common ones:

* `add_executable`
* `add_library`
* `target_link_libraries`
* `include_directories`

These define how the build graph is constructed.

---

## What Actually Happens (Under the Hood)

CMake generates a file called:

```text
compile_commands.json
```

Example:

```json
{
  "directory": "/home/.../build/src",
  "command": "/usr/bin/c++ -I/usr/include/opencv4 -isystem /opt/pylon/include ...",
  "file": "light.cpp"
}
```

This is extremely useful — it shows the *real compiler invocation*.

---

### `-I` vs `-isystem`

From observation:

* `-I` → user-controlled headers
* `-isystem` → system / external headers

Key difference:

* warnings from `-isystem` are usually **suppressed**

Insight:

> CMake treats dependencies differently based on how you link them.

---

## Experiments & Findings

The most confusing part was:

```cmake
find_package(TestTestTest REQUIRED)
```

It felt like magic.

So I tested it.
* Use `--debug-find` to understand `find_package`

---

### Experiment 1: Flat Structure

```
/opt/testtesttest/
├── testtesttest-config.cmake
└── share/
```

Result:

* CMake finds the config file directly in `/opt/testtesttest/`

---

### Experiment 2: Nested Structure

```
/opt/testtesttest/
└── share/testtesttest/testtesttest-config.cmake
```

Result:

* CMake searches deeper
* eventually finds it in `/share/testtesttest/`

---

### Experiment 3: Both Exist

```
/opt/testtesttest/
├── testtesttest-config.cmake
└── share/testtesttest/testtesttest-config.cmake
```

Result:

* CMake picks the **top-level one first**

Insight:

> There is a clear **search priority order**

---

### Experiment 4: Using HINTS

```cmake
find_package(TestTestTest REQUIRED HINTS "/opt/testtesttest/share")
```

Result:

* Search path changes
* CMake prioritizes the hinted directory

Insight:

> You can override search behavior explicitly

---

## Insights

From all experiments:

* `find_package` is **not magic**
* it follows a deterministic search path
* structure of installation matters a lot

Typical patterns include:

```
<prefix>/lib/cmake/<name>/
<prefix>/share/<name>/
```

So where you install your package directly affects discoverability.

---

## Notes on Go vs CMake

Go feels simpler because:

* build system is built-in
* dependency resolution is automatic

CMake exposes the complexity explicitly:

* more flexible
* but also more confusing

---

## Citations
* https://bazel.build/basics/build-systems
* https://cmake.org/cmake/help/book/mastering-cmake/index.html
* https://cmake.org/cmake/help/latest/guide/tutorial/index.html
* https://cliutils.gitlab.io/modern-cmake/modern-cmake.pdf
* https://cmake.org/cmake/help/latest/command/find_package.html