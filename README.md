## Qt for WebAssembly development server. 

This server is intented to be used while developing and testing WebAssembly applications on a
trusted network. It is not suitable for production use cases like distributing applicaitons
to end users over public networks.

The server is an upgrade from the python one line server ("python -m http.server"), and offers the
following features:

* Zero-configuration / minimal configuration.

* Support for binding to multiple addresses in addition to localhost. This is useful
  for e.g. testing on mobile devices on the same local network as the development machine.
  Address can be addded individually using the --address option. Pass the "-all" option to
  bind to all available addresses.

* Support for generating https certificates using the mkcert utility. Many web features
  require a secure context. While "localhost" is considered a secure context (also when plain
  http is used), other addresses are not. Clicking through the "not secure" warnings is
  an option, but using a valid certificate improves flow.

* Support for compression using brotli. By default, the server compresses when serving over a
  public, non-localhost address. (localhost is fast, enabling compression here usualually increases
  download time instead of doing the opposite). Compression be controlled with the --compress-always and
  --compress-never options.

* Support for enabling cross-origin isolation mode. This sets the so-called COOP and COEP headers,
  which are required to enable SharedArrayBuffer and multithreading. Enable with the --cross-origin-isolation
  option. Note that this may impose additional restrictions on cross-origin requests.

TODO:

 * ipv6 support

# Installation and Usage

pip install qtwasmserver

Usage exmaples:

    qtwasmserver                            # Start server on localhost, serve $CWD
    qtwasmserver /path/to/wasm/builds       # Specify web root path
    qtwasmserver -p 1080                    # Start server(s) on custom port
    qtwasmserver --all-interfaces           # Start server(s) on all network interfaces
    qtwasmserver -a 10.0.0.2                # Start server on specific address, in addition to localhost
    qtwasmserver --cross-origin-isolation   # Enable cross-origin isolation mode for multithreading
    qtwasmserver -h                         # Show help

# Using mkcert

qtwasmserver can optinally use mkcert to generate https certifacates. See https://github.com/FiloSottile/mkcert for
installation and getting started instructions.

The basic flow is:

 1. Gereate a certificate authority (CA), and install that on all devices and browsers.
    This is a one time operation. mkcert will use thuis CA to sign certificates.
 2. Generate a certificate for each address you want to use. This is done automatically
    by this server.

The beneifit of this appraoch is that certificates can be generated locally, on demand,
for each server address in use. This can be useful when for instance moving a development
machine between home and office networks.
