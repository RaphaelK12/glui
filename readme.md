# Welcome to the GLUI User Interface Library

https://github.com/libglui/glui

[![Build Status](https://travis-ci.org/libglui/glui.svg?branch=master)](https://travis-ci.org/libglui/glui)

This distribution contains the latest community-maintained fork of the
GLUI Library, now under the ZLIB license.  

It is based on the GLUI v2.1 beta version from Paul Rademacher plus changes made in _GLUI v2.2_ onwards.

The (original) manual is available: [glui_manual.pdf](doc/glui_manual.pdf). 

The (original) GLUI web page is at http://www.cs.unc.edu/~rademach/glui

# Releases

## Version 2.37, October 15, 2015

- [GLUI 2.37 Downloads](https://github.com/libglui/glui/releases/tag/2.37)
- CMake build added
- Bugfixes

## Version 2.36, November 4, 2007

- [GLUI 2.36 Downloads](https://sourceforge.net/projects/glui/files/Source/2.36/)
- License changed to the more permissive ZLIB license 
  with the blessing of Paul Rademacher, Nigel Stewart, Bill Baxter, 
  John Kew, Orion Sky Lawlor and all other developers.

## Version 2.35, July 7, 2006

- [GLUI 2.35 Downloads](https://sourceforge.net/projects/glui/files/Source/2.35/)
- Applied patch [950354] "Good Idle For Spinners" written by Alain
  Durat.  This makes it so GLUI doesn't suck up 100% of your CPU time
  when nothing is happening.

Many changes submitted by Orion Sky Lawlor.
- Comments: I've added doxygen comments to all of glui.h.  Use "make
  docs" to generate Doxygen in doc/html/index.html.
- Bug fix: rollout actually resizes properly when opened (bin/example5
  demonstrated this bug). 
- Bug fix: scroll bars have a (saner) time-based speed limit.  This is
  visible in scrolling around in bin/example6.
- Appearance: double-buffering can be turned on globally, eliminating
  flicker when, e.g., resizing bin/example5 window.  This is on by
  default; turn double-buffering off in GLUI::init or set
  glui->buffer_mode to GLUI::buffer_front.
- Appearance: the texture used in the GLUI_Rotation
  control sphere is now mipmapped, which is much smoother than
  nearest-neighbor.  It's also a stored (glGenTextures) texture, which
  is much much faster. 
- Optimization: I moved GLUI_Node::add_child_to_control to a more
  linker-friendly location.  Now non-deprecated executables are up to
  100KB smaller on disk.
- Optimization: glui_img's are now 1 byte per pixel instead of 3 ints
  per pixel.  This saves 50KB+ space on disk in the library and each
  executable. 

## Version 2.3, March 22, 2005

- *WARNING*: Introduces some incompatible changes with previous versions!!

- GLUI_String is now a `std::string`
  This is the main source of most incompatibilities, but I felt it was
  a necessary change, because the previous usage of a fixed-sized
  buffer was just too unsafe.  I myself was bitten a few times passing
  a char* buffer of insufficient size into GLUI as a live variable.
  It is still possible to use a char buffer, but it is not recommended.

  If you used GLUI_String before as a live var type, the easiest way
  to get your code compiling again is to change those to "char
  buf[300]".  The better way, though, is to update your code to treat
  it as a std::string.

  For instance, if you used to pass mystr to functions that take
  'const char*', now use mystr.c_str() method, instead.
  If you used strcpy(mystr, b) to set the value, now just do mystr=b.
  If you used sprintf(mystr,...) to set the value, now do 
  glui_format_string(mystr,...).
  If you used to clear the string with mystr[0]='\0', now just clear
  it with mystr="".

- Enhanced GLUI_EditText
  Control keys can be used for navigation and control.  The bindings
  are bash-like: Ctrl-B for previous char, Ctrl-F for forward char, etc.
  bindings.  Also control keys that aren't bound to commands are
  simply ignored, whereas before they would be inserted as invisible
  characters.

- Added GLUI_CommandLine class
  This is a GLUI_EditText with a history mechanism.

- New, more object oriented construction API.
  Now instead of calling 

    glui->add_button_to_panel( panel, "my button", myid, mycallback );

  you should just call the button constructor:

    new GLUI_Button( panel, "my button", myid, mycallback );

  And similarly to add it to a GLUI instead of a panel, rather than:

    glui->add_button( glui, "my button", myid, mycallback );

  just call the constructor with the GLUI as the first argument:

    new GLUI_Button( glui, "my button", myid, mycallback );
    
  The old scheme is now deprecated, but still works.  The benefit of
  this new scheme is that now the GLUI class doesn't have to know
  about all the different types of GLUI_Controls that exist.
  Previously GLUI had to both know about all the controls, and know
  how to initialize them.  Now the responsibility for initialization
  belongs to the GLUI_Control subclasses themselves, where it
  belongs. Additionally it means that you can create your own
  GLUI_Control subclasses which will be on equal footing with the
  built-in controls, whereas before any user-created controls would
  always be "second-class citizens" since they would have to be
  constructed differently from the built-ins.

- Removed need for type-declaring arguments when argment type suffices.
  This effects GLUI_Spinner and GLUI_EditText (and GLUI_CommandLine?).

  For example, instead of calling 

    new GLUI_Spinner( glui, "myspin", GLUI_SPINNER_INT, &live_int_var );

  you can just omit the GLUI_SPINNER_INT part, because the type of the
  live_int_var tells the compiler which type you want.

    new GLUI_Spinner( glui, "myspin", &live_int_var );

  If you're not using a live, var, you can still use the
  GLUI_SPINNER_INT type argument.  See glui.h for all the new 
  constructor signatures.  Note this only works with the new
  construction API, not with the old "add_blah_to_panel" style of
  API.

- GLUI_Rotation uses your matrix live-variable now.
  GLUI used to ignore the matrix in your live variable.  This version
  doesn't ignore it, so you'll need to set it to the identity matrix
  yourself if that's what you want it to start as.  There could
  probably be some improvements to this API, though.
  
- Improvements to 'const' usage.
  Most char*'s in GLUI functions used to be non-const even when the
  functions did not modify the string.  I changed everywhere
  appropriate to use const char* instead.

- Updated license info in the headers
  Paul's web page says that GLUI is LGPL, but that wasn't declared in
  the code itself.  I've modified all the headers with the standard
  LGPL notice.

- Updated examples for the API changes

- Created project files for Visual Studio .NET (MSVC7.1)

That's about it.  Enjoy!

If you find yourself with too much time on your hands, the things I
think would be most useful for future improvements to GLUI would be:

1. The GLUI_TextBox and GLUI_Tree definitely need some work, still.  
2. Clipboard integration under Windows/X-Win.  I have some code that
   works on Win32 that I once integrated with GLUI, but I lost that 
   version somewhere.  I still have the Win32 clipboard code, though
   if anyone wants to work on integrating it.  I have some X-Win
   clipboard code, too, but I never got it working quite right.
3. Remove the dependency on GLUT, making the connection with window 
   system APIs into a more plug-in/adapter modular design.  
   So e.g. if you want to use GLUT, you'd link with the GLUI lib and a
   GLUI_GLUT lib, and call one extra GLUI_glut_init() function or 
   something.

Definitly consider submitting a patch if you've made some nice improvements
to GLUI.  Hopefully being an LGPL sourceforge project will attract some new
interest to the GLUI project.

Bill Baxter    
baxter
at
cs unc edu

# JOHN KEW'S ADDITIONS (March 2005)

Thanks to John Kew of Natural Solutions Inc.,
there are some new widgets.  These are demonstrated in example6.cpp.

The new widgets are:

* GLUI_Scrollbar - A scrollbar slider widget
* GLUI_TextBox - A multi-line text widget
* GLUI_List - A static choice list
* GLUI_FileBrowser - A simple filebrowser based on GLUI_List
* GLUI_Tree - Hierarchical tree widget
* GLUI_TreePanel - Manager for the tree widget
 
And one other change:

* GLUI_Rollout has optional embossed border 

# PAUL'S ORIGINAL GLUI 2.0/2.1 README

## Welcome to the GLUI User Interface Library, v2.0 beta!

This distribution contains the full GLUI sources, as well as five example
programs.

## Windows

The directory 'msvc' contains a Visual C++ workspace entitled
'glui.dsw'.  To recompile the library and examples, open this
workspace and run the menu command "Build:Batch Build:Build".  The 3
executables will be in the 'bin' directory, and the library in the
'lib' directory.

To create a new Windows executable using GLUI, create a "Win32 Console
Application" in VC++, add the GLUI library (in 'msvc/lib/glui32.lib'),
and add the OpenGL libs:

	glui32.lib glut32.lib glu32.lib opengl32.lib   (Microsoft OpenGL)

Include the file "glui.h" in any file that uses the GLUI library.

## Unix

An SGI/HP makefile is found in the file 'makefile' (certain lines may need 
to be commented/uncommented).

To include GLUI in your own apps, add the glui library to your
makefile (before the glut library 'libglut.a'), and include "glui.h"
in your sources.
