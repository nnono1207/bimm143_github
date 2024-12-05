# Class/Lab 6: R Functions
Nathaniel Nono (PID: A16782656)

# Background

Today we are going to explore R functions and begin to think about
writing our own function

Every function has at least 3 things:

- A **name** that we pick
- One or more input **arguments**
- The **body**, whether the work actually gets done

``` r
add <- function(x, y) {
  x + y
}
```

> Using the `add()` function

``` r
add(1, 1)
```

    [1] 2

``` r
# R allows function to iterate over a vector
add_vector <- c(10, 1, 1, 10)
add(add_vector, 1)
```

    [1] 11  2  2 11

``` r
# Only 1 argument
# Uncomment function below to show to an error
# add(10)
# add(10, 10, 20)
```

``` r
# Able to override the default behavior
mean(c(10, 10, NA), na.rm = T)
```

    [1] 10

# Lab Sheet work

## Question 1

> Write a function grade() to determine an overall grade from a vector
> of student homework assignment scores dropping the lowest single
> score. If a student misses a homework (i.e. has an NA value) this can
> be used as a score to be potentially dropped. Your final function
> should be adquately explained with code comments and be able to work
> on an example class gradebook such as this one in CSV format:
> “https://tinyurl.com/gradeinput” \[3pts\]

Given vectors that show the scores of the students - student1: 90 should
be dropped - student2: Doing ok w/ one missing grade that should be
dropped - student3: Student has dropped

``` r
# Example input vectors to start with
student1 <- c(100, 100, 100, 100, 100, 100, 100, 90)
student2 <- c(100, NA, 90, 90, 90, 90, 97, 80)
student3 <- c(90, NA, NA, NA, NA, NA, NA, NA)
```

Before writing a function just write some code to do some work

Calculating the average for student 1

``` r
student1
```

    [1] 100 100 100 100 100 100 100  90

``` r
mean(student1)
```

    [1] 98.75

Calculating the average for student 2

``` r
student2
```

    [1] 100  NA  90  90  90  90  97  80

``` r
# This will lead to a NA return because there is a missing assignment
# mean(student2)

# This will pass over the NA
mean(student2, na.rm = T)
```

    [1] 91

Calculating the average for student 3 -\> Leads to more problems…

``` r
student3
```

    [1] 90 NA NA NA NA NA NA NA

``` r
# This is not really that fair since their average is still a 90 despite dropping
mean(student3, na.rm = T)
```

    [1] 90

Need to make the code a bit more complicated - Want to calculate the
average -\> Use `mean()` - Want to drop the lowest score - Want to make
it known if there are many `NA` scores

We can try the `min()` function to find the lowest score:

``` r
min(student1)
```

    [1] 90

I want to find the location of the min value not the value itself. For
this I can use `which.min()`

``` r
student1
```

    [1] 100 100 100 100 100 100 100  90

``` r
which.min(student1)
```

    [1] 8

Current progress code explained from inside out - Get the vector for
student1 - Grab the minimum value - Index to subtract that value from
the student1 vector - Take the mean of the vector to get their average
score

``` r
mean(student1[-(which.min(student1))])
```

    [1] 100

We need to deal with NA (missing values) still…

Make all NA equal to 0

``` r
student2
```

    [1] 100  NA  90  90  90  90  97  80

``` r
x <- student2
x
```

    [1] 100  NA  90  90  90  90  97  80

``` r
is.na(x)
```

    [1] FALSE  TRUE FALSE FALSE FALSE FALSE FALSE FALSE

``` r
# Find any NA within the `x` vector and map it to
x[is.na(x)] <- 0

x
```

    [1] 100   0  90  90  90  90  97  80

Progress so far - Got code to turn all NA’s into 0 - Got code to get the
mean after dropping the lowest score

``` r
# x[ is.na(x) ] <- 0
# mean(student1[-(which.min(student1))])

# Uncheck the specific x variable to make it work with a specific student
# x <- student1
# x <- student2
# x <- student3

x[ is.na(x) ] <- 0
mean(x[-(which.min(x))])
```

    [1] 91

Let’s write the function

``` r
grade <- function(x){
  # Finds the NAs in 'x' and make them 0
  x[ is.na(x) ] <- 0
    
  # Drops the lowest value and find mean
  mean(x[-(which.min(x))])
}
```

Using the function

``` r
grade(student1)
```

    [1] 100

``` r
grade(student2)
```

    [1] 91

``` r
grade(student3)
```

    [1] 12.85714

Using the apply function on the : `apply(dataset, margin, function)`

