# Lua

## NOTES
It's not an official repository.
Check the official site: https://www.lua.org/


## Goal
Change old style Make to modern CMake.
Adapt build for `FetchContent` statement.

## How to build

```bash

cmake -S . -Bout/ -G "Ninja"

cmake --build out/

```

