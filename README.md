[![Build Status](https://travis-ci.org/carl-walker/paxix.svg?branch=master)](https://travis-ci.org/carl-walker/paxix)

# paxix
Paxi Extended. A toy programming language project. 

## Quick Start Build
To build the compiler, you will need autoconf, automake, and libtools

Once you have those tools installed, follow the standard build process:

	autoreconf
	automake --add-missing
	./configure
	make
	make check

## The Language

Paxi is a simple programming language intended for a compiler writing project at George Mason University. It supplies minimal but representative programming facilities and is designed for ease of implementation.
The canonical "Hello, world!" program looks like this in Paxi:

	proc main()
    	writestr("Hello, world!");  line;
	endproc
### Program Structure

A Paxi program consists of a declaration section, in which global variables are declared, and a code section. The code section is a list of procedures. Procedures accept parameters, have local variables and can optionally return a result. The entry point to a Paxi program is always a procedure called main. Paxi is case sensitive. All text between `//` and the end of the line is a comment. Variables are declared with the keywords `var`, for scalar variables, and `array` for arrays. All variables are integers. Below "[[item]]" means a comma-separated list of one or more items. A declaration has the form

`var [[identifier]];`

for scalar variables or

`array [[number indentifier]];`

for arrays. Any number of declarations can appear in the declaration section.

An identifier is any string of letters, digits, and underscores beginning with a letter or underscore. A Paxi compiler must accept identifiers up to 20 characters long (but may accept longer). Number literals in Paxi are decimal integers.

The number above is the dimension of the array. Array indexing is zero-based and any arithmetic expression can be used as an array index. An array index is encloses in brackets.

Character strings are stored in integer arrays (since integer is the only type available to us in Paxi). Strings are stored as ASCII codes with a 0 terminator. Single characters are enclosed in single quote marks (e.g. 'A'). A character may be used where ever a number is expected and is treated as an ASCII code.

### Procedures

Paxi subprograms are called procedures. They are introduced by the keyword proc followed by the procedure name (an identifier) and a (possibly empty) comma-separated parameter list enclosed in parentheses. They end with the keyword endproc. The formal parameters are identifiers and are treated as (scalar) local variables within the procedure. Procedures cannot contain other procedures.
Following the procedure name and parameters comes a (possibly empty) list of local variable declarations. Local variables cannot be arrays but are otherwise declared just like global variables. The scope of a local variable is the procedure which contains it. The local declarations are followed by a statement list. Statements are terminated by semi-colons. The following is a simple example procedure:

	proc haha(i, j)
		var temp;  // all local declarations precede the statement list
   		writestr("The first parameter has value ");
   		write(i); line;
   		temp = 3*j;
   		writestr("Three times the second is ");
   		write(temp); line;
	endproc
    
Procedures are called in the usual way, with any (integer-valued) arithmetic expressions for parameters:

	// calling haha:
   	haha(7, a[x + 9]*4);
Procedures may return a value. The retval statement saves a value to be used as a return value. When the procedure is called its value is the last value saved this way in the procedure.

	proc sum(i, j)
   		retval i + j;
	endproc

	proc main()
		var in1;
		var in2;
   		writestr("->  ");  read(in1);
   		writestr("->  ");  read(in2);
   		writestr("Sum is ");
   		write(sum(in1, in2)); line;
	endproc
    
Retval does not cause a return from a procedure -- it only associates a value. If a procedure containing a retval statement does not appear inside an expression where a value is expected the return value is ignored. If a procedure is used where a value is expected but it does not contain a retval statement its value is undefined.

Forward references are not allowed (a procedure can only call procedures which precede it in the program) but a procedure can call itself recursively.

### Input and Output

Paxi has very limited input and output, and no file I/O.
Integer values are read with the read statement and written with the write statement.

	// accept a value into variable i
   	read(i);
	// display an integer value:
   	write(i + 2);
Strings are read and written with readstr and writestr.

Writestr can take either a string literal (in double quotes) or an array name as an argument. If writestr takes an array name it displays a character for each element in the array up to (not including) the first 0 in the array treating each character as an ASCII code.

readstr takes an array name as argument. Input is stored in the array as ASCII codes, one character to an (integer) array element, with a terminating 0. It is the programmer's responsibility to see that the array is large enough to hold the input string and terminating 0. All input is accepted from the keyboard up to the newline (when the user hits the enter key). The newline character is not stored with the string.

When the following is run:

	array 80 instring;

	proc main()
   		writestr("What is your name?  ");
   		readstr(instring);
   		writestr("Well hello, ");
   		writestr(instring);
   		writestr("!"); line;
	endproc
the output may look like:

	What is your name?  Johann Sebastian Bach
	Well hello, Johann Sebastian Bach!
All characters inside a string are displayed literally, there are no "escaped" characters. A newline is displayed by using the line statement.

### Arithmetic

All arithmetic is integer arithmetic. Arithmetic expressions use the usual operators: +, -, *, and /. They have the usual precedence and parentheses have the expected meaning. When a procedure returns a value the procedure call can appear where an integer value is to be used.
Boolean Expressions

The simplest boolean expressions have the form (arithmetic_expression relational_operator arithmetic_expression), for example:

	(a < b)
	(var1 = 0)
	(3*x[5] <= y)
    
The relational operators are `<`, `<=`, `>=`, `>`, `=`, and `#`. `=` tests equality and `#` tests for "not equal to." `=` is used both as test for equality and for the assignment operator.

These simple expressions can then be combined with binary operators and and or and the unary operator not. not has higher precedence than and and and has higher precedence than or. Parentheses can be used to make sub-expressions be evaluated before other operators are applied. It is not necessary to have a pair of parentheses enclosing the entire expression.

Example Boolean expressions:

	(a < b) and (b > 0) or (b < a) and (a > 0)

	not((nextval() = 0) or (nextval() = -1))
### Control Structures

There are three control structures in Paxi: if (with optional else), while loop, and do while loop. All control structures may be nested.

Example if statements:

	if (var1 <= var2) and not (var1 = 0)
   		write(var2/var1);
   		var1 = var1 + 1;
	endif;

	if (i < 0)
   		writestr("negative"); line;
	else
   		writestr("non-negative"); line;
	endif;
The `if` statement is ended with the keyword `endif`. The list of statements between `if` and `endif`, or between `if` and `else` and then between `else` and `endif` are controlled as blocks.

The while and do while loops also control statement lists. while loops end with endwhile and the statement list controlled in a do while loop ends with endo. Examples:

	while (guess # target)
   		writestr("Guess again  ");
   		read(guess);
	endwhile;

	do
   		writestr("Enter y or n:  ");
   		readstr(instring);
	endo while (instring[0] # 'y') and (instring[0] # 'n');
### A Program

Finally, a complete (though not very interesting) program:

	// all global declarations up here
	array 80 instr;
	// now write procedures
	proc is_letter(ch)
	// this procedure can be called as a function
   		if (ch >= 'a') and (ch <= 'z') or (ch >= 'A') and (ch <= 'Z')
      		retval 1;
   		else
      		retval 0;
   		endif;
	endproc
   
	proc main()
		var i, letter_count;  // local declaration
   		writestr("Type something:  ");
   		readstr(instr);
   		letter_count = 0;
   		i = 0;
   		while (i < 80) and (instr[i] # 0)
      		if (is_letter(instr[i]) = 1)
         		letter_count = letter_count + 1;
      		endif;
      		i = i + 1;
   		endwhile;
   		writestr(instr); writestr (" has ");
   		write(letter_count); writestr(" letters"); line;
	endproc

### Keywords and Symbols

Keywords are reserved, i.e. they may not be used as identifiers. The reserved words in Paxi are:
`and, array, do, else, endo, endif, endproc, endwhile, if, line, not, or, proc, read, readstr, retval, var, while, writestr, write`.

The symbols used in Paxi are:

`+, -, *, /, =, <, <=, >=, >, #, (, ), [, ], ,(comma), ;, "(double quote), '(single quote), //`.

Paxi also has string literals (any character string enclosed in double quote marks), characters (any single character enclosed in single quotes), and number literals. Number literals are (signed) decimal integers. 
