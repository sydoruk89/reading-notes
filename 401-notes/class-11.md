# Data Analysis

## Lists Of Lists for CSV Data
* Import the csv library.
* Open the winequality-red.csv file.
* With the file open, create a new csv.reader object.
* Pass in the keyword argument delimiter=";" to make sure that the records are split up on the semicolon character instead of the default comma character.
* Call the list type to get all the rows from the file.
* Assign the result to wines.

import csv
with open('winequality-red.csv', 'r') as f:
    wines = list(csv.reader(f, delimiter=';'))

## Creating A NumPy Array
Import the numpy package.
Pass the list of lists wines into the array function, which converts it into a NumPy array.
Exclude the header row with list slicing.
Specify the keyword argument dtype to make sure each element is converted to a float. We’ll dive more into what the dtype is later on.
import csv
with open("winequality-red.csv", 'r') as f:
    wines = list(csv.reader(f, delimiter=";"))
import numpy as np
wines = np.array(wines[1:], dtype=np.float)

We can check the number of rows and columns in our data using the shape property of NumPy arrays:
wines.shape
(1599, 12)

You can also create an array where each element is a random number using numpy.random.rand. Here’s an example:

np.random.rand(3,4)
array([[ 0.2247223 , 0.92240549, 0.14541893, 0.61731257],
[ 0.00154957, 0.82342197, 0.74044906, 0.11466845],
[ 0.6152478 , 0.14433138, 0.13009583, 0.22981301]])

## Using NumPy To Read In Files

Use the genfromtxt function to read in the winequality-red.csv file.
Specify the keyword argument delimiter=";" so that the fields are parsed properly.
Specify the keyword argument skip_header=1 so that the header row is skipped.

wines = np.genfromtxt("winequality-red.csv", delimiter=";", skip_header=1)


7.4	0.70	0.00	1.9	0.076	11	34	0.9978	3.51	0.56	9.4	5
7.8	0.88	0.00	2.6	0.098	25	67	0.9968	3.20	0.68	9.8	5
7.8	0.76	0.04	2.3	0.092	15	54	0.9970	3.26	0.65	9.8	5
11.2 0.28	0.56	1.9	0.075	17	60	0.9980	3.16	0.58	9.8	6
7.4	0.70	0.00	1.9	0.076	11	34	0.9978  3.51	0.56	9.4	5

wines[2,3]
2.2999999999999998

## Slicing NumPy Arrays
wines[0:3,3]
array([ 1.9, 2.6, 2.3])

Just like with list slicing, it’s possible to omit the 0 to just retrieve all the elements from the beginning up to element 3:
wines[:3,3]
array([ 1.9, 2.6, 2.3])

The below code will select the entire fourth column:
wines[:,3]
array([ 1.9, 2.6, 2.3, ..., 2.3, 2. , 3.6])

We selected an entire column above, but we can also extract an entire row:
wines[3,:]
array([ 11.2 , 0.28 , 0.56 , 1.9 , 0.075, 17. , 60.,
0.998, 3.16 , 0.58 , 9.8 , 6. ])

## Assigning Values To NumPy Arrays
We can also use indexing to assign values to certain elements in arrays. We can do this by assigning directly to the indexed value:
wines[1,5] = 10
We can do the same for slices. To overwrite an entire column, we can do this:
wines[:,10] = 50

# 1-Dimensional NumPy Arrays
third_wine = wines[3,:]
Here’s how third_wine looks:
11.200
0.280
0.560
1.900
0.075
17.000
60.000
0.998
3.160
0.580
9.800
6.000
third_wine[1]
0.28000000000000003

## N-Dimensional NumPy Arrays
earnings = [
    [
        [500,505,490],
        [810,450,678],
        [234,897,430],
        [560,1023,640]
    ],
    [
        [600,605,490],
        [345,900,1000],
        [780,730,710],
        [670,540,324]
    ]
]
earnings = np.array(earnings)
earnings[0,0,0]
500

We can also find the shape of the array:
earnings.shape
(2, 4, 3)

Indexing and slicing work the exact same way with a 3-dimensional array, but now we have an extra axis to pass in. If we wanted to get the earnings for January of all years, we could do this:
earnings[:,0,0]
array([500, 600])

If we wanted to get first quarter earnings from both years, we could do this:
earnings[:,0,:]
array([[500, 505, 490],
[600, 605, 490]])

## Converting Data Types
You can use the numpy.ndarray.astype method to convert an array to a different type. The method will actually copy the array, and return a new array with the specified data type. For instance, we can convert wines to the int data type:

wines.astype(int)
array([[ 7, 0, 0, ..., 0, 9, 5],
[ 7, 0, 0, ..., 0, 9, 5],
[ 7, 0, 0, ..., 0, 9, 5],
...,
[ 6, 0, 0, ..., 0, 11, 6],
[ 5, 0, 0, ..., 0, 10, 5],
[ 6, 0, 0, ..., 0, 11, 6]])

## Single Array Math
wines[:,11] + 10
array([ 15., 15., 15., ..., 16., 15., 16.])

If we instead did +=, we’d modify the array in place:
wines[:,11] += 10
wines[:,11]
array([ 15., 15., 15., ..., 16., 15., 16.])

