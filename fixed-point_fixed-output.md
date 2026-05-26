# A simplified conceptual view of a fixed-point package cache
let
  cacheFun = final: {
    # Each entry is an individual fixed-output derivation
    libA = fetchurl { 
      url = "https://example.com"; 
      sha256 = "07v9g...hyg3"; 
    };

    # libB looks up libA through the final evaluated fixed-point set
    libB = mkDerivation {
      pname = "libB";
      src = fetchurl { 
        url = "https://example.com"; 
        sha256 = "1md7j...465"; 
      };
      buildInputs = [ final.libA ]; # Evaluated lazily!
    };
  };

  # Tie the loop. This creates the immutable "Cache" structure
  packageCache = builtins.fix cacheFun;
in
  packageCache
