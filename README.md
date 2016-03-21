### Introduction
Hello fellow Coursera Students!
This R file is composed of two functions, one which creates an enhanced
matrix object, and a second that specifically handles the caching of the
matrix inverse.  In effect the "makeCacheMatrix" function acts as object
constructor, where object methods are stored and returned as list elements
and object properties (matrix and inverse) are stored as variables in the
function environment.  A demonstration of these functions and the structures
they create are given as follows:

    > testMatrix <- makeCacheMatrix(matrix(c(1,-1,2,0),ncol=2))
    > ls(environment(testMatrix$getMatrix))
    [1] "getMatrix"    "getMatrixInv" "inv"          "setMatrix"    "setMatrixInv" "x"           
    > ls(environment(testMatrix$setMatrix))
    [1] "getMatrix"    "getMatrixInv" "inv"          "setMatrix"    "setMatrixInv" "x"           
    > ls(environment(testMatrix$setMatrixInv))
    [1] "getMatrix"    "getMatrixInv" "inv"          "setMatrix"    "setMatrixInv" "x"           
    > ls(environment(testMatrix$getMatrixInv))
    [1] "getMatrix"    "getMatrixInv" "inv"          "setMatrix"    "setMatrixInv" "x"  
    > testMatrix$getMatrixInv()
    NULL
    > cacheSolve(testMatrix)
         [,1] [,2]
    [1,]  0.0 -1.0
    [2,]  0.5  0.5
    > testMatrix$getMatrixInv()
         [,1] [,2]
    [1,]  0.0 -1.0
    [2,]  0.5  0.5
    > testMatrix$setMatrix(matrix(c(1,1,1,-1),ncol=2))
    > testMatrix$getMatrixInv()
    NULL

In retrospect I am wondering if the second
function - cachesolve - could also be included as one of the member functions
of makeCacheMatrix, so that it could be called via x$cachesolve

### makeCacheMatrix
This function in effect creates the container around the matrix and its 
inverse, alone with set and get functions to access each of these.
This function does not handle any calculation of the matrix inverse, and
in fact sets the inverse to null when the object is first created.

    makeCacheMatrix <- function(x = matrix()) {
      inv <- NULL
      setMatrix <- function(y) {
        x <<- y
        inv <<- NULL
      }
      getMatrix <- function() x
      setMatrixInv <- function(matinv) inv <<- matinv
      getMatrixInv <- function() inv
      list(setMatrix = setMatrix, 
           getMatrix = getMatrix,
           setMatrixInv = setMatrixInv,
           getMatrixInv = getMatrixInv)
    }

### cacheSolve
This function actually handles the calculation of
the matrix inverse, but only does so if the stored
matrix inverse is null, which from the code above
would only occur if the matrix has been reset or if
the matrix object itself is being created for the
first time.  If the matrix inverse is retrievable
without recalculation, then a message is generated
that it is being retrieved from the cache.  Since
the matrix solve function allows for additional
arguments, I am passing the '...' to this function.

    cacheSolve <- function(x, ...) {
      inv <- x$getMatrixInv()
      if(!is.null(inv)) {
        message("getting cached matrix inverse")
        return(inv)
      }
      mat <- x$getMatrix()
      inv <- solve(mat, ...)
      x$setMatrixInv(inv)
      inv
    }

Happy grading, everybody!