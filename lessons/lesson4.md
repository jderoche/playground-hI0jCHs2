Given an array `arr[ARRAY_SIZE]` we can get the address of the `i`-th element by `arr + i` as `arr` works as a pointer to the first element of the array. This is true for any pointer pointing any element of `arr` array.

```C runnable
#include <stdio.h>
#include <stdint.h>

#define ARRAY_SIZE 8

void print_array(const int32_t arr[], int num_elements)
{
    for (int i = 0; i < num_elements; i++)
	{
		printf("%d ", arr[i]);
	}
}

int main()
{
	int32_t arr[ARRAY_SIZE] = { 22, 33, 44 };
	int32_t *p = arr + 3;

	printf("Array elements before modification: ");
	print_array(arr, ARRAY_SIZE);

	*p = 55;

	printf("\nArray elements after modification:  ");
	print_array(arr, ARRAY_SIZE);

	return 0;
}
```

In the example above, `p` points to the 4th element of array by the assignment `p = arr + 3`. The statement `*p = 55;` modifies the contents of the memory location pointed to by `p`, essentially `arr[3]`.

In the following example, all array elements are modified by pointer:

```C runnable
#include <stdio.h>
#include <stdint.h>

#define ARRAY_SIZE 4

void print_array(const int32_t arr[], int num_elements)
{
    for (int i = 0; i < num_elements; i++)
	{
		printf("%p | %d\n", arr + i, arr[i]);
	}
}

int main()
{
	int32_t arr[ARRAY_SIZE] = { 0 };

	for (int i = 0; i < ARRAY_SIZE; i++)
	{
		arr[i] = i + 1;
	}

	printf("Array elements' address and content before modification:\n");
	print_array(arr, ARRAY_SIZE);

	int32_t *p = arr; /* p points to the first element of arr */
	for (int i = 0; i < ARRAY_SIZE; i++)
	{
		printf("Modifying content of address %p\n", p); /* Current address p is holding */
		*p *= *p; /* Squaring the content pointed to by p (eg., 3 becomes 9) */
		p++; /* Increamenting the content of p, points to the next element of the array */
	}

	printf("Array elements' address and content after modification:\n");
	print_array(arr, ARRAY_SIZE);

	return 0;
}
```

This is what happens in the example above. `arr` is initialized as follows:

```
arr:         1    2    3    4
address:     10   14   18   22
```

The address is hypothetical here. `p` points to the first element of `arr` by `int32_t *p = arr;`:

```
arr:         1    2    3    4
address:     10   14   18   22
             ^
             |
             p
```

A loop iterates `ARRAY_SIZE` times. In each iteration `*p` is multiplied by `*p` - the dereferenced value of `p` is squared - and stored in the memory location pointed to by `p` by the statement `*p *= *p;`. This modifies one array element in every iteration. The next statement, `p++;`, increments the pointer `p`, not by 1, but by `sizeof(*p)`, equivalently `sizeof(int32_t)` - in this case 4 at a time.

In the hypothetical address above:

```
before 1st iteration: p = 10
after 1st iteration : p = 14
after 2nd iteration : p = 18
after 3rd iteration : p = 22
after 4th iteration : p = 26
```

This can be verified by the following example with 16 bit integer:

```C runnable
#include <stdio.h>
#include <stdint.h>

#define ARRAY_SIZE 4

int main()
{
	int16_t arr[ARRAY_SIZE] = { 0 };
	int16_t *p = arr;

	for (int i = 0; i < ARRAY_SIZE; i++)
	{
		printf("%p\n", p);
		p++;
	}

	return 0;
}
```

Notice that `p` is incremented by 2, or `sizeof(int16_t)`, at every iteration.

**Warning:** When the loop exits in the above example, `p` points to one past the last element of `arr`. That area is outside the declared variables in the program. Accessing that location is undefined behaviour.

```C
int16_t arr[ARRAY_SIZE] = { 0 };
int16_t *p = arr;

for (int i = 0; i < ARRAY_SIZE; i++)
{
    printf("%p\n", p);
    p++;
}

/* printf("%d\n", *p); */ /* Fatal: p points to outside location of declared variable - undefined behaviour */
```
