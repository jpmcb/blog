---

---

# The No Nonsense Interview Prep

The following is interview preperation material
for software engineer and development roles
that covers the most common aspects of data structures,
algorithms, design, and behavioral.

This interview manual is ever evolving;
I make changes to it frequently as my experience grows
and I find new resources.

{{< toc >}}

## Python Crash Course

The following is a crash course in Python
that I've used to refresh my knowledge and understanding of the langauge
from a high level.

### But why Python?

The last thing you want to do during a technical white board interview
is stumble over weird or complicated syntax, forget how some library works,
or waste time sketching out alot of boilerplate.

Further, because of how _close_ python is to normal spoken language,
even if the person interviewing you doesn't have a firm grasp of python,
they _will_ understand what you're trying to do.

In short, it's all about communication.
Python is easy to communicate,
reason about,
simple to understand,
and reduces the overall complexity of your technical interview.

Python is just really simple and un-complicated.

### Basic operations

## Data structures

### Arrays

```python3
# initializing arrays with array values
arr = [1, 2, 3]

# Use `append()` to insert a new value at end of the array.
# This is a O(1) operation but may require that we
# grow the underlying datastructure to accommodate the new element.
#
# An understand of how arrays "grow" under the hood as we add elements
# is a necessary part of understanding the time complexity of using arrays.
arr.append(4);

# Use insert() to insert a value at a specific position.
# This inserts 5 at the 2nd position. This is NOT zero indexed.
# This is a O(n) operation in the worst case given all elements
# may need to be "shifted" or the underlying array may need to grow
arr.insert(2, 5)

# We can perform an in place sort.
# The `arr` will now be sorted.
arr.sort()

# This returns a sorted copy where `arr` is not mutated.
sorted(arr)

# You may apply a lambda to the sorting.
#
# A lambda is essentially an "anonymous" function
# that can take any arbitrary number of inputs.
#
# This example uses an arbitrary "student" class
# that has some "grade_point_average" data member.
#
# For the `.sort()` method, we can supply a key
# and in this example, use the student's gradepoint averages
student.sort(key=lambda student: student.grade_point_average)

# Reverses the array.
# Ever get a "reverse string" question? Just use this!
arr.reverse()

# Returns the index of the first occurrence of the given element.
arr.index(2)

# Removes the first occurence of the given element
arr.remove(2)

# Returns true if element is in the array
# The time complexity of this is O(n) in order to scan the whole array
if 2 in arr:
	print('2 is in the array!')

# Creates a shallow copy (where compound objects are only REFERENCES, not deep copies)
copy.copy(arr)

# Creates a deep copy which recursively inserts copies of any nest, compound objects
# Most of the time, during interview quetsions, you probably want a deep copy
copy.deepcopy(arr)

# Returns the minimum element within the array
min(arr)

# Returns the maximum element within the array
max(arr)

# Slicing is very useful where array[start:stop:step]
# So, for example, this prints the array starting at the second element
# and going in reverse order. It'd look something like "[2, 1]"
print(arr[1::-1])

# List comprehension in python is also super powerful during interviews.
# You need 3 things to do list comprehension:
# 1. And input sequence
# 2. An iterator
# 3. A logical condition
#
# Wrap all of that in the `[ ]` array syntax
# and you'll end up with a new array.
# In this example, we are taking x to the power of 2
# where x is the list of number from 0 to 5
# but we only take numbers if they are modulo % 2.
# All this in one line!
[x**2 for x in range(6) if x % 2 == 0]
```
