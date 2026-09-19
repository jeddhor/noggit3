# QT 6 PORT NOTICE #
This fork has been modified to build and run with Qt 6 instead of Qt 5. The
build requirements and instructions below describe the Qt 6 version.

# LICENSE #
This software is open source software licensed under GPL3, as found in
the COPYING file.

# DISCORD #
You can follow Noggit's development and get the latest build here: https://discord.com/invite/UbdFHyM

# SCRIPTING #
Noggit can be scripted using the Lua (5.1) programming language. See the [scripting documentation](scripts/docs/README.md).

# BUILDING #
This project requires CMake to be built. It also requires the
following libraries:

* OpenGL
* Qt 6.2 or newer (Core, Gui, Widgets, OpenGL, and OpenGLWidgets)

Further following libraries are required for MySQL GUID Storage builds:

* LibMySQL
* MySQLCPPConn

The following libraries are automatically installed:
* StormLib (by Ladislav Zezula)
* FastNoise2 (scripting only)
* sol2 (scripting only)
* lodepng (scripting only)
* nlohmann/json (scripting only)

## Windows ##
Text in `<brackets>` below are up to your choice but shall be replaced
with the same choice every time the same text is contained.

### MSVC++ ###
Any recent version of Microsoft Visual C++ should work. Be sure to
remember which version you chose as later on you will have to pick
corresponding versions for other dependencies.

### CMake ###
Any recent CMake version >= 3.18 should work. Just take the latest.

### Qt 6 ###
Install Qt 6.2 or newer to `<Qt-install>` using the Qt online installer.

Note that during installation you only need **one** version of Qt and
also only **one** compiler version. If download size is noticably large
(more than a few hundred MB), you're probably downloading way too much.

### LuaJIT ###
_(Not necessary if disabling `NOGGIT_WITH_SCRIPTING`)_
* Clone luajit to `<Lua-install>` using git: `git clone https://luajit.org/git/luajit.git`
* Open up the visual studio command prompt (cmd / powershell does **NOT** work)
  * The program is usually called something like "x64 Native Tools Command Prompt for VS 20xx"
  * Usually enough to search for "x64 native" in the windows start menu
  * It will open a command prompt that looks like the normal windows command prompt, but has a bunch of necessary visual studio variables set.
* Navigate to the `<Lua-install>/src` (with the visual studio command prompt)
* Run "msvcbuild.bat" (with the visual studio command prompt)
* If successful, should give a message similar to `=== Successfully built LuaJIT for Windows/x64 ===`

### Noggit ###
* open CMake GUI
* set `CMAKE_PREFIX_PATH` (path) to `"<Qt-install>"`,
  e.g. `"C:/Qt/6.8.3/msvc2022_64"`
* set `CMAKE_INSTALL_PREFIX` (path) to an empty destination, e.g. 
  `"C:/Users/blurb/Documents/noggitinstall`
* set `LUA_INCLUDE_DIR` to `<Lua-install>/src`
* set `LUA_LIBRARY` to `<Lua-install>/src/lua51.lib`
* configure, generate
* open solution with visual studio
* build ALL_BUILD
* build INSTALL

To launch noggit you will need the following DLLs from Qt loadable. Install
them in the system, or copy them from `C:/Qt/X.X/msvcXXXX/bin` into the
directory containing noggit.exe, i.e. `CMAKE_INSTALL_PREFIX` configured.

* release: Qt6Core, Qt6Gui, Qt6OpenGL, Qt6OpenGLWidgets, Qt6Widgets
* debug: Qt6Cored, Qt6Guid, Qt6OpenGLd, Qt6OpenGLWidgetsd, Qt6Widgetsd

* If using scripting, you will also need to copy `<Lua-install>/src/lua51.dll` to `CMAKE_INSTALL_PREFIX`

## Linux ##

These instructions assume a working directory `<Linux-Build>`, for example `/home/myuser/`

### Dependencies ###

On **Ubuntu** you can install the building requirements using:

```bash
sudo apt install build-essential cmake git ninja-build libbz2-dev libgl-dev zlib1g-dev qt6-base-dev libqt6opengl6-dev
```

### LuaJIT ###

From `<Linux-Build>`, build luajit using:
```bash
git clone https://luajit.org/git/luajit.git
cd luajit
make
sudo make install
cd ..
```

