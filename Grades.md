# Introduction #

---

Statistics on grades can be displayed in many ways. Decisions have to be made on formatting as well as what constitutes each letter grade. Design decisions have to be made as well, like who will compute what.

In our implementation, we chose to round each grade down after two decimal places so that the letter-grade cut-offs are respected. In turn, this led to always using rounded-down scores being used in computation so that things appear to add up correctly. If all students' actual scores were 79.999, technically they have C's, displaying 79.99 in the report. Since all students have 79.99, it would seem odd to have a mean of 80.00, or a standard deviation not 0.

We chose to have the student object calculate the student score, and the course object calculate the course mean. Then each student object calculate the student's z-score.

When it came to formatting the report, each grade was given the width of the largest max value plus 1 for the space. If the largest max value was 9, each score was given a width of 2. If the largest max value was 10000, each score was given a width of 6. When displaying the top of this table, we simply multiplied the number of scores by this width. If this total width was less than 6 (width needed to display the string at the top of the table), then the width was set to 6. In this case, spaces were added to the output of each student after displaying scores until the total width reached 29.

Another decision made was each student having a pointer to the Course. For an explanation, see second bullet under READ in Algorithm. Also made sense because a student can't exists without a course.



# Design #

---

**Course**
  * **Data**_This data was all made public to allow for easy testing.  We realized that it is identical to structs, but upon "shipping" the code the necessary data would be made private. We had so many variables to allow testing computation as well as testing display, to reflect read eval print being three separate testable sections of code. We didn't want to make variables private because then each variable would need to have a getter for testing, and each function also needs at least three tests. AH.
    * **istream&** r
    * **int** total\_events
    * **vector of int** max\_values
    * **vector of int** weights
    * **vector of Student** students
    * **int** total\_students
    * **double** min
    * **double** max
    * **double** mean
    * **int** num\_As
    * **double** percent\_As
    * **int** num\_Bs
    * **double** percent\_Bs
    * **int** num\_Cs
    * **double** percent\_Cs
    * **int** num\_Ds
    * **double** percent\_Ds
    * **int** num\_Fs
    * **double** percent\_Fs
    * **double** gpa
    * **double** std\_dev
    * **int** width
    * **int** maxvwidth
    * **char** fill
    * **iosbase::fmtflags** flags
    * **streamsize** precision
  * **Methods**_ most methods were void since the resulting computation would inevitably change the object's own data to be displayed.
    * **Course Course()** - constructor - Stores a reference to an input stream.
    * **void readNumEvents()**
    * **void readMaxValues()** - Enhancement: Also finds the largest max value to compute width when printing report. Makes alignment perfect, even with 1 1-digit score or 9 9-digit scores.
    * **void readWeights()**
    * **void readStudents()** - Enhancement: Also calculates each student's score in the process, as well as number of letter grades.
    * **void calculateStatistics()** - calls the following calculate methods
    * **void calculateMean()**
    * **void calculateStandardDeviation()**
    * **void calculatePercentLetterGrades()**
    * **void calculateGPA()**
    * **void calculateZscores()** - calls each student to calculate their own z-score since students are responsible for this.
    * **void restore()** - Restores stream settings back to their original states.
    * **void displayReport()** - calls the following display methods:
    * **void displayEvents()**
    * **void displayMaxValues()**
    * **void displayStatistics()**
    * **void displayWeights()**
    * **void displayStudents()**


**Student**
  * **Data**_* **Course pointer** course - pointer to course to which Student belongs to. Pointer is used to reduce vectors being passed.
    * **string** ID - description
    * **int** unique - description
    * **string** ta\_name - description
    * **vector of int** scores - description
    * **double** total\_score - description
    * **double** z\_score - description
    * **char** letter\_grade - description_

  * **Methods**_* **Student Student(course pointer)** - constructor -
    * **void readGrades(istream& rs)** - Also computes total.
    * **void calculateMyLetterGrade()** - Also updates course's count for letter grades.
    * **void calculateMyZscore()** - Uses pointer to course to access mean and standard deviation.
    * **bool Operator<(const Student& rhs)** - used to take advantage of stl sort, which requires < to be defined. Sorts first by score then by ID
    * **double getScore()** - getter needed for finding the mean of all the students_



# Algorithm #

---


**At it's core,** our algorithm called a read, eval, print loop.
  * **Read (initCourse)** - reads the number of events, reads maxes, reads weights, then sends each following line to Student's constructor, where each student's constructor does the following:
    * Reads ID, unique number, ta name, then scores
    * Upon reading each score, compares the score to max score then multiplies this by the score's weight. The resulting number is then added to the student's score. **Note:** We chose to store a pointer to the course in order to prevent copying the same vectors for max values and weights multiple times.
    * After reading each student's scores, the computed score is then rounded down to two decimal places. This is to make sure that computed mean and z-scores will reflect displayed values. We chose to round down to enforce the cut-offs for letter-grades.
  * **Eval (calculateStatistics)** - Calculates the mean of all students since the course object is responsible for calculating the mean of the course. Then calls each student's calculatemyzscore because each student object is responsible for calculating its own zscore. Since each student has a pointer to the course, we do not need to send the mean. For the reason of having student store pointer to course, see explanation given when describing student reading scores. This eval section also  computes the percentages of letter grades, storing them in the data to be displayed later.
  * **Print (displayReport)** - Displays all stored data.