## Multiple Array Math
It’s also possible to do mathematical operations between arrays. This will apply the operation to pairs of elements. For example, if we add the quality column to itself, here’s what we get:

wines[:,11] + wines[:,11]
array([ 10., 10., 10., ..., 12., 10., 12.]) // Note that this is equivalent to wines[11] * 2 

We’d multiply alcohol by quality, and select the wine with the highest score:
wines[:,10] * wines[:,11]
array([ 47., 49., 49., ..., 66., 51., 66.])

## NumPy Array Methods
wines[:,11].sum()
9012.0
wines.sum(axis=0)
array([ 13303.1 , 843.985 , 433.29 , 4059.55 ,
139.859 , 25384. , 74302. , 1593.79794,
5294.47 , 1052.38 , 16666.35 , 9012. ])

If we pass in axis=1, we’ll find the sums over the second axis of the array. This will give us the sum of each row:
wines.sum(axis=1)
array([ 74.5438 , 123.0548 , 99.699 , ...,<br />
100.48174, 105.21547,
92.49249])

There are several other methods that behave like the sum method, including:
numpy.ndarray.mean — finds the mean of an array.
numpy.ndarray.std — finds the standard deviation of an array.
numpy.ndarray.min — finds the minimum value in an array.
numpy.ndarray.max — finds the maximum value in an array.

## NumPy Array Comparisons
wines[:,11] > 5
array([False, False, False, ..., True, False, True], dtype=bool)

wines[:,11] == 10
array([False, False, False, ..., False, False, False], dtype=bool)

## Subsetting
One of the powerful things we can do with a Boolean array and a NumPy array is select only certain rows or columns in the NumPy array. For example, the below code will only select rows in wines where the quality is over 7:
high_quality = wines[:,11] > 7
wines[high_quality,:][:3,:]
array([[ 7.90000000e+00, 3.50000000e-01, 4.60000000e-01,
3.60000000e+00, 7.80000000e-02, 1.50000000e+01,
3.70000000e+01, 9.97300000e-01, 3.35000000e+00,
8.60000000e-01, 1.28000000e+01, 8.00000000e+00],
[ 1.03000000e+01, 3.20000000e-01, 4.50000000e-01,
6.40000000e+00, 7.30000000e-02, 5.00000000e+00,
1.30000000e+01, 9.97600000e-01, 3.23000000e+00,
8.20000000e-01, 1.26000000e+01, 8.00000000e+00],
[ 5.60000000e+00, 8.50000000e-01, 5.00000000e-02,
1.40000000e+00, 4.50000000e-02, 1.20000000e+01,
8.80000000e+01, 9.92400000e-01, 3.56000000e+00,
8.20000000e-01, 1.29000000e+01, 8.00000000e+00]])

high_quality_and_alcohol = (wines[:,10] > 10) & (wines[:,11] > 7)
wines[high_quality_and_alcohol,10:]

## Reshaping NumPy Arrays

np.transpose(wines).shape
(12, 1599)
We can use the numpy.ravel function to turn an array into a one-dimensional representation. It will essentially flatten an array into a long sequence of values:

wines.ravel()
array([ 7.4 , 0.7 , 0. , ..., 0.66, 11. , 6. ])

array_one = np.array(
    [
        [1, 2, 3, 4],
        [5, 6, 7, 8]
    ]
)
array_one.ravel()
array([1, 2, 3, 4, 5, 6, 7, 8])


Finally, we can use the numpy.reshape function to reshape an array to a certain shape we specify. The below code will turn the second row of wines into a 2-dimensional array with 2 rows and 6 scolumns:
wines[1,:].reshape((2,6))
array([[ 7.8 , 0.88 , 0. , 2.6 , 0.098 , 25. ],
[ 67. , 0.9968, 3.2 , 0.68 , 9.8 , 5. ]])

## Combining NumPy Arrays
With NumPy, it’s very common to combine multiple arrays into a single unified array. We can use numpy.vstack to vertically stack multiple arrays.

white_wines = np.genfromtxt("winequality-white.csv", delimiter=";", skip_header=1)
white_wines.shape
(4898, 12)

all_wines = np.vstack((wines, white_wines))
all_wines.shape
(6497, 12)

If we want to combine arrays horizontally, where the number of rows stay constant, but the columns are joined, then we can use the numpy.hstack function. The arrays we combine need to have the same number of rows for this to work.
Finally, we can use numpy.concatenate as a general purpose version of hstack and vstack. If we want to concatenate two arrays, we pass them into concatenate, then specify the axis keyword argument that we want to concatenate along. Concatenating along the first axis is similar to vstack, and concatenating along the second axis is similar to hstack:

np.concatenate((wines, white_wines), axis=0)
array([[ 7.4 , 0.7 , 0. , ..., 0.56, 9.4 , 5. ],
[ 7.8 , 0.88, 0. , ..., 0.68, 9.8 , 5. ],
[ 7.8 , 0.76, 0.04, ..., 0.65, 9.8 , 5. ],
...,
[ 6.5 , 0.24, 0.19, ..., 0.46, 9.4 , 6. ],
[ 5.5 , 0.29, 0.3 , ..., 0.38, 12.8 , 7. ],
[ 6. , 0.21, 0.38, ..., 0.32, 11.8 , 6. ]])