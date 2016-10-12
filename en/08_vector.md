# Vector

## Creating vector object


```cpp
// v <- rep(0, 3)
NumericVector v (3);

// v <- rep(1, 3)
NumericVector v (3,1);

// v <- c(1,2,3) C++11 Initializer list
NumericVector v = {1,2,3};

// v <- c(1,2,3)
NumericVector v = NumericVector::create(1,2,3);

// v <- c(x=1, y=2, z=3)
NumericVector v =
  NumericVector::create(Named("x",1), Named("y")=2 , _["z"]=3);
```

## Accessing vector elements

You can access to individual element of a vector object using `[]` or `()` operator. Both operators accept NumericVector/IntegerVector (numerical index), CharacterVector (element names) and LogicalVector. `[]` operator ignores out of range access, while `()` operator throws an exception.

It is important to note that vector indices in C++ start at 0.

** [Important] : Vector indices start at 0 in C++. **


```cpp
// [[Rcpp::export]]
void rcpp_vector_access(){

  // Creating vector
  NumericVector v  {10,20,30,40,50};

  // Setting element names
  v.names() = CharacterVector({"A","B","C","D","E"});

  // Preparing vector for access
  NumericVector   numeric = {1,3};
  IntegerVector   integer = {1,3};
  CharacterVector character = {"B","D"};
  LogicalVector   logical = {false, true, false, true, false};

  // Getting values of vector elements
  double x1 = v[0];
  double x2 = v["A"];
  NumericVector res1 = v[numeric];
  NumericVector res2 = v[integer];
  NumericVector res3 = v[character];
  NumericVector res4 = v[logical];

  // Assigning values to vector elements
  v[0]   = 100;
  v["A"] = 100;
  NumericVector v2 {100,200};
  v[numeric]   = v2;
  v[integer]   = v2;
  v[character] = v2;
  v[logical]   = v2;
}
```

## Member functions

Member functions (also called as Methods) are functions that is attached to individual object. You can call member functions `f()` of object `v` in the form of `v.f()`.

```cpp
NumericVector v = {1,2,3,4,5};

// Calling member function
int n = v.length(); // 5
```

The Vector object in Rcpp have member object listed below.


### length(), size()

returns the number of elements.

### names()

returns element names as CharacterVector.

### offset(name), findName(name)

returns numerical index of the element specified by character string `name`.

### fill(x)

fills all the element of this vector with scalar value `x`.

### sort()

returns vector sorted in increasing order.


### assign( first_it, last_it)

assign values specified by iterator `first_it` and `last_it` to this vector.

### push_back(x)

append a scalar value `x` to the end of this vector.

### push_back( x, name )

append a scalar value `x` to the end of this vector and set name of the element as character string `name`.

### push_front(x)

append a scalar value `x` to the front of this vector.

### push_front( x, name )

append a scalar value `x` to the front of this vector and set name of the element as character string `name`.

### begin()

return an iterator pointing to the first element of the vector.

### end()

return an iterator pointing to the end of the vector (**one past the last element of this vector**).


### insert( i, x )

insert scalar value `x` to the position pointed by numerical index `i`. Return the iterator pointing the inserted element.


### insert( it, x )

insert scalar value `x` to the position pointed by iterator `it`. Return the iterator pointing the inserted element.

### erase(i)

erase element at the position pointed by numerical index `i`. Return the iterator pointing the element just behind the erased element.

### erase(it)

erase element at the position pointed by iterator `it`. Return the iterator pointing the element just behind the erased element.

### erase( first_i, first_i )

erase elements from the position pointed by numerical index `first_i` to `last_i - 1`. Return the iterator pointing the element just behind the erased elements.

### erase( first_it, last_it )

erase elements from the position pointed by iterator `first_it` to `last_it - 1`. Return the iterator pointing the element just behind the erased elements.

### containsElementNamed(name)

return `true` if this vector contains an element with the name specified by character string `name`.


## Static member functions

Static member function is the function that is attached to the class from which an object being molded. Static member functions is called in the form such as `NumericVector::create()`.

### get_na()

return the `NA` value of this `Vector` class.

### is_na(x)

return `true` if a vector element specified by `x` is `NA`.


### create( x1, x2, ...)

create a `Vector` object containing elements specified by scalar value `x1` and `x2`. Maximum number of arguments are 20.

### import( first_it , last_it )

create a `Vector` object filled with data from the position pointed by iterator `first_it` to `last_it - 1`.

### import_transform( first_it, last_it, func)

create a `Vector` object filled with data from the position pointed by iterator `first_it` to `last_it - 1` that is transformed by function specified by `func`.
