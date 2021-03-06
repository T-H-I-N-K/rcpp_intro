# Arithmetic operations and comparison operations

By using the `+ - * /` operator you can perform elementwise arithmetic operations between vectors of the same length.

```
NumericVector x ;
NumericVector y ;

// Vector and vector operation
NumericVector res = x + y ;
NumericVector res = x - y ;
NumericVector res = x * y ;
NumericVector res = x / y ;

// Vector and scalar operation
NumericVector res = x   + 2.0 ;
NumericVector res = 2.0 - x;
NumericVector res = y   * 2.0 ;
NumericVector res = 2.0 / y;

// expression and expression operation
NumericVector res = x * y + y / 2.0 ;
NumericVector res = x * ( y - 2.0 ) ;
NumericVector res = x / ( y * y ) ;
```

The `-` operator inverts the sign.

```
NumericVector res = -x ;
```

## Comparison operation

Comparison of vectors using `==` `! =` `<` `>` `> =` `<=` operators produces logical vectors. You can also access vector elements using logical vectors.

```cpp
NumericVector x ;
NumericVector y ;

// Comparison of vector and vector
LogicalVector res = x < y ;
LogicalVector res = x > y ;
LogicalVector res = x <= y ;
LogicalVector res = x >= y ;
LogicalVector res = x == y ;
LogicalVector res = x != y ;

// Comparison of vector and scalar
LogicalVector res = x < 2 ;
LogicalVector res = 2 > x;
LogicalVector res = y <= 2 ;
LogicalVector res = 2 != y;

// Comparison of expression and expression
LogicalVector res = ( x + y ) < ( x*x ) ;
LogicalVector res = ( x + y ) >= ( x*x ) ;
LogicalVector res = ( x + y ) == ( x*x ) ;
```

The `!` operator negates the logical value.

```
LogicalVector res = ! ( x < y );
```

Accessing the elements of the vector using logical vectors.

```cpp
NumericVector res = x[x < 2];
```