``` r
gradebook <- read.csv('https://tinyurl.com/gradeinput', row.names = 1)
gradebook
```

               hw1 hw2 hw3 hw4 hw5
    student-1  100  73 100  88  79
    student-2   85  64  78  89  78
    student-3   83  69  77 100  77
    student-4   88  NA  73 100  76
    student-5   88 100  75  86  79
    student-6   89  78 100  89  77
    student-7   89 100  74  87 100
    student-8   89 100  76  86 100
    student-9   86 100  77  88  77
    student-10  89  72  79  NA  76
    student-11  82  66  78  84 100
    student-12 100  70  75  92 100
    student-13  89 100  76 100  80
    student-14  85 100  77  89  76
    student-15  85  65  76  89  NA
    student-16  92 100  74  89  77
    student-17  88  63 100  86  78
    student-18  91  NA 100  87 100
    student-19  91  68  75  86  79
    student-20  91  68  76  88  76

To use `apply()` function on this `gradebook` dataset I need to decide
whether I want to `apply` the `grade()` function over the rows (1) or
columns (2) of the `gradebook`.

``` r
ans <- apply(gradebook, 1, grade)
ans
```

     student-1  student-2  student-3  student-4  student-5  student-6  student-7 
         91.75      82.50      84.25      84.25      88.25      89.00      94.00 
     student-8  student-9 student-10 student-11 student-12 student-13 student-14 
         93.75      87.75      79.00      86.00      91.75      92.25      87.75 
    student-15 student-16 student-17 student-18 student-19 student-20 
         78.75      89.50      88.00      94.50      82.75      82.75 

## Question 2

> Q2. Using your grade() function and the supplied gradebook, Who is the
> top scoring student overall in the gradebook? \[3pts\]

- Get the scores of the students
- Find the max score using the `which.max()` function

``` r
# Which student got the highest score
which.max(ans)
```

    student-18 
            18 

``` r
# What was the highest scoring student's actual score
ans[which.max(ans)]
```

    student-18 
          94.5 

Student 18 had the highest score with a score of 94.5

## Question 3

> Q3. From your analysis of the gradebook, which homework was toughest
> on students (i.e. obtained the lowest scores overall? \[2pts\]

``` r
apply(gradebook, 2, mean, na.rm=T)
```

         hw1      hw2      hw3      hw4      hw5 
    89.00000 80.88889 80.80000 89.63158 83.42105 

``` r
masked_gradebook <- gradebook
masked_gradebook[is.na(masked_gradebook)] = 0
apply(masked_gradebook, 2, mean)
```

      hw1   hw2   hw3   hw4   hw5 
    89.00 72.80 80.80 85.15 79.25 

I could modify the grade dunction to not drop the lowest options

``` r
grade2 <- function(x, drop.low=TRUE){
  
  # Finds the NAs in 'x' and make them 0
  x[ is.na(x) ] <- 0
   
  if(drop.low) {
    cat('Hello low')
    # Drops the lowest value and find mean
    mean(x[-(which.min(x))])
  }    
  
  else {
    out <- mean(x)
    cat('No low')
  }
  
  return(out)
    
}
```

HW 2 was the hardest due to it’s lowest score compared to the other
scores

## Question 4

> Q4. Optional Extension: From your analysis of the gradebook, which
> homework was most predictive of overall score (i.e. highest
> correlation with average grade score)? \[1pt\]

The function to calculate correlation in R is called `cor()`

``` r
x <- c(100, 90, 80, 100)
y <- c(100, 90, 80, 100)
z <- c(80, 90, 100, 10)


cor(x,y)
```

    [1] 1

``` r
cor(x,z)
```

    [1] -0.6822423

``` r
cor(ans, gradebook$hw1)
```

    [1] 0.4250204

**0.4250204** = Not too strong of a correlation between grades and hw1

``` r
cor(ans, masked_gradebook$hw5)
```

    [1] 0.6325982

I want to `apply()` the `cor()` function over the `masked_gradebook` and
use the `ans` scores for the class - Need to spell out extra `ans`
argument for `cors()`

``` r
apply(masked_gradebook, 2, cor, y=ans)
```

          hw1       hw2       hw3       hw4       hw5 
    0.4250204 0.1767780 0.3042561 0.3810884 0.6325982 

hw5 had the highest correlation

## Question 5

> Q5. Make sure you save your Quarto document and can click the “Render”
> (or Rmark- down”Knit”) button to generate a PDF foramt report without
> errors. Finally, submit your PDF to gradescope. \[1pt\]

kk :)
