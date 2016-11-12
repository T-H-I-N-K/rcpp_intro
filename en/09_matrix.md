# Matrix

### Creating Matrix object

Like Vector, Matrix object can be created in several ways.

```
// Create a matrix equivalent to
// m <- matrix(0, nrow=2, ncol=2)
NumericMatrix m1( 2 );

// Create a matrix equivalent to
// m <- matrix(0, nrow=2, ncol=3)
NumericMatrix m2( 2 , 3 );

// m <- matrix(v, nrow=2, ncol=3) と同等の行列を作成します
NumericMatrix m3( 2 , 3 , v.begin() );
```

In addition, a matrix in R is actually a vector that the number of rows and columns are set in the attribute `dim`. So if you created a vector with attribute `dim` in Rcpp and return it to R. It will be treated as a matrix.


```
// [[Rcpp::export]]
NumericVector rcpp_matrix(){
    // Creating a vector object
    NumericVector v = {1,2,3,4};

    // Set number of rows and columns to attribute dim
    v.attr("dim") = Dimension(2, 2);

    // Returns a vector with attribute dim to R
    return v;
}
```

Execution result

```
> rcpp_matrix()
     [,1] [,2]
[1,]    1    3
[2,]    2    4
```

However, even if you set a value to attribute `dim` of a vector object, the type of the object in Rcpp will remain Vector type. To convert it to Matrix type in Rcpp, you need to use `as<T>()` function.

```
// Set number of rows and columns to attribute dim
v.attr("dim") = Dimension(2, 2);

// Converting to Rcpp Matrix type
NumericMatrix m = as<NumericMatrix>(v);
```

### Accessing to Matrix elements

By using `()` operator, you can get and assign the values of the elements of the matrix by specifying its column number and row number. As in the case of vectors, row numbers and column numbers in Matrix start with 0. If you want to access to a specific row or column, use the symbol `_`.

You can also use the `[]` operator to access an element as a vector connecting the columns of a matrix.



```
// Creating a 5x5 numerical matrix
NumericMatrix m(5,5);

// Retrieving the element of row 0 and column 2
double x = m( 0 , 2 );

// Copying the value of row 0 to the vector v
NumericVector v = m( 0 , _ );

// Copying the value of column 2 to the vector v
NumericVector v = m( _ , 2 );

// Copying the row (0 to 1) and column (2 to 3) to the matrix m2
NumericMatrix m2 = m( Range(0,1) , Range(2,3) );

// Accessing matrix element as vector
m[5]; // This points to the same element as m(0,1)
```


### References to row, column, sub matrix

Rcpp also provides types that hold "references" to some column and row of a matrix.

```
NumericMatrix::Column col = m( _ , 1);  // Reference to the column 1
NumericMatrix::Row    row = m( 1 , _ ); // Reference to the row 1
NumericMatrix::Sub    sub = m( Range(0,1) , Range(2,3) ); // Reference to sub matrix
```
Substituting a value to a "reference" object of a matrix will assign the value to its original matrix. For example, assigning a value to `col` will assign a value to the column 1 of `m`.

```
col = 2 * col;             // The value of the column 1 of matrix m will be doubled
m( _ , 1) = 2 * m( _ , 1 ) // Synonymous with the above example
```




## Member functions

Since `Matrix` is actually `Vector`, `Matrix` basically has the same member functions as `Vector`. Below is a member function unique to `Matrix`.

#### nrow() rows()

Returns the number of rows.

#### ncol()　cols()

Returns the number of columns.

####row( i )

Return a reference `Vector::Row` to the `i`th　row.

####column( i )

Return a reference `Vector::Column` to the `i`th　column.

#### fill_diag( x )

Fill diagonal elements with scalar value x.

#### offset(i,j)

Returns the numerical index in the original vector of the matrix corresponding to the element of row i and column j.



## Static member function

`Matrix` basically has the same static member function as` Vector`. The static member functions unique to `Matrix` are shown below.

#### Matrix::diag( size, x )

Returns a diagonal matrix whose number of rows and columns equals to "size" and the value of the diagonal element is "x".



## Other functions relating to Matrix

This section shows functions relating to Matrix.

#### rownames( m )

Get and set the row name of matrix m.

```
CharacterVector ch = rownames(m);
rownames(m) = ch;
```

#### colnames( m )

Get and set the column name of matrix m.

```
CharacterVector ch = colnames(m);
colnames(m) = ch;
```


#### transpose( m )

Returns the transposed matrix of matrix m.
