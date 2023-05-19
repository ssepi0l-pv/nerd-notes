# Nerd notes

Datatypes:
    - Simple: int, char, float, double.
    - Derived: arrays, pointers.
    - User-defined: enum, structures, unions.

Lenght of each:
    - Integer: -32768 .. 32767 || it uses 2 bytes of memory
    - Char: -128 .. 127 || it uses 1 byte of memory
    - Float: 3.4E-38 .. 3.4E+38 || it uses 4 bytes of memory
    - Doubles: 1.7E-308 .. 1.7E+308 || it uses 8 bytes of memory

Syntax: <datatype> <varname> = <var>;
        <datatype> <varname>;
        int hola = 1;

Modifiers: they're used to modify basic data types.
    - signed: stores both negative and positive numbers
    - unsigned: stores only negative numbers.
    - long; used double the memory of a given datatype.
    - short: uses half the memory of a given datatype.

Syntax: <modifier> <basic datatype> = <var>;
        <modifier> <basic datatype>;
        signed variable = 100;

Size and format specifiers of datatypes

| Datatype          | Size in bytes | Format specifier  |
| ----------------- | ------------- | ----------------- |
| char              | 1             | %c                |
| signed char       | 1             | %c                |
| unsigned char     | 1             | %c                |
| short int or int  | 2             | %hd, %d           |
| unsigned int      | 2             | %u                |
| long int          | 4             | %ld               |
| unsigned long int | 4             | %lu               |
| float             | 4             | %f                |
| double            | 8             | %lf               |
| long double       | 10            | %Lf               |
| ----------------- | ------------- | ----------------- |
