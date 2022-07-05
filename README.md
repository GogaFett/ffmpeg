


libmp3lame
cd /ffmpeg_sources && rm -rf lame-3.99.5 && wget -O lame-3.99.5.tar.gz https://deac-riga.dl.sourceforge.net/project/lame/lame/3.99/lame-3.99.5.tar.gz &&
tar xvf lame-3.99.5.tar.gz && rm -f lame-3.99.5.tar.gz && 
cd lame-3.99.5 &&
./configure --host=x86_64-w64-mingw32 --prefix="/usr/x86_64-w64-mingw32/sys-root/mingw" \
--enable-static --disable-shared &&
make -j$(nproc) &&
make install 

libx264
cd /ffmpeg_sources && rm -rf x264 && 
git clone --depth 1 https://code.videolan.org/videolan/x264.git && 
cd x264 &&
./configure --cross-prefix=x86_64-w64-mingw32- --host=x86_64-w64-mingw32 \
--prefix="/usr/x86_64-w64-mingw32/sys-root/mingw" --enable-static &&
make -j$(nproc) &&
make install 

libogg 
cd /ffmpeg_sources && rm -rf ogg &&
git clone --depth 1 https://gitlab.xiph.org/xiph/ogg.git &&
cd ogg && ./autogen.sh &&
./configure --host=x86_64-w64-mingw32 --prefix="/usr/x86_64-w64-mingw32/sys-root/mingw" \
--enable-static --disable-shared &&
make -j$(nproc) &&
make install

libvorbis
cd /ffmpeg_sources && rm -rf vorbis && 
git clone --depth 1 https://gitlab.xiph.org/xiph/vorbis.git &&
cd vorbis && ./autogen.sh &&
./configure --host=x86_64-w64-mingw32 --prefix="/usr/x86_64-w64-mingw32/sys-root/mingw" \
--enable-static --disable-shared &&
make -j$(nproc) &&
make install

libaom
cd /ffmpeg_sources && rm -rf aom && 
git clone --depth 1 https://aomedia.googlesource.com/aom && 
mkdir -p /ffmpeg_sources/aom/build && cd /ffmpeg_sources/aom/build && 
cmake -G "Unix Makefiles" -DCMAKE_INSTALL_PREFIX="/usr/x86_64-w64-mingw32/sys-root/mingw" \
-DCMAKE_EXE_LINKER_FLAGS="-static" .. \
-DCMAKE_TOOLCHAIN_FILE="/ffmpeg_sources/aom/build/cmake/toolchains/x86_64-mingw-gcc.cmake" && 
make -j$(nproc) && 
make install

libopus
cd /ffmpeg_sources && rm -rf opus && 
git clone --depth 1 https://github.com/xiph/opus.git &&
cd opus && ./autogen.sh &&
./configure CFLAGS="-I/usr/local/llsp" --host=x86_64-w64-mingw32 --prefix="/usr/x86_64-w64-mingw32/sys-root/mingw" \
--enable-static --disable-shared &&
make -j$(nproc) &&
make install

libfdk-aac
cd /ffmpeg_sources && rm -rf fdk-aac && 
git clone --depth 1 https://github.com/mstorsjo/fdk-aac &&
cd fdk-aac && autoreconf -fiv &&
./configure --host=x86_64-w64-mingw32 --prefix="/usr/x86_64-w64-mingw32/sys-root/mingw" \
--enable-static --disable-shared &&
make -j$(nproc) &&
make install 

libsoxr

To compile the SOX resampler library, you'll first need to create a CMAKE toolchain file for the MinGW-w64 toolchain as the project doesn't include one by default.

Create a new file in the Cygwin root directory, and call it toolchain-x86_64-mingw32.cmake (make sure Windows is showing extensions, and that the extension is .cmake).

