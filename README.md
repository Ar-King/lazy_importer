# lazy importer
A simple and easy to use header only library to make the life of a reverse engineer much harder.

## Small example
```cpp
LI_FIND(MessageBoxA)(nullptr, "hello world", nullptr, 0);
```

## Features
* Does not leave any strings in memory.
* Does not allocate any memory.
* Can be easily inlined.
* Does not import any functions.
* Produces extremely small assembly.
* Non caching functions do not leave anything in data sections.

## A thing to take consideration of
You must be sure that the function you are trying to find is exported and the shared library is loaded.
If that is not the case your program _will_ crash.

## Example output
Example from above decompiled with ida when forcefully not inlined
```c
char *sub_1400010D0()
{
  // variable declarations

  for ( i = *(_QWORD **)(*(_QWORD *)(*(_QWORD *)(__readgsqword(0x30u) + 96) + 24i64) + 16i64); ; i = (_QWORD *)*i )
  {
    v1 = (_DWORD *)i[6];
    v2 = (_DWORD *)((char *)v1 + *(unsigned int *)((char *)v1 + v1[15] + 136));
    if ( v2 != v1 )
    {
      v3 = 0i64;
      if ( v2[6] )
        break;
    }
LABEL_9:
    ;
  }
  v4 = (_DWORD *)((char *)v1 + (unsigned int)v2[8]);
  while ( 1 )
  {
    v5 = -2128831035;
    v6 = (char *)v1 + *v4;
    v7 = *v6;
    if ( *v6 )
    {
      do
      {
        ++v6;
        v5 = 16777619 * (v5 ^ v7);
        v7 = *v6;
      }
      while ( *v6 );
      if ( v5 == 598309348 )
        return (char *)v1
             + *(unsigned int *)((char *)&v1[*(unsigned __int16 *)((char *)v1 + 2 * v3 + (unsigned int)v2[9])]
                               + (unsigned int)v2[7]);
    }
    v3 = (unsigned int)(v3 + 1);
    ++v4;
    if ( (unsigned int)v3 >= v2[6] )
      goto LABEL_9;
  }
}
```

## Documentation
lazy importer exposes 4 rather self explanatory macros with multiple "overloads".

`LI_FIND[_DEF][_CACHED](function)`
Iterates trough all modules and their exports.

`LI_NT[_DEF][_CACHED](function)`
Iterates trough `ntdll.dll` exports.

`LI_GET[_DEF](module_base_address, function)`
Iterates trough exports of given module.

`LI_MODULE(module_name_string)`
Finds module base address, behaviour is undefined if module is not found.

`LI_MODULE_SAFE(module_name_string)`
Finds module base address, returns null if module is not found.

* If macro name contains "_DEF" the expected parameter "function" is a typedef/alias, otherwise a function pointer.
* If macro name contains "_CACHED" a static variable will be used to store the function pointer for fast access in subsequent calls. Note that for each hash there is a separate static variable.

None of these functions throw exceptions and are linear in complexity.

## Extra configuration
define LAZY_IMPORTER_NO_FORCEINLINE to disable force inlining.

define LAZY_IMPORTER_CASE_INSENSITIVE to enable case insensitive comparisons.

define LAZY_IMPORTER_RESOLVE_FORWARDED_EXPORTS to enable resolution of forwarded exports. IMPORTANT: LAZY_IMPORTER_CASE_INSENSITIVE might be necessary for this option to function properly.
