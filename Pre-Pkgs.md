This file illustrates how I intend to create an internal/vendored hex0 bootstrap... 

"Only doing one cycle and without the kexec steps, for the sake of my sanity."

To build the tools, that then are used to auto-import/verify nixpkgs hex0, if its valid
Since youre wondering `Why the f*$k?`
because there's a large number of processes involved in stdenv bootstrap, and importing both nixpkgs.lib and nixpkgs.pkgs
the first process in nixpkgs implementation of the posix-stage0... by necessity uses non-stdenv mkderivation.
But even for those that don't... fetching the source itself relies on "fetchers", which in-turn... 
rely on your computers state ("ssl certs", ssl implementation, "curl version/compile options,etc.", ), even if only minimally. 

> Just how ridiculous is it?
`Unbelievably!!!`

> So Unbelievably, in fact, that I wont be doing the full thing, but will outline the concept... for anyone else with as much joy in the insanely hilarious joke that is computer security, as I do.

If you want to truly ensure that a system is actually... not just reproducible, but actually secured... 
You have to ensure that what you have fetched is what you intended to. 
Theoretically... and therefore - 
Factually, in the case of actually starting with an untrusted or unknown trust system...
You - 
cannot trust your sha-256/hashing algoriths
cannot trust host binaries weren't tampered with
cannot trust - even - that current nix lib is actually safe/proper,
because it was fetched with untrustable fetchers,
using untrustable ssl-certs, used in the backing of untrustable ssl-libs, with - a kernel - you...
cannot trust... 
Technically... creating an actually secure env, would require
> 1. auditing
> 2. building out the entire self-hosting toolchain.
> 3. doing it again using that toolchain, to verify, or create, and replace "any single point-of-failure/code" you depended on to fetch, modify, build, interpret, or otherwise interact with the source, including the kernel you're running.
> Because the interpreter, and everything else was built with "almost" "potentially trustworthy" state, they need replaced.
> So you use the new toolchain as your only/single source of truth, to rebuild your entire computers foundation.
> 5. kexec into and mount the chroot as new_root "Full reboot invalidates the entire process... as you use the unknown==false trust-state of the early stages of the computers firmware loading"
> 6. repeat the entire process with your new system binaries, init, kernel, verified syscalls, and fetchers - to create something actually capable of theoretically being... marginally trusted.
> 7. do it A 3rd time. Now with the potential for marginal trust.
> 8. do it a 4th time with the marginally trusted system state, to create an actually trusted system state (note the word state)
> 9. set up ssl,  dns, and every other basic+essential security measure, other than secure-boot...

Again:
`Why the f*$k?`
Because until now we've been using kexec to skip and ignore much of the firmware *for simplicity*, he says, hopefully not sounding deranged.
At this point you (hopefully) have a secure system "state"... but states are temporary, and it's time to make it maintainable.

That means secure-boot, and firmware, for which flashrom can "maybe? work", for your needs depending on firmware, if you have amd without (re-flashed linux-boot based (or otherwise audited by you) firmware), (intel without the above or without a disabled management-engine), or aarch64 (At all), you have to "fix that", using your current "highest achiveable level of trust", then repeat the entire process above.

After all of that, you can set up secure-boot.

Obviously... this is insane... but technically, actually the bare-minimum, which no-system uses.

once all of that is done... you can now... continue to the next step... 

and fetch the hex0 seed from nixpkgs.
`WTF?!!`
I know... now you can compare it... and it's "descendents" - to your own... and maybe find it trustworthy... 


---


If you want to absolutely guarantee that no bash shell or subprocess executes at all -
You can use Nix's built-in placeholder expansion.
However, this is typically done using builtins.toFile.
```nix
# Purely evaluated text file, but NOT a fixed-output derivation with a manually set hash
builtins.toFile "hex0-seed" "vendored/inlined long sequence of the ascii reresentation of a known-good hex0 seed;"

```
