+++
date = '2025-08-09T08:26:57+02:00'
title = 'General Structure'
weight = 2
[params]
  math = true
+++

# General Structure

In this section, we discuss the general structure of our game which we will use as a base for future chapters.

## Entry Point

On Xbox 360, the runtime environment looks for a `void` function named `main` with no arguments and a `__cdecl`[^1] calling convention as the application’s entry point. This reflects the fixed execution model of the Xbox’s OS, which—unlike desktop Windows—does not provide command-line arguments or environment variables. This is similar to how embedded or tool-style Windows programs operate, as seen in Microsoft's own Cryptography API examples[^2].

```cpp
void __cdecl main() {
	// ...
}
```

## Structures

In our example code, we include two structures: `Vertex` and `Time`. These structs facilitate encapsulation of data, without hiding key implementation details.

```cpp
struct Vertex {
	float position[3];
	DWORD color;
};

struct Time {
	LARGE_INTEGER previousFrameTime;
	LARGE_INTEGER totalElapsedTicks;
	
	float totalTimeSinceLaunch;
	float deltaTime;
	float secondsPerTick;
};
```

`Vertex` is used to help marshal data between the Xbox 360’s CPU and GPU, and `Time` is used for general time bookkeeping and is very useful for physics/rendering updates.

## Pre-Compiled Header

When developing a title for the Xbox 360 it was highly recommended to use a pre-compiled header in an effort to reduce compilation times. By default, the pre-compiled header file’s name will be `stdafx.h` and is intended for including frequently used but rarely changed headers, such as system headers. This header is accompanied by a source file with a similar name - `stdafx.cpp` which is used to simply include the header file.

Ensure `stdafx.h` is included above all other includes in every source file, for example:

```cpp
// Renderer.cpp
#include "stdafx.h"
#include "Renderer.hpp"

Renderer::Renderer() {
		// ...
}
```

In the source code just shown, we are imagining that we have a Renderer class split into an interface and implementation (header and source) file.

## Implementation Rationale

In the first chapters, code will be written in a single `main.cpp` file. This way the usefulness of the code is hopefully maximised, and as the code we write begins to grow in size and complexity, we will make suggestions for classes/structures to help better organise the code.

[^1]: [__cdelc: Microsoft Learn Documentation](https://learn.microsoft.com/en-us/cpp/cpp/cdecl?view=msvc-170)
[^2]: [CryptoAPI: Retrieving an Issued Certificate from Active Directory](https://learn.microsoft.com/pl-pl/windows/win32/seccrypto/retrieving-an-issued-certificate-from-the-active-directory)