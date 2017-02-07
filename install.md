# GmSSL Installation

This document describes installation on all supported operating systems (the Linux/macOS/Unix family and Windows). This document is modified from the OpenSSL INSTALL.

<!--Todo: I need to add GmSSL related examples.-->

## Quick Start

If you want to just get on with it, do:

Download the source code from where?



 on Unix:

    $ ./config
    $ make
    $ make test
    $ make install

  on Windows (only pick one of the targets for configuration):

    $ perl Configure { VC-WIN32 | VC-WIN64A }
    $ nmake
    $ nmake test
    $ nmake install

 If any of these steps fails, see section Installation in Detail below.

 This will build and install OpenSSL in the default location, which is:

  Unix:    normal installation directories under /usr/local
  Windows: C:\Program Files\OpenSSL or C:\Program Files (x86)\OpenSSL

 If you want to install it anywhere else, run config like this:

--prefix and --openssldir control the configuration of installed components. The behavior and interactions of --prefix and --openssldir are slightly different between OpenSSL 1.0.2 and below, and OpenSSL 1.1.0 and above.

The **rule of thumb** to use when you want something that "just works" for all recent versions of OpenSSL, including OpenSSL 1.0.2 and 1.1.0, is:

- specify *both* --prefix and --openssldir
- set --prefix and --openssldir to the same location