Copy and paste the following into the file:
SET(CMAKE_SYSTEM_NAME Windows)
SET(CMAKE_C_COMPILER /usr/bin/x86_64-w64-mingw32-gcc)
SET(CMAKE_CXX_COMPILER /usr/bin/x86_64-w64-mingw32-g++)
SET(CMAKE_RC_COMPILER /usr/bin/x86_64-w64-mingw32-windres)
SET(CMAKE_Fortran_COMPILER /usr/bin/x86_64-w64-mingw32-gfortran)
SET(CMAKE_AR:FILEPATH /usr/bin/x86_64-w64-mingw32-ar)
SET(CMAKE_RANLIB:FILEPATH /usr/bin/x86_64-w64-mingw32-ranlib)
SET(CMAKE_FIND_ROOT_PATH /usr/x86_64-w64-mingw32)
SET(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
SET(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
SET(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
SET(QT_BINARY_DIR /usr/x86_64-w64-mingw32/bin /usr/bin)
SET(Boost_COMPILER -gcc47)

cd /ffmpeg_sources && rm -rf soxr &&  
git clone --depth 1 https://git.code.sf.net/p/soxr/code soxr &&
mkdir -p soxr/build && cd soxr/build &&
cmake -Wno-dev -DCMAKE_BUILD_TYPE=Release \
-DCMAKE_INSTALL_PREFIX="/usr/x86_64-w64-mingw32/sys-root/mingw" \
-DBUILD_SHARED_LIBS=OFF .. -DCMAKE_TOOLCHAIN_FILE="/toolchain-x86_64-mingw32.cmake" &&
make -j$(nproc) &&
make install

FFMPEG
cd /ffmpeg_sources && rm -rf ffmpeg && git clone --depth 1 https://github.com/AntonyukYegor/ffmpeg ffmpeg  && cd ffmpeg && CFLAGS=-I/usr/x86_64-w64-mingw32/sys-root/mingw/include && LDFLAGS=-L/usr/x86_64-w64-mingw32/sys-root/mingw/lib && export PKG_CONFIG_PATH= && export PKG_CONFIG_LIBDIR=/usr/x86_64-w64-mingw32/sys-root/mingw/lib/pkgconfig && ./configure --arch=x86_64 --target-os=mingw32 --cross-prefix=x86_64-w64-mingw32- --prefix=/usr/local --pkg-config=pkg-config --pkg-config-flags=--static --extra-cflags=-static --extra-ldflags=-static --extra-libs="-lm -lz -fopenmp" --enable-static --disable-shared --enable-nonfree --enable-gpl --enable-libaom --enable-libfdk-aac --enable-libmp3lame --enable-libopus --enable-libsoxr --enable-libvorbis --enable-libx264 && make -j$(nproc) && make install


FFmpeg README
=============

FFmpeg is a collection of libraries and tools to process multimedia content
such as audio, video, subtitles and related metadata.

## Libraries

* `libavcodec` provides implementation of a wider range of codecs.
* `libavformat` implements streaming protocols, container formats and basic I/O access.
* `libavutil` includes hashers, decompressors and miscellaneous utility functions.
* `libavfilter` provides a mean to alter decoded Audio and Video through chain of filters.
* `libavdevice` provides an abstraction to access capture and playback devices.
* `libswresample` implements audio mixing and resampling routines.
* `libswscale` implements color conversion and scaling routines.

## Tools

* [ffmpeg](https://ffmpeg.org/ffmpeg.html) is a command line toolbox to
  manipulate, convert and stream multimedia content.
* [ffplay](https://ffmpeg.org/ffplay.html) is a minimalistic multimedia player.
* [ffprobe](https://ffmpeg.org/ffprobe.html) is a simple analysis tool to inspect
  multimedia content.
* Additional small tools such as `aviocat`, `ismindex` and `qt-faststart`.

## Documentation

The offline documentation is available in the **doc/** directory.

The online documentation is available in the main [website](https://ffmpeg.org)
and in the [wiki](https://trac.ffmpeg.org).

### Examples

Coding examples are available in the **doc/examples** directory.

## License

FFmpeg codebase is mainly LGPL-licensed with optional components licensed under
GPL. Please refer to the LICENSE file for detailed information.

## Contributing

Patches should be submitted to the ffmpeg-devel mailing list using
`git format-patch` or `git send-email`. Github pull requests should be
avoided because they are not part of our review process and will be ignored.
