Disclaimer
==========
   DISCLAIMER.  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND
   CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING,
   BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND
   FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
   COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT,
   INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
   BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS
   OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
   ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR
   TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE
   USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH
   DAMAGE.

What this is
============

This is a fork of sacd-ripper/sacd-ripper.  For original release documents, please refer to https://github.com/sacd-ripper/sacd-ripper

This fork adds the following additional features to the original sacd-ripper:

1. Padding-less DSF generation (-z):  Some players do not handle zero padding in DSF correctly resulting in a pop noise at a track transition.  With this option, instead of zero padding, the data fragment that doesn't perfectly fit in the 4096 Bytes/channel block size at the tail of a track is carried forward to the head of the next track so no zero padding is needed.  This is a loss-less process.  This option cannot be combined with the -t option because continuous processing of the entire album is needed for this option to work.

2. Concurrent ISO+DSF/DSDIFF processing (-w):  With this option, both ISO (-I) and DSF (-s) or DSDIFF (-p) can be generated concurrently.  This is much more efficient than generating ISO and DSF/DSDIFF sequentially because ISO generation is a very light process in terms of CPU usage while DST decompression (mandatory for DSF, optional for DSDIFF) is not.  The run time of this process is expected to be by the slower of ISO and DSF/DSDIFF generation.

3. Output directory options (-o and -y):  These allow users to specify the output directory.  For the concurent processing mode, -o is for ISO and -y is for DSF/DSDIFF.  The directories specified by these options must exist.  Output directories default to the current directory.

4. Enabled max compiler optimization in CMakeList.txt for gcc.  This provides about 3x speed boost to DST decoding (mandatory for DSF generation) for Linux and MacOS.

Development of this software is primarily done on Linux.  Functionality on Windows has been checked.

Usage
======================================

The following options are available for the sacd_extract commandline tool: ::

  -2, --2ch-tracks                : Export two channel tracks (default)
  -m, --mch-tracks                : Export multi-channel tracks
  -e, --output-dsdiff-em          : output as Philips DSDIFF (Edit Master) file
  -p, --output-dsdiff             : output as Philips DSDIFF file
  -s, --output-dsf                : output as Sony DSF file
  -z, --dsf-nopad                 : Do not zero pad DSF (cannot be used with -t)
  -t, --select-track              : only output selected track(s) (ex. -t 1,5,13)
  -I, --output-iso                : output as RAW ISO
  -w, --concurrent                : Concurrent ISO+DSF/DSDIFF processing mode
  -c, --convert-dst               : convert DST to DSD
  -C, --export-cue                : Export a CUE Sheet
  -i, --input[=FILE]              : set source and determine if "iso" image,
                                    device or server (ex. -i 192.168.1.10:2002)
  -o, --output-dir[=DIR]          : Output directory (ISO output dir for concurrent processing mode)
  -y, --output-dir-conc[=DIR]     : DSF/DSDIFF Output directory for concurrent processing mode
  -P, --print                     : display disc and track information


Usage examples
==============

Extract all stereo tracks in uncompressed DSDIFF from an ISO to /home/user/blah/<album_name>::

    $ sacd_extract -2 -p -c -i"Foo_Bar_RIP.ISO" -o /home/user/blah

Extract all stereo tracks in padding-less DSF files from an ISO to /home/user/blah/<album_name>::

    $ sacd_extract -2 -s -z -i"Foo_Bar_RIP.ISO" -o /home/user/blah

Extract an ISO from a server to /home/user/blah/<album_name>.iso::

    $ sacd_extract -I -i192.168.1.10:2002 -o /home/user/blah

Concurrently extract an ISO file to /home/user/blah/<album_name>.iso and all stereo tracks in DSF to /tmp/blah/<album_name> from a server.::

    $ sacd_extract -I -s -w -z -i192.168.1.10:2002 -o /home/user/blah -y /tmp/blah