One word of caution is *avoid* --prefix=/usr when OpenSSL versions are **not** [binary compatible](https://wiki.openssl.org/index.php/Binary_Compatibility). You will replace the distro's version of OpenSSL with your version of OpenSSL. It will most likely break everything, including the package management system.





  On Unix:

    $ ./config --prefix=/opt/openssl --openssldir=/usr/local/ssl

## Requirements

 To install OpenSSL, you will need:

* A make implementation
* Perl 5 with core modules (please read NOTES.PERL)
* The perl module Text::Template (please read NOTES.PERL)
* an ANSI C compiler
* a development environment in the form of development libraries and C header files
    * a supported operating system



## Configuration Options

 There are several options to ./config (or ./Configure) to customize
 the build (note that for Windows, the defaults for --prefix and
 --openssldir depend in what configuration is used and what Windows
 implementation OpenSSL is built on.  More notes on this in NOTES.WIN):

#### --api=x.y.z

Don't build with support for deprecated APIs below the specified version number. For example `--api=1.1.0` will remove support for all APIS that were deprecated in OpenSSL version 1.1.0 or below.

#### --cross-compile-prefix=PREFIX

The PREFIX to include in front of commands for your toolchain. It's likely to have to end with dash, e.g. a-b-c- would invoke GNU compiler as a-b-c-gcc, etc.

Unfortunately cross-compiling is too case-specific to put together one-size-fits-all instructions. You might have to pass more flags or set up environment variables to actually make it work. Android and iOS cases are discussed in corresponding Configurations/10-main.cf sections. But there are cases when this option alone is sufficient. For example to build the mingw64 target on Linux `--cross-compile-prefix=x86_64-w64-mingw32-` works. Naturally provided that mingw packages are installed. Today Debian and Ubuntu users have option to install a number of prepackaged cross-compilers along with corresponding run-time and development packages for `alien` hardware. To give another example `--cross-compile-prefix=mipsel-linux-gnu-` suffices in such case. Needless to mention that you have to invoke ./Configure, not ./config, and pass your target name explicitly.

#### --debug

Build OpenSSL with debugging symbols.

#### --libdir=DIR

The name of the directory under the top of the installation directory tree (see the --prefix option) where libraries will be installed. By default this is `lib`. Note that on Windows only `.lib` files will be stored in this location. dll files will always be installed to the `bin` directory.

#### --openssldir=DIR

Directory for OpenSSL configuration files, and also the default certificate and key store.  Defaults are:

	Unix:           /usr/local/ssl
	Windows:        C:\Program Files\Common Files\SSL
	or C:\Program Files (x86)\Common Files\SSL

#### --prefix=DIR

The top of the installation directory tree.  Defaults are:

	Unix:           /usr/local
	Windows:        C:\Program Files\OpenSSL
	or C:\Program Files (x86)\OpenSSL

#### --release

Build OpenSSL without debugging symbols. This is the default.

#### --strict-warnings

This is a developer flag that switches on various compiler options recommended for OpenSSL development. It only works when using gcc or clang as the compiler. If you are developing a patch for OpenSSL then it is recommended that you use this option where possible.

#### --with-zlib-include=DIR

The directory for the location of the zlib include file. This option is only necessary if enable-zlib (see below) is used and the include file is not already on the system include path.

#### --with-zlib-lib=LIB

**On Unix**: this is the directory containing the zlib library. If not provided the system library path will be used.

**On Windows**: this is the filename of the zlib library (with or without a path). This flag must be provided if the zlib-dynamic option is not also used. If zlib-dynamic is used then this flag is optional and a default value (`ZLIB1`) is used if not provided.

#### no-afalgeng

Don't build the AFALG engine. This option will be forced if on a platform that does not support AFALG.

#### no-asm

Do not use assembler code. On some platforms a small amount of assembler code may still be used.

#### no-async

Do not build support for async operations.

#### no-autoalginit

Don't automatically load all supported ciphers and digests. Typically OpenSSL will make available all of its supported ciphers and digests. For a statically linked application this may be undesirable if small executable size is an objective. This only affects libcrypto. Ciphers and digests will have to be loaded manually using EVP_add_cipher() and EVP_add_digest() if this option is used. This option will force a non-shared build.

#### no-autoerrinit

Don't automatically load all libcrypto/libssl error strings. Typically OpenSSL will automatically load human readable error strings. For a statically linked application this may be undesirable if small executable size is an objective.


#### no-capieng

Don't build the CAPI engine. This option will be forced if on a platform that does not support CAPI.

#### no-cms

Don't build support for CMS features

#### no-comp
Don't build support for SSL/TLS compression. If this option is left enabled (the default), then compression will only work if the zlib or zlib-dynamic options are also chosen.

#### enable-crypto-mdebug
Build support for debugging memory allocated via OPENSSL_malloc() or OPENSSL_zalloc().

#### enable-crypto-mdebug-backtrace
As for crypto-mdebug, but additionally provide backtrace information for allocated memory. TO BE USED WITH CARE: this uses GNU C functionality, and is therefore not usable for non-GNU config targets.  If your build complains about the use of '-rdynamic' or the lack of header file execinfo.h, this option is not for you. ALSO NOTE that even though execinfo.h is available on your system (through Gnulib), the functions might just be stubs that do nothing.

#### no-ct

Don't build support for Certificate Transparency.

#### no-deprecated

Don't build with support for any deprecated APIs. This is the
same as using `--api` and supplying the latest version
number.

#### no-dgram

Don't build support for datagram based BIOs. Selecting this
option will also force the disabling of DTLS.

#### no-dso

Don't build support for loading Dynamic Shared Objects.

#### no-dynamic-engine

Don't build the dynamically loaded engines. This only has an
effect in a `shared` build

#### no-ec

Don't build support for Elliptic Curves.

#### no-ec2m
Don't build support for binary Elliptic Curves

#### enable-ec_nistp_64_gcc_128

Enable support for optimised implementations of some commonly
used NIST elliptic curves. This is only supported on some
platforms.

#### enable-egd

Build support for gathering entropy from EGD (Entropy
Gathering Daemon).

#### no-engine

Don't build support for loading engines.

#### no-err

Don't compile in any error strings.

#### no-filenames

Don't compile in filename and line number information (e.g. for errors and memory allocation).

#### enable-fuzz-libfuzzer, enable-fuzz-afl

Build with support for fuzzing using either libfuzzer or AFL.
These are developer options only. They may not work on all
platforms and should never be used in production environments.
See the file fuzz/README.md for further details.

#### no-gost

Don't build support for GOST based ciphersuites. Note that if this feature is enabled then GOST ciphersuites are only available if the GOST algorithms are also available through loading an externally supplied engine.

#### enable-heartbeats

Build support for DTLS heartbeats.

#### no-hw-padlock

Don't build the padlock engine.

#### no-makedepend

Don't generate dependencies.

#### no-multiblock

Don't build support for writing multiple records in one go in libssl (Note: this is a different capability to the pipelining functionality).

#### no-nextprotoneg

Don't build support for the NPN TLS extension.

#### no-ocsp

Don't build support for OCSP.

#### no-pic

Don't build with support for Position Independent Code.

#### no-posix-io

Don't use POSIX IO capabilities.

#### no-psk

Don't build support for Pre-Shared Key based ciphersuites.

#### no-rdrand

Don't use hardware RDRAND capabilities.

#### no-rfc3779

Don't build support for RFC3779 (`X.509 Extensions for IP Addresses and AS Identifiers`)

#### sctp

Build support for SCTP

#### no-shared

Do not create shared libraries, only static ones.  See `Note
on shared libraries` below.

#### no-sock

Don't build support for socket BIOs

#### no-srp

Don't build support for SRP or SRP based ciphersuites.

#### no-srtp

Don't build SRTP support

#### no-sse2

Exclude SSE2 code paths. Normally SSE2 extension is detected at run-time, but the decision whether or not the machine code will be executed is taken solely on CPU capability vector. This means that if you happen to run OS kernel which does not support SSE2 extension on Intel P4 processor, then your application might be exposed to `illegal instruction` exception. There might be a way to enable support in kernel, e.g. FreeBSD kernel can be compiled with CPU_ENABLE_SSE, and there is a way to disengage SSE2 code paths upon application start-up, but if you aim for wider `audience` running such kernel, consider no-sse2. Both the 386 and no-asm options imply no-sse2.







#### enable-ssl-trace

Build with the SSL Trace capabilities (adds the `-trace` option to s_client and s_server).

#### no-static-engine

Don't build the statically linked engines. This only has an impact when not built `shared`.

#### no-stdio

Don't use any C `stdio` features. Only libcrypto and libssl can be built in this way. Using this option will suppress building the command line applications. Additionally since the OpenSSL tests also use the command line applications the tests will also be skipped.

#### no-threads

Don't try to build with support for multi-threaded applications.

#### threads

Build with support for multi-threaded applications. Most platforms will enable this by default. However if on a platform where this is not the case then this will usually require additional system-dependent options! See `Note on multi-threading` below.

#### no-ts

Don't build Time Stamping Authority support.

#### enable-ubsan

Build with the Undefined Behaviour sanitiser. This is a developer option only. It may not work on all platforms and should never be used in production environments. It will only work when used with gcc or clang and should be used in conjunction with the `-DPEDANTIC` option (or the `--strict-warnings` option).

#### no-ui

Don't build with the `UI` capability (i.e. the set of features enabling text based prompts).

#### enable-unit-test

Enable additional unit test APIs. This should not typically be used in production deployments.

#### enable-weak-ssl-ciphers

Build support for SSL/TLS ciphers that are considered ***weak*** (e.g. RC4 based ciphersuites).

#### zlib

Build with support for zlib compression/decompression.

#### zlib-dynamic

Like `zlib`, but has OpenSSL load the zlib library dynamically when needed.  This is only supported on systems where loading of shared libraries is supported.

#### 386

On Intel hardware, use the 80386 instruction set only (the default x86 code is more efficient, but requires at least a 486). Note: Use compiler flags for any other CPU specific configuration, e.g. `-m32` to build x86 code on an x64 system.

#### no-{prot}

Don't build support for negotiating the specified SSL/TLS protocol (one of ssl, ssl3, tls, tls1, tls1_1, tls1_2, dtls, dtls1 or dtls1_2). If `no-tls` is selected then all of tls1, tls1_1 and tls1_2 are disabled. Similarly `no-dtls` will disable dtls1 and dtls1_2. The `no-ssl` option is synonymous with `no-ssl3`. Note this only affects version negotiation. OpenSSL will still provide the methods for applications to explicitly select the individual protocol versions.

#### no-{prot}-method

As for no-{prot} but in addition do not build the methods for applications to explicitly select individual protocol versions.

#### enable-{alg}

Build with support for the specified algorithm, where {alg} is one of: md2 or rc5.

#### no-{alg}

Build without support for the specified algorithm, where {alg} is one of: bf, blake2, camellia, cast, chacha, cmac, des, dh, dsa, ecdh, ecdsa, idea, md4, md5, mdc2, ocb, ploy1305, rc2, rc4, rmd160, scrypt, seed or whirlpool. The `ripemd` algorithm is deprecated and if used is synonymous with rmd160.

#### -Dxxx, -lxxx, -Lxxx, -fxxx, -mXXX, -Kxxx

These system specific options will be passed through to the compiler to allow you to define preprocessor symbols, specify additional libraries, library directories or other compiler options.


## Installation in Detail


### 1a. Configure OpenSSL for your operation system automatically:

NOTE: This is not available on Windows.

	$ ./config [[ options ]]                         # Unix

or

	$ @config [[ options ]]                          ! OpenVMS

For the remainder of this text, the Unix form will be used in all
examples, please use the appropriate form for your platform.

This guesses at your operating system (and compiler, if necessary) and
configures OpenSSL based on this guess. Run ./config -t to see
if it guessed correctly. If you want to use a different compiler, you
are cross-compiling for another platform, or the ./config guess was
wrong for other reasons, go to step 1b. Otherwise go to step 2.

On some systems, you can include debugging information as follows:

	$ ./config -d [[ options ]]

### 1b. Configure OpenSSL for your operating system manually

OpenSSL knows about a range of different operating system, hardware and
compiler combinations. To see the ones it knows about, run

	$ ./Configure                                    # Unix

or

	$ perl Configure                                 # All other platforms

For the remainder of this text, the Unix form will be used in all
examples, please use the appropriate form for your platform.

Pick a suitable name from the list that matches your system. For most
operating systems there is a choice between using `cc` or `gcc`.  When
you have identified your system (and if necessary compiler) use this name
as the argument to Configure. For example, a `linux-elf` user would
run:

	$ ./Configure linux-elf [[ options ]]

If your system isn't listed, you will have to create a configuration
file named Configurations/{{ something }}.conf and add the correct
configuration for your system. See the available configs as examples
and read Configurations/README and Configurations/README.design for
more information.

The generic configurations `cc` or `gcc` should usually work on 32 bit
Unix-like systems.

Configure creates a build file (`Makefile` on Unix, `makefile` on Windows
and `descrip.mms` on OpenVMS) from a suitable template in Configurations,
and defines various macros in include/openssl/opensslconf.h (generated from
include/openssl/opensslconf.h.in).

### 1c. Configure OpenSSL for building outside of the source tree.

OpenSSL can be configured to build in a build directory separate from
the directory with the source code.  It's done by placing yourself in
some other directory and invoking the configuration commands from
there.

Unix example:

	$ mkdir /var/tmp/openssl-build
	$ cd /var/tmp/openssl-build
	$ /PATH/TO/OPENSSL/SOURCE/config [[ options ]]

or

	$ /PATH/TO/OPENSSL/SOURCE/Configure {{ target }} [[ options ]]

Windows example:

	$ C:
	$ mkdir \temp-openssl
	$ cd \temp-openssl
	$ perl d:\PATH\TO\OPENSSL\SOURCE\Configure {{ target }} [[ options ]]

Paths can be relative just as well as absolute.  Configure will
do its best to translate them to relative paths whenever possible.

### 2. Build OpenSSL by running:

	$ make                                           # Unix
	$ nmake                                          # Windows

This will build the OpenSSL libraries (libcrypto.a and libssl.a on
Unix, corresponding on other platforms) and the OpenSSL binary
(`openssl`). The libraries will be built in the top-level directory,
and the binary will be in the `apps` subdirectory.

If the build fails, look at the output.  There may be reasons for
the failure that aren't problems in OpenSSL itself (like missing
standard headers).  If you are having problems you can get help by
sending an email to the openssl-users email list (see
https://www.openssl.org/community/mailinglists.html for details). If it
is a bug with OpenSSL itself, please report the problem to
<rt@openssl.org> (note that your message will be recorded in the request
tracker publicly readable at
https://www.openssl.org/community/index.html#bugs and will be
forwarded to a public mailing list). Please check out the request
tracker. Maybe the bug was already reported or has already been
fixed.

(If you encounter assembler error messages, try the `no-asm`
configuration option as an immediate fix.)

Compiling parts of OpenSSL with gcc and others with the system
compiler will result in unresolved symbols on some systems.

### 3.   After a successful build, the libraries should be tested. Run:

	$ make test                                      # Unix
	$ nmake test                                     # Windows

NOTE: you MUST run the tests from an unprivileged account (or
disable your privileges temporarily if your platform allows it).

If some tests fail, look at the output.  There may be reasons for
the failure that isn't a problem in OpenSSL itself (like a
malfunction with Perl).  You may want increased verbosity, that
can be accomplished like this:

	$ make VERBOSE=1 test                            # Unix
	$ nmake VERBOSE=1 test                           # Windows

