This file illustrates how I intend to create an internal/vendored hex0 bootstrap... 
To build the tools, that then are used to auto-import/verify nixpkgs hex0, if its valid
Since youre wondering `Why the f*$k?`
because there's a large number of processes involved in stdenv bootstrap, and importing both nixpkgs.lib and nixpkgs.pkgs
the first... by necessity uses non-stdenv mkderivation.
But even for those that don't... fetching the source itself relies on "fetchers", which in-turn... 
rely on your computers state ("ssl certs", ssl implementation, "curl version/compile options,etc.", ), even if only minimally, 

> Just how ridiculous is it?
If you want to truly ensure that a system is actually... not just reproducible, but actually secured... 
You have to ensure that what you have fetched is what you intended to,
cannot trust your sha-256/hashing algoriths
cannot trust host binaries weren't tampered with
technically...
cannot trust - even - that current nix lib is actually safe/proper,
because it was fetched with untrustable fetchers,
using untrustable ssl-certs, used in the backing of untrustable ssl-libs, with - a kernel - you...
cannot trust... 
Technically... creating an actually secure env, would require
> 1. auditing
> 2. building out the entire self-hosting toolchain.
> 3. doing it again using that toolchain, to verify, or create, and replace "any single point-of-failure/code" you depended on to fetch, modify, build, interpret, or otherwise interact with source, including the kernel you're running.
> Since the interpreter, and everything else were built with "almost" "potentially trustworthy" state, they need replaced.
> So you use the new toolchain as your only/single source of truth, to rebuild your entire computers foundation.
> 5. kexec into and mount the chroot as new_root "Full reboot invalidates the entire process... as you use the unknown==false trust-state of the early stages of the computers firmware loading"
> 6. repeat the entire process with your new system binaries, init, kernel, verified syscalls, and fetchers - to create something actually capable of theoretically being... marginally trusted.
> 7. do it A 3rd time. Now with the potential for marginal trust.
> 8. do it a 4th time with the marginally trusted system, to create an actually trusted system
> 9. set up ssl, secure-boot, dns, and every other basic+essential security measure

Obviously... this is insane... but technically, actually the bare-minimum which no-system uses.


If you want to absolutely guarantee that no bash shell or subprocess executes at all -
You can use Nix's built-in placeholder expansion.
However, this is typically done using builtins.toFile.
```nix
# Purely evaluated text file, but NOT a fixed-output derivation with a manually set hash
builtins.toFile "hex0-seed" "vendored/inlined long sequence of the ascii reresentation of a known-good hex0 seed;"

```
