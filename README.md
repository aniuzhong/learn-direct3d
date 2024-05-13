# Learn Direct3D

## d3d9_hello_triangle

## d3d9_hello_triangle_indexed

- Make the triangle rotate.
``` C++
static float degree = 0.0f; degree += 1.0f;
D3DXMATRIX matRotateY;
D3DXMatrixRotationY(&matRotateY, pi * (degree / 180.0));
d3ddev->SetTransform(D3DTS_WORLD, &matRotateY);
```

- Make the triangle rotate in the other direction.
``` C++
static float degree = 0.0f; degree -= 1.0f;
D3DXMATRIX matRotateY;
D3DXMatrixRotationY(&matRotateY, pi * (degree / 180.0));
d3ddev->SetTransform(D3DTS_WORLD, &matRotateY);
```

- As the triangle rotates, make it lift off like a rocket.
``` C++
static float degree = 0.0f; degree += 1.0f;
D3DXMATRIX matRotateY;
D3DXMatrixRotationY(&matRotateY, pi * (degree / 180.0));

static float y = 0.0f; y += 0.01f;
D3DXMATRIX matTranslation;
D3DXMatrixTranslation(&matTranslation, 0, y, 0);

D3DXMATRIX matTransformation = matRotateY * matTranslation;
d3ddev->SetTransform(D3DTS_WORLD, &matTransformation);
```

- Extend the far view-plane so that the rocket does not disappear.
``` C++
D3DXVECTOR3 vec3Eye(0.0f, 0.0f, 100.0f);    // the camera position
D3DXMatrixPerspectiveFovLH(&matProjection,
                            D3DXToRadian(45),    // the horizontal field of view
                            (FLOAT)SCREEN_WIDTH / (FLOAT)SCREEN_HEIGHT, // aspect ratio
                            1.0f,    // the near view-plane
                            10000.0f);    // the far view-plane
```

- Make the triangle lie flat while it rotates.
``` C++
D3DXMATRIX matRotateY, matRotateX;
static float degree = 0.0f; degree += 1.0f;
D3DXMatrixRotationY(&matRotateY, pi * (degree / 180.0));
D3DXMatrixRotationX(&matRotateX, pi * (-89 / 180.0));
```

- Make the triangle lie flat while it rotates.
``` C++
D3DXMATRIX matRotateZ;
static float degree = 0.0f; degree += 1.0f;
D3DXMatrixRotationZ(&matRotateZ, pi * (degree / 180.0));
```


## d3d9_transform_triangle_save_raw

This program shows how to capture the screen in `d3d9_transform_triangle`, a rotating triangle, to raw bitmap in memory without using `D3DXSaveSurfaceToFile` (which is used in `d3d9_transform_triangle_save_png`). This program is referenced from a [Q&A on StackExchange](https://gamedev.stackexchange.com/questions/41958/how-to-capture-the-screen-in-directx-9-to-a-raw-bitmap-in-memory-without-using-d). The Maximus Minimus' answer didn't give a specific implementation, so I finished it here.

**IDirect3DDevice9::GetBackBuffer**: Get access to the IDirect3DSurface9 representing the back buffer.
``` C++
LPDIRECT3DSURFACE9 pBackBuffer = nullptr;
d3ddev->GetBackBuffer(0, 0, D3DBACKBUFFER_TYPE_MONO, &pBackBuffer);
```

**IDirect3DSurface::GetDesc**: Get the description of the back buffer surface.
``` C++
D3DSURFACE_DESC desc;
pBackBuffer->GetDesc(&desc);
```

**IDirect3DDevice9::CreateOffscreenPlainSurface**: Create a new surface object in D3DPOOL_SCRATCH with the same width, height and format.
``` C++
LPDIRECT3DSURFACE9 pOffscreenPlainSurface = nullptr;
d3ddev->CreateOffscreenPlainSurface(desc.Width, desc.Height, desc.Format, D3DPOOL_SYSTEMMEM,
                                    &pOffscreenPlainSurface, NULL);
```

**IDirect3DDevice9::GetRenderTargetData**: Copy from the back buffer surface to the offsceen plain surface.
``` C++
d3ddev->GetRenderTargetData(pBackBuffer, pOffscreenPlainSurface);
```

**IDirect3DSurface9::LockRect**: Get access to the data in the offscreen plain surface and save it; UnlockRect when done.
``` C++
D3DLOCKED_RECT locked_rect;
pOffscreenPlainSurface->LockRect(&locked_rect, NULL, D3DLOCK_READONLY)
```