If you want to run just one or a few specific tests, you can use
the make variable TESTS to specify them, like this:

	$ make TESTS='test_rsa test_dsa' test            # Unix
	$ nmake TESTS='test_rsa test_dsa' test           # Windows

And of course, you can combine (Unix example shown):
​    
	$ make VERBOSE=1 TESTS='test_rsa test_dsa' test

You can find the list of available tests like this:

	$ make list-tests                                # Unix
	$ nmake list-tests                               # Windows

Have a look at the manual for the perl module Test::Harness to
see what other HARNESS_* variables there are.

If you find a problem with OpenSSL itself, try removing any
compiler optimization flags from the CFLAGS line in Makefile and
run `make clean; make` or corresponding.

Please send bug reports to <rt@openssl.org>.

### 4.   If everything tests ok, install OpenSSL with

	$ make install                                   # Unix
	$ nmake install                                  # Windows

This will install all the software components in this directory tree under PREFIX (the directory given with `--prefix` or its default):

|                                  |                                          |
| -------------------------------- | ---------------------------------------- |
| `{PREFIX}/bin/`                  | Contains the openssl binary and a few other |
| `{PREFIX}/include/openssl/`      | Contains the header files needed if you want to build your own programs that use libcrypt or libssl. |
| `{PREFIX}/lib`/                  | Contains the OpenSSL library files.      |
| `{PREFIX}/lib/engines/`          | Contains the OpenSSL dynamically loadable engines. |
| {PREFIX}/share/man/man1          | Contains the OpenSSL  man-pages.         |
| {PREFIX}/share/doc/openssl/html/ | Contains the HTML rendition of the man-pages. |
| {OPENSSLDIR}/certs               | Initially empty, this is the default location for certificate files. |
| {OPENSSLDIR}/private             | Initially empty, this is the default location for private key files. |
| {OPENSSLDIR}/misc                | Various scripts.                         |



