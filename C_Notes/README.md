# C programming notes and stuff

Datatypes:<br>

- Simple: int, char, float, double.<br>
- Derived: arrays, pointers.<br>
- User-defined: enum, structures, unions.<br>

Lenght of each:<br>
- Integer: -32768 .. 32767 || it uses 2 bytes of memory<br>
- Char: -128 .. 127 || it uses 1 byte of memory<br>
- Float: 3.4E-38 .. 3.4E+38 || it uses 4 bytes of memory<br>
- Doubles: 1.7E-308 .. 1.7E+308 || it uses 8 bytes of memory<br>

Syntax: int variable = 1;

int variable1;

Modifiers: they're used to modify basic data types.<br>
- signed: stores both negative and positive numbers<br>
- unsigned: stores only negative numbers.<br>
- long; used double the memory of a given datatype.<br>
- short: uses half the memory of a given datatype.<br>

Syntax: signed variable = 100;

unsigned variable1;

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
