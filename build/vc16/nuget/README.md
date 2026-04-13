# The OpenGL Extension Wrangler Library (GLEW)

## Initializing GLEW

First you need to create a valid OpenGL rendering context and call glewInit() to initialize the extension entry points. If glewInit() returns GLEW_OK, the initialization succeeded and you can use the available extensions as well as core OpenGL functionality. For example:

```c
#include <GL/glew.h>
#include <GL/glut.h>
...
glutInit(&argc, argv);
glutCreateWindow("GLEW Test");
GLenum err = glewInit();
if (GLEW_OK != err)
{
  /* Problem: glewInit failed, something is seriously wrong. */
  fprintf(stderr, "Error: %s\n", glewGetErrorString(err));
  ...
}
fprintf(stdout, "Status: Using GLEW %s\n", glewGetString(GLEW_VERSION));
```

## Checking for Extensions

Starting from GLEW 1.1.0, you can find out if a particular extension is available on your platform by querying globally defined variables of the form `GLEW_{extension_name}`:

```c
if (GLEW_ARB_vertex_program)
{
  /* It is safe to use the ARB_vertex_program extension here. */
  glGenProgramsARB(...);
}
```

In GLEW 1.0.x, a global structure was used for this task. To ensure binary compatibility between releases, the struct was replaced with a set of variables.

You can also check for core OpenGL functionality. For example, to see if OpenGL 1.3 is supported, do the following:

```c
if (GLEW_VERSION_1_3)
{
  /* Yay! OpenGL 1.3 is supported! */
}
```

In general, you can check if `GLEW_{extension_name}` or `GLEW_VERSION_{version}` is true or false.

It is also possible to perform extension checks from string input. Starting from the 1.3.0 release, use `glewIsSupported` to check if the required core or extension functionality is available:

```c
if (glewIsSupported("GL_VERSION_1_4  GL_ARB_point_sprite"))
{
  /* Great, we have OpenGL 1.4 + point sprites. */
}
```

For extensions only, `glewGetExtension` provides a slower alternative (GLEW 1.0.x-1.2.x). Note that in the 1.3.0 release `glewGetExtension` was replaced with `glewIsSupported`.

```c
if (glewGetExtension("GL_ARB_fragment_program"))
{
  /* Looks like ARB_fragment_program is supported. */
}
```

## Experimental Drivers

GLEW obtains information on the supported extensions from the graphics driver. Experimental or pre-release drivers, however, might not report every available extension through the standard mechanism, in which case GLEW will report it unsupported. To circumvent this situation, the `glewExperimental` global switch can be turned on by setting it to `GL_TRUE` before calling `glewInit()`, which ensures that all extensions with valid entry points will be exposed.

## Platform Specific Extensions

Platform specific extensions are separated into two header files: `wglew.h` and `glxew.h`, which define the available `WGL` and `GLX` extensions. To determine if a certain extension is supported, query `WGLEW_{extension name}` or `GLXEW_{extension_name}`. For example:

```c
#include <GL/wglew.h>

if (WGLEW_ARB_pbuffer)
{
  /* OK, we can use pbuffers. */
}
else
{
  /* Sorry, pbuffers will not work on this platform. */
}
```

Alternatively, use `wglewIsSupported` or `glxewIsSupported` to check for extensions from a string:

```c
if (wglewIsSupported("WGL_ARB_pbuffer"))
{
  /* OK, we can use pbuffers. */
}
```