`<Linux-Build>/luajit` should now exist.

### Build Noggit ###

From `<Linux-Build>`, clone Noggit3 (change repo as needed):
```bash
git clone https://github.com/wowdev/noggit3
```

`<Linux-Build>/noggit3` should now exist.

From `<Linux-Build>`, configure and build using the following commands:

```bash
cmake -S noggit3 -B build -G Ninja -DCMAKE_BUILD_TYPE=RelWithDebInfo
cmake --build build --parallel "$(nproc)"
```

For scripting support, also pass `-DNOGGIT_WITH_SCRIPTING=ON`,
`-DLUA_LIBRARIES=<Linux-Build>/luajit/src/libluajit.so`, and
`-DLUA_INCLUDE_DIR=<Linux-Build>/luajit/src` when configuring.

From `<Linux-Build>/build`, if the build pass correctly without errors, you can install the default lua scripts using:
```bash
cp -r ../noggit3/scripts ./bin/scripts
```

You can now go into `<Linux-Build>/build/bin` and run noggit.

Note that `make install` will probably not work.

# Building the Scripting documentation #
To generate the scripting API documentation, install any new version of Node.js and run: 

```
npm i -g typedoc@0.20.28 typedoc-plugin-markdown@3.5.0 typescript@4.0
```

Then, enter the "scripting" directory in this repository and run the following commands:

```
typedoc --disableSources --plugin typedoc-plugin-markdown --hideBreadcrumbs --out docs/api global.d.ts
rm docs/api/README.md
```

# DEVELOPMENT #
Feel free to ask the owner of the official repository
(https://github.com/wowdev/noggit3) for write access or
fork and post a pull request.

There is a bug tracker at https://github.com/wowdev/noggit3/issues which should be used.

Discord, as linked above, may be used for communication.

# CODING GUIDELINES #
Following is an example for file `src/noggit/ui/foo_ban.hpp`. `.cpp` files
are similar.

```cpp
// This file is part of Noggit3, licensed under GNU General Public License (version 3).
 
//! \note Include guard shall be using #pragma once
#pragma once
 
//! \note Use fully qualified paths rather than "../relative". Order
//! includes with own first, then external dependencies, then c++ STL.
#include <noggit/bar.hpp>
 
//! \note Namespaces equal directories. (java style packages.)
namespace noggit
{
  namespace ui
  {
    //! \note Lower case, underscore separated.
    class foo_ban : public QWidget
    {
      Q_OBJECT;
 
    public:
      //! \note Long parameter list. Would be more than 80 chars.
      //! chars. Break with comma in front. Use spaces to be
      //! aligned below the braces.
      foo_ban ( type name
              , type_2 const& name_2
              , type const& name3
              )
        : QWidget (nullptr)
      //! \note Prefer initialization lists over assignment.
        , _var (std::move (name))
      {}

      //! \note Use const where possible. No space between name and
      //! braces when no arguments are given.
      void render() const;
 
      //! \note If you really need getters and setters, your design
      //! might be broken. Please avoid them or consider just a
      //! public data member, or use proper methods with semantics.
      type const& var() const
      {
        return _var;
      }
      void var (type var_)
      {
        _var = std::move (var_);
      }
 
      //! \note Prefer const where possible. If you need to copy, 
      //! just take a `T`. Otherwise prefer taking a `T const&`.
      //! Don't bother when it comes to tiny types.
      baz_type count_some_numbers ( std::size_t begin
                                   , std::size_t end
                                   ) const
      {
        bazs_type bazs;
 
        //! \note Prefer construction over assignment. Prefer
        //! preincrement.
        for (size_t it (begin); it != end; ++it)
        {
          bazs.emplace_back (it);
        }
 
        //! \note Prefer STL algorithms over hand written code.
        assert (!bazs.empty());
        auto const smallest
          (std::min_element (bazs.begin(), bazs.end()));
 
        return *smallest;
      }

    private:
      //! \note Member variables are prefixed with an underscore.
      type _var;
      //! \note Typedef when using complex types. Fully qualify
      //! types.
      using baz_type = type_2;
      using bazs_type = std::vector<baz_type>;
      bazs_type _bazs;
    }
  }
}
```
