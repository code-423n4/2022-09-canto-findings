# Low and Non Critical Issues
* Pragmas should be locked to a specific compiler version, to avoid contracts getting deployed using a different version, which may have a greater risk of undiscovered bugs.
* Internal functions without `_` mislead the reader. It is better to mark internal functions by adding `_` in the beginning of their name.