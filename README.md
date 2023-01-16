## Qt for WebAssembly development server. 

This server is intented to be used while developing and testing WebAssembly applications on a
trusted network. It is not suitable for production use, i.e. distributing applicaitons to end
users over public networks.

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
  download time instead of the opposite). Compression be controlled with the --compress-always and 
  --compress-never options.

* Support for enabling site isolation mode. This sets the so-called COOP and COEP headers,
  which are required to enable SharedArrayBuffer and multithreading. Enable with the --site-isolation option.
  While there is little reason to not enable SharedArrayBuffer, site isolation mode also enables
  additional restrictions for cross origin requests, which may come as a surprise.

It also has a couple of non-features:

* Support for starting/stopping web browsers.

* Support for forwarding standard output to the console.

If you are looking to use features like the above, then emrun from Emscripten is a better choice.

TODO:

 * ipv6 support

# Installation and Usage

pip install qtwasmserver

    qtwasmserver                        # Start server on localhost, serve $CWD
    qtwasmserver /path/to/wasm/builds   # Specify web root path
    qtwasmserver --all-interfaces       # Start server(s) on all network interfaces
    qtwasmserver -a 10.0.0.2            # Start server on specific address, in addition to localhost
    qtwasmserver --site-isolation       # Enable site isolation mode for multithreading
    qtwasmserver -h                     # Show help

# Using mkcert

qtwasmserver can optinally use mkcert to generate https certifacates. See https://github.com/FiloSottile/mkcert for
installation and getting started instructions.

The basic flow is:

 1. Gereate a certificate authority (CA), and install that on all devices and browsers.
    This is a one time operation. mkcert will use thuis CA to sign certificates.
 2. Generate a certificate for each address you want to use. This is done automatically
    by this server.

The main beneifits of this appraoch is that certificates can be freely generated if/when
the network address for the server changes, for example when moving between home and office
networks, and that it does not requie reqeusting certificates from a third party.
