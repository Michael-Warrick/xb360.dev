+++
date = '2025-08-09T08:26:57+02:00'
title = 'Initialising Direct3D'
weight = 3
[params]
  math = false
+++

# Initialising Direct3D 9
In this section we will learn how to setup and create a Direct3D 9 ready device.

## Globals
We will begin by defining some global variables. Of course in a production application it would be better practice to scope these variables to classes, structs or namespaces (which we will be doing in later chapters).

```cpp
LPDIRECT3DDEVICE9 g_d3dDevice;
LPDIRECT3DVERTEXBUFFER9 g_vertexBuffer;
LPDIRECT3DVERTEXDECLARATION9 g_vertexDeclaration;
LPDIRECT3DVERTEXSHADER9 g_vertexShader;
LPDIRECT3DPIXELSHADER9 g_pixelShader;

XMMATRIX g_worldMatrix;
XMMATRIX g_projectionMatrix;
XMMATRIX g_viewMatrix;

Time g_Time;
BOOL g_isUsingWidescreenMode = TRUE
```

## Function
Next we’ll write all of our initialisation code in a function called `initDirect3D()`, where the function’s return type will be of `HRESULT` as described in the Direct3D 9 documentation[^1].

```cpp
HRESULT initDirect3D() {
	LPDIRECT3D9 d3d = Direct3DCreate9(D3D_SDK_VERSION);
	if (!d3d) {
		return E_FAIL
	}

	D3DPRESENT_PARAMETERS presentParameters;
	ZeroMemory(&presentParameters, sizeof(presentParameters));

	XVIDEO_MODE videoMode;
	XGetVideoMode(&videoMode);
	g_isUsingWidescreenMode = videoMode.fIsWideScreen;

	presentParameters.BackBufferWidth = min(videoMode.dwDisplayWidth, 1280);
	presentParameters.BackBufferHeight = min(videoMode.dwDisplayHeight, 720);
	presentParameters.BackBufferFormat = D3DFMT_X8R8G8B8;
	presentParameters.BackBufferCount = 1;
	presentParameters.EnableAutoDepthStencil = TRUE;
	presentParameters.AutoDepthStencilFormat = D3DFMT_D24S8;
	presentParameters.SwapEffect = D3DSWAPEFFECT_DISCARD;
	presentParameters.PresentationInterval = D3DPRESENT_INTERVAL_ONE;

	if (FAILED(d3d->CreateDevice(0, D3DDEVTYPE_HAL, NULL, 
								 D3DCREATE_HARDWARE_VERTEXPROCESSING,
								 &presentParameters, &g_d3dDevice))) {
		return E_FAIL;
	}

	return S_OK;
}
```

## Explanation
We start by declaring an `LPDIRECT3D9` which will be our D3D object, checking that it is non-null, confirming it was successfully created.

```cpp
LPDIRECT3D9 d3d = Direct3DCreate9(D3D_SDK_VERSION);
	if (!d3d) {
		return E_FAIL
	}
```

​Next we need to setup our device’s presentation parameters. This is done to inform the graphics driver which hardware features our application requires and which capabilities it expects.

The first step is to zero-out a suitably sized block of memory to hold our `D3DPRESENT_PARAMETERS` structure, if you’re not used to programming for Microsoft systems you can consider `ZeroMemory()`[^2] as Microsoft’s answer to a portable `memset()`.

```cpp
D3DPRESENT_PARAMETERS presentParameters;
ZeroMemory(&presentParameters, sizeof(presentParameters));
```
Now we will create an `XVIDEO_MODE` object, allowing us to query physical information about our display.

```cpp
XVIDEO_MODE videoMode;
XGetVideoMode(&videoMode);
```

With this display information and the presentation parameters structure, we can specify the desired capabilities and behaviour for our Direct3D device.

```cpp
g_isUsingWidescreenMode = videoMode.fIsWideScreen;
		
presentParameters.BackBufferWidth = min(videoMode.dwDisplayWidth, 1280);
presentParameters.BackBufferHeight = min(videoMode.dwDisplayHeight, 720);
presentParameters.BackBufferFormat = D3DFMT_X8R8G8B8;
presentParameters.BackBufferCount = 1;
presentParameters.EnableAutoDepthStencil = TRUE;
presentParameters.AutoDepthStencilFormat = D3DFMT_D24S8;
presentParameters.SwapEffect = D3DSWAPEFFECT_DISCARD;
presentParameters.PresentationInterval = D3DPRESENT_INTERVAL_ONE;
```

Finally, we can now create our Direct3D device.

```cpp
if(FAILED(d3d->CreateDevice(0, D3DDEVTYPE_HAL, NULL,
                            D3DCREATE_HARDWARE_VERTEXPROCESSING,
                            &presentParameters, &g_d3dDevice))) {
	return E_FAIL;                 
}

return S_OK;
```

[^1]: [Direct3DCreate9 function](https://learn.microsoft.com/en-us/windows/win32/api/d3d9/nf-d3d9-direct3dcreate9)
[^2]: [ZeroMemory macro](https://learn.microsoft.com/en-us/previous-versions/windows/desktop/legacy/aa366920(v=vs.85))