Additionally, install will add the following directories under
 (the directory given with --openssldir or its default)
for you convenience:

	          
	        
	          

Package builders who want to configure the library for standard
locations, but have the package installed somewhere else so that
it can easily be packaged, can use

	$ make DESTDIR=/tmp/package-root install         # Unix

The specified destination directory will be prepended to all
installation target paths.


## Environment Variables

A number of environment variables can be used to provide additional control over the build process. Typically these should be defined prior to running config or Configure. Not all environment variables are relevant to all platforms.

| ENV                      | Descritpion                              |
| ------------------------ | ---------------------------------------- |
| AR                       | The name of the `ar` executable to use.  |
| CC                       | The compiler to use. Configure will attempt to pick a default compiler for your platform but this choice can be overridden using this variable. Set it to the compiler executable you wish to use, e.g. `gcc` or `clang`. |
| CROSS_COMPILE            | This environment variable has the same meaning as for the`--cross-compile-prefix` Configure flag described above. If both are set then the Configure flag takes precedence. |
| NM                       | The name of the nm executable to use.    |
| OPENSSL_LOCAL_CONFIG_DIR | OpenSSL comes with a database of information about how itshould be built on different platforms. This information isheld in `.conf` files in the Configurations directory. See the file `Configurations/README` for further information about the format of `.conf` files. As well as the standard `.conf` files it is possible to create your own `.conf` files and store them locally, outside the OpenSSL source tree. This environment variable can be set to the directory where these files are held. |
| RC                       | The name of the `rc` executable to use. The default will be as defined for the target platform in the `.conf` file. If not defined then `windres` will be used. The WINDRES environment variable is synonymous to this. If both are defined then RC takes precedence. |
| WINDRES                  | See RC.                                  |
| RANLIB                   | The name of the `ranlib` executable to use. |
|                          |                                          |

## Makefile targets

The Configure script generates a Makefile in a format relevant to the specific
platform. The Makefiles provide a number of targets that can be used. Not all
targets may be available on all platforms. Only the most common targets are
described here. Examine the Makefiles themselves for the full list.

| TARGET     |                                          |
| ---------- | ---------------------------------------- |
| all        | The default target to build all the software components. |
| clean      | Remove all build artefacts and return the directory to a *clean* state. |
| install    | Install all OpenSSL components.          |
| list-tests | Prints a list of all the self test names. |
| test       | Build and run the OpenSSL self tests.    |
| uninstall  | Uninstall all OpenSSL components.        |
| update     | This is a developer option. If you are developing a patch for OpenSSL you may need to use this if you want to update automatically generated files; add new error codes or add new (or change the visibility of) public API functions. (Unix only). |









## Compile GmSSL for Android







## Compile GmSSL for iOS





I need to check the build of GmSSL on major platforms.
