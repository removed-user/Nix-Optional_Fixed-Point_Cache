> Basic_fix_example.md

A fixed-point function that expects its final, evaluated representation as a parameter.

```nix
let
  f = self: {
    foo = "Hello";
    bar = "World";
    greeting = "${self.foo} ${self.bar}!";
  };
in
  builtins.fix f
  
# Evaluates to: { bar = "World"; foo = "Hello"; greeting = "Hello World!"; }

```

Fixed points unlock extensibility.
By delaying final evaluation, you can inject changes (overlays) into the middle of the evaluation process.
