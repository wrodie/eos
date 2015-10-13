# EOS

Tweaks made to allow the variable replacement in the postfix version, beyond that all code is the same as the original so full credit to https://github.com/jlawrence11/Classes.

The use case for these changes was that same formulas were being run many thousands of times with different variables.  With these tweaks it is possible to convert the formula from Infix to Postfix and then save the result. This result can then be solved by passing it different variables.

## Equation Operating System

### jlawrence\eos\

This class makes it incredibly easy to use and parse/solve equations in
your own applications. __NOTE__ ALL of the functions within
these classes are static. It is also important to note that these
classes throw exceptions if running in to errors, please read the beginning
of the `Math.php` file for the defines of the exceptions thrown. Exceptions
includes a descriptive message of the error encountered and within `Parser` will
also typically include the full equation used.

#### Parser

This class has one important function, `Parser::solve()` which does all the legwork,
so we'll start there and end with examples.

    use jlawrence\eos\Parser;

##### solve($infix, $variables)

To use this function:

    $value = Parser::solve($eq, $vars);

###### _$infix_

Is simply a standard equation with variable support.

Example Equations:

	2(4x)
    5+((1+2)*4)+3
    5+4(1+2)+3
    10*sin(x)
    10*cos(x)

The parser has good implied multiplication.

###### _$variables_

The variables are fairly simple to understand.  If it contains a scalar (ie
a non-array value) _every_ variable within the equation will be replaced with
that number.  If it contains an array, there will be a by-variable replacement -
note that the array MUST be in the format of `'variable' => value`  
Such as:

    array(
        'x' => 2,
        'y' => 3
    );

Given the equation:

    5x^y

If this is called by:

    Parser::solveIF('5x^y', 2);

It will equal '20', as every variable is replaced by 2.  However, if called like:

    Parser::solveIF('5x^y', array(
                                'x' => 2,
                                'y' => 3));

You will get the result of '40' as it would equate to `5*2^3`, as expected.

#### jlawrence\eos\Graph

To use:

    use jlawrence\eos\Graph;

This is the fun class that can create graphs.
The image will default to 640x480, to initialize a different size use:

    Graph::init($width, $height);

The `$width` and `$height` are the values used for the image size.

##### graph($eq, $xLow, $xHigh, [$xStep, $xyGrid, $yGuess, ...])

This method will generate the graph for the equation (`$eq`) with a min and max
`x` range that it will parse through. All Variables explained:

* `$eq`
    The Standard Equation to use.  _Must_ have a variable in it. (ie `x`)
* `$xLow`
    The starting point for the calculations - the left side of the graph.
* `$xHigh`
    The last point calculated for the variable - the right side of the graph.
* `$xStep`
    Stepping point for the variable. Set to null/false to use the smart xStep feature within the graph class.
* `$xyGrid = false`
    Show `x/y` gridlines on the graph.  Defaults to false.  Each grid line is set at an integer, with a max of 30 lines, so it will calculate the stepping for it. When the grid is show, the lines are labeled along the top and left side of the image. 
* `$yGuess = true`
    Guess the Lower and Upper `y-bounds` (The bottom and top of the image
    respectively.)  This will set the the bounds to the lowest `y` value
    encountered for the `$yLow`, and the largest `y` value for `$yHigh`.
* `$yLow = null`
    Lower bound for `y`. Will be reset if a lower value for `y` is found if `$yGuess` is true.
* `$yHigh = null`
    Upper bound for `y`. Will be reset if a larger `y` value is found if `$yGuess` is true.

If you don't want the axis' labeled with their numbers, you can turn off the default behavior with:

    Graph::$labelAxis = false;

TODO:

* Allow user-defined colors for all aspects of the graph.

To set up a graph with a `21x21` window (ie `-10 to 10`) for the equation
`sin(x)` and output as PNG, would use as:

    Graph::graph('sin(x)', -10, 10, 0.01, true, false, -10, 10);
    Graph::outPNG();

It would look like:  
![Sin(x)](http://s6.postimg.org/nm7tcj8lt/eos3.png)
---
---
