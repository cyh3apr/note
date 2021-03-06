## 2016 C++ 

- [BOOK: EFFECTIVE MODERN C++](#Effective-Modern-C++11)
----
#### Memory
- type
	- **stack** memory (faster)
		- LIFO
		- local (scope) variable
		- limited space (8M)
		- known length
		- error usage: `int a = 5; int arr[a];`
		- storage direction: up to bottom
	- **heap** memory
		- FIFO
		- (mutual, require booking)allocated variable: `malloc`, `new`
		- unlimited space
		- unknown length
		- storage direction: bottom to up
- Reading memory from stack is faster than reading from heap. Thus, if something is often used but the length is variable, we can allocate much larger memory to it in the beginning to keep it in stack memory instead of using heap memory.

----

####  C++ tutorial
* `cout` is an object of < iostream >

* better coding style:
	- put the braces in new lines
	- function name should be a verb
	- try not to convert types or it may wastes time. It is wrong to write `long - = 5;` because `5` is an integer not a long integer; should be fixed as `long a = 5l;`(put letter 'l' after `5` to make `5` a long integer as well)
	- use less conditional operator, such as `switch`
	- don't use library like .h(because it is for C not C++). If neccessary, include < cstdio > or < cstdlib > instead.

* literal constant : `const int a = 5;` or `const int a(5)`
	-> it is reasonable to write code as: 
		`const int a = 5; int arr[a];`

* starting with '#' means that it is for preprocessor(compiler)

* initialization VS assignment
	- initialization is faster than assignment, for example:
	`int a = 5;` or `int a(5);` is faster than `int a; a=5;`
----

#### C++ Tutorial -- Type
* category
	- Static typing(writing code slower but running code faster)
		- strong typing: doesn't allow implicit type converation.
		- weak typing: It allows implicit type converation by containing lots of "type convertion operator", like C++. (allow this situation->`float a = 1;`)
	- dynamic typing(writing code faster but running code slower and easier to cause bug)
		- (for example: declare as `var`)
		- it is possible to slow the program
		- easy to meta programming

* Giving a specific type first helps to determine the length and the format of that variable

* type
	- primitive type : int, float, ..., literal constant(can't be motified)
	- compound type : pointer/reference, class/structure, string, c_string, ...

* value
<a id="rvalue-bookmark"></a>
	- rvalue: the value should always be on right-hand side (like literal constant) and it doesn't have a name
	- lvalue: can be at the both side
	- example:
		`a = b+c;`
		a, b, c are lvalue while b+c is a rvalue

* pointer
	-	-|\*|&
		--------------|--------------------|----------------------
		type|dereference operator|reference operator
		read as|'value pointed by'|'address of'
		in declaration|pointer|alias (reference)
		in expression|dereference(get the value)|get the address
	
	- `int&&` is a rvalue reference: `int&& b = c+d //c+d is a rvalue`
		- rvalue is supported by compiler itself
		- it is actually a "name swap"
		```c++
		int   c = a+b; // c is a lvalue
		int&  c = a+b; // this will cause compiler error 
				// -> int& c is an alias so it needs some other declaration first
		int&& c = a+b; // c is a rvalue reference. In memory stack, the names instead of the value swap ('c' <--> 'a+b')
		```
	- `int a[10];` -> a is a `const int *`
		- `a[0] = *(a+0)`
		- `a[1] = *(a+1)`
	
	- increase(`++`) and decrease(`--`) operators have greater operator precedence than the dereference operator(`*`)
		- `*p++` = `*(p++)` != `(*p)++`

	- pointers are always the same size no matter what kind of type it points to.
	
	- void pointers
		- are pointers that point to a value that has no type (and thus also an undetermined length and undetermined dereference poroperties)
		- limitation: the data pointed by them cannot be directly dereferneced, so we will always have to cast the address in the void pointer to some other pointer type that points to a concrete data type before dereferencing it.
			- example
			```c++
			void increase (void* data, int psize)
			{
				if(psize==sizeof(char))
				{
					char* pchar;
					pchar = (char*) data;
					++(*pchar);
				}
			}
			```
		- `void* f(void*)` : `void*` in the parentheses means it needs an address (no matter what type this pointer points to), the outside `void*` means this function will return an address.
	
	- null pointer: A null pointer is a value that any pointer may take to represent that it is pointing to "nowhere".

	- `(int)(*a) == (int*)a`

	- new
		- two types:
			- allocation
				- example: `int* a = new int [1000]`
				- In heap memory, assign 1000 memory to it and assign the first memory address of this block of memory to pointer a; Then, store a's memory address to the stack memory
				-> pointer a is stored in the stack while the new requested momemory is allocated in the heap memory.
					-> so the difference between the address of a and the address of the array is large since a is stored in stack (high) while the array is sotred in heap.(low)
				- need to `delete[] a` after done using these memory
				- `delete a` only delete the memory of the pointer not the memory of the array
			- replacement
				- example:
					- `int a[10][10]` => `int* *a = new int* [10];`
					 // then each `int*` points to a block of memory which size is 10.(more nested) Be careful with deleting a.
						- If only `delete [] a` => will only delete the memory of int* and leaves 10\*10 memory assigned to int in the memory.
		- ** use Valgrind to detect memory leak **

	- smart_ptr
		- know when to release the memory
		- types:
			- unique_ptr
				-> only one pointer can access this memory at one time.
				-> it is a class.
				-> usage example: `std::unique_ptr<int> a;`
			- shared_ptr

	- function pointer
		- example
		```c++
		int add(int a, int b);
		(int)(*)(pf)(int, int) = add;
		pf(5,6);
		//can be used in other funtion, like:
		int sort( , , pf)
		{
			pf(_____);
		}
		```

* library
	- namespace : boost->tr2->tr1->std
	- useful library: Armadillo

* struct VS class
	- struct : POD(plain, old, data)
	- class is better for functions.
	- By default, everything is public in struct while everything is private in class


* String & Vector

	- 	_ |String|vector
		-----|-------|-------
		lib|< string >|< vector >
	
	- there is no class called "std::string", however it is actually `std::basic_string<char>`. "String" is only template.

	- using c_string is safer because the size of it is limited. while using string, once the length of the string get much larger, the memory won't be released even the length of the string get smaller without freeing the memory manually.

	- using vector is better because the size is flexible/ also support random 	access like array.

	- nested vector : `vector< vector<int> >`

----

####  call by value/reference/pointer
* call by value: need copying
* call by reference:
	- saving copying (just reading)
	- interacting to the outer scope
		-> so if don't want interact with the outer scope => use `const`
* call by pointer

----

####  Recursion
* design
	1. condition for the termination
	2. recursion equation
		- example: `f(x) = f(x-1) + f(x-2)`
----

####  Template & Function Overload
example code
```c++
	int add(int a, int b)
	{
		return a+b;
	}
	float add(float a, float b)
	{
		return a+b;
	}
	int add(int a, int b, int(*)f(int, int))
	{
		return f(a,b);
	}
	float add(float a, float b, float(*)f(float, float))
	{
		return f(a,b);
	}
```
===> revised
```c++	
	int add(int a, int b)
	{
		return a+b;
	}
	float add(float a, float b)
	{
		return a+b;
	}
	template <typename T>;
	T add(T a, T b, T(*)f(T, T))
	{
		return f(a, b);
	}

	int main()
	{
		add(3, 5, operator +);
		add(3.5, 5.5, operator +);
	}
```
* Template Specialization
	- type checking
		- example code
		```c++
		template <typename T>
		T add(T a, T b)
		{
			return a+b;
		}
		template <> //----> full specialization for (T == string)
		std::string add(std::string a, std::string b)
		{
			cerr<<"not supported"; //the return type is string and the input type are all string, then return "not supported"
		}
		template <typename Q> //---> partial specialization
		std::string add(Q a, Q b)
		{
			... //else if the return type is string but the input type are Q, do something different with the above
		}
		```

* Type classification
	- `template <typename Q>`
	- `template <class Q>` can't be used for 'int' because 'int' is not a kind of class

* `vector<int>` is a type ; `vector` is a template

----

#### Class
- example code
```c++
	class myclass
	{
		public:
			int f(int, int);
			int a, b, c;
	}
	void f(void)
	{
		a = b+c; // it can access a, b, and c because the real statement
				 // is 'this.a = this.b + this.c;'
				 // 'this' is a hidden parameter.
	}
```

* class layer
	- public
	- private
	- protected

* static member
	- example
	```c++
	class A
	{
		public: static int a(int, int);
	};
	```
	- in the example above, integer a is a global function.

* special member function
	- constructor
		- there will be a default synthesized constructor. --> every bits are initialized to 0.
		- can be write as other ways like:
	```c++
	Q(int a1, int b1)
	: a(a1), b(b1)
	{

	} //initialization list
	```
	- destructor
		- form: `~$ClassName(){}`
		- release used memory
		- can use function like `delete`
	- copy constructor
		- example situation needed copy constructor:
			-	`Q q(1,2);`
			-	`Q q2 = q;`
			-	`Q q2(q);`
		- synthesized copy constructior: bitwise copy
		- It is better we use pointer-based copy constructor than synthesized one:
			- example: (copy the value instead of address)
			```c++
			Q(Q& q)
			{
				a = q.a;
				ptr = new();
				*ptr = *q.ptr;
			}
			```
	- assignment operator
		- example
		```c++
		Q& operator = (Q& q)
		{
			a = q.a;
			...
			return *this;
		}
		```
	- move constructor
		- example: `Q (Q&& q) : a(std::move(q.a), std::move(q.b))){}`
	- move assignment

* Friend class/function
	- only defined in the class
	- example code:
	```c++
	class Q
	{
		private:
			int a;
		friend int add(Q); // this make function 'add' able to access the private members of Q
		friend int B::add(int); // make friend with the function of the class
		friend class C; // make friend with a class
		/* cannot make friends with both of the class and the function of it */
	}
	int add(Q q){}; // this is a global function
	```

* Operator overload
	- operator can takes different amounts of parameter(unary/binary/ternary)
	- examples:
		- `cout<<a` equals to `cout.operator<<(a)` or `operator<<(cout, a)`
		- `cout<<a<<b`: `cout<<a` returns another `cout` so this statement makes sense

----

#### Array
* storing 'consecutive data'
* types:
	- static array : fixed size -> if the size is too large, will cause 'allocation failed'.
		- `std::array`
	- dynamic array : the size can grow or shrink 
		- `std::vector`
		- vector adapter : for random access(stack, queue, deque)
			- keep trace of each beginning of the block of the memory

----

####  Linked List/ Tree/ Graph
* Types:
	- single linked list
	- double linked list : for back & forth
* disadvantage: needs extra pointer to record the first and the last nodes
	- can use graph instead(each node can points to more than one nodes)
	- tree is different from graph becaucse tree doesn't allow 'cycle' (just like acyclic graph)
	- tree:
		- Red-Black Tree helps maintain the balance of the tree

----

#### Hash
* hash table gurantees to get the data in constant time
* hash function using '%' is slow. The recommended hash function is '&' and it's much faster than using '%'
* collision policy shoud be built.
* it is a space-consuming data structure.
* when designing hash function, should consider the time complexity.
* pair & tuple:
	- example code
	```c++
	pair <int, int> x;
	x.first = ...; x.second = ...;
	tuple <int, int, string, pair<int, int>> x;
	x.get<0> = 3;
	x.get<1> = 3;
	```

----

#### Class Template
* It is better to define and to implement funtion in template class together. However, it is better to separate function definition and function implementation in non-template class.
* classification
	- non-type:
		- int, pointer(function or member), reference
		- not allow: `std::array<int, a>` except that declare `a` as a constant integer first, like: `const int a = 5;`
		- allow: `std::array<int, 5>`
	- type:
		- example: 
			- to create `my_container<int, std:vector>`: 
				- `template<class T, template<class, class> class Q> //template-template parameter`
				-> instead, can use "parameter pack" : 
					- example: `template <class ... args>`
* full specialization
	- example code
	```c++
	template <class T, class Q>
	class my_container
	{
		T a;
		T b;
	};

	template < >
	class my_container <int, vector<int>> //specializtion
	{}; // do nothing
	```
* partial specialization
	- // please look into HW4/3.cpp -- hannoi tower

* parameter pack
	- example: `template <class ... args>`

* template-template parameter
	- example: `template <class T, template<class a, class b> class Q> class R`
		- class Q is a template with parameter class a & class b. Class R cannot access class a & class b.
* static recursion
	example code : try to get something like `sum<10>::a` -> this value will be 55
	```c++
	template <int K>
	class sum
	{
	public:
		static const int a = K + sum<N-1>::a;
	}
	template <> //specialize the situation when a = 1
	class sum <1>
	{
	public:
		static const int a = 1;
	}
	```
* constant expression can replace template. (`constexpr`)

----

#### Traits 
* Traits -> member types
	- value_type
	- allocator_type
	- size_type
	- pinter
	- iterator
	- reference
	- (... and so on)
* example: `vector<int>::value_type` => will get `int`
* `Traits` are useful when using template: 
	`Q::value_type sum(Q &q){}` => the return type of this sum funcion will be the value_type of Q
	- if you are using your own defined type, use `typedef`:
	```c++
	template <class T>
	class QQ
	{
		typedef T value_type;
	}
	QQ<int>::value_type --> this is 'int'
	```

----

#### is_type
* is_type is a value not trait
- example code
```c++
template <typename T>
class is_vector
{
	static int value = false;
}
template <> //specialize for vector
class is_vector<vector<T>>
{
	static int value = true;
}
is_vector< $sometype >::is_type --> this is a boolean value
```

----

#### parameter pack (variadic template)
* template can take any number of parameters
* example of this kind of template: `std::tuple<int, int, float> a;`
```c++
template <class ... T> // "..." is called 'ellipsis' while 'T' is a parameter pack
class tuple
{};

template <class ... T> //this "..." tells compiler that T is a parameter pack
void f(T ... a) // this "..." tells compiler to expand the parameter pack T
{}
```
* three form of the position of the ellipsis
```c++
	template <class ... U> //"..." put in a template parameter
	void f (U... pargs) //pargs is a parameter pack
	{}
/*-------------------------------------------------------------*/
	template <class ... T>
	void g (T... args) //"..." is put in a declaration
	{
		f(&args ...); //"..." is for expansion and '&' is a pattern
		f2(args ...);
	}

	g(1, 0.2, 'a');
	// => void g(int, float, char)
	// {
	// 	 f(int&, float&, char&);
	//	 f2(int, float, char);
	// }
/*-------------------------------------------------------------*/
	template <class... Args>
	void g (Args... args)
	{
		f0(add(args...));
		f1(add(args)...); // add is a pattern
		f2(add(args...) + args...) // the last "..." expands (add(args...)+args)
	}
	g(1, 2, 0.5); 
	// => f0(add(1, 2, 0.5))
	// => f1(add(1), add(2), add(0.5));
	// => f2(add(1,2,0.5)+1, add(1,2,0.5)+2, add(1,2,0.5)+0.5)
/*-------------------------------------------------------------*/
	template <class ... U>
	class tuple
	{};

	template <int N, class T, class... U> //N is the index of the parameter pack U
	// homework : use static recursion to extract template pack from one to other
```
* a parameter pack can be (1) a bunch of nontype (2) a bunch of type (3) a bunch of template. can't be mixed together.
	- example: `template<typename ... Ts, int ... N>`

----
#### Basic Components
- `std::pair`
	- library: `#include < utility >`
```c++
	template <class T1, class T2>
	class pair
	{
	public:
		T1 first;
		T2 second;
	}

	std::pair<int, double> my_data;
	my_data.first = 5;
	auto a = my_data.first; //or my_data.second
	auto my_data_1 = std::make_pair(5,0.5); //let compiler guess which types are they
	auto my_data_2 = std::make_pair<int, double>(5,0.5); //set the type manually
```
	- pair can only take two types, that is, the first one and the second one. If you need more than 2 types, then:
		1. use static recursion like: `std::pair<int, std::pair<double, std::pair<>>> my_data;`
		2. use `std::tuple` instead
- `std::tuple`
	- library: `#include < tuple >`
```c++
	template <class... Types>
	class tuple
	{
		//...
	}
	tuple<int, int, int> data;
	std::get<0>(data) = 5;
	auto a = std::get<0>(data);
```
	- HW8 : use `std::get`
	- to get the size of tuple : `tuple_size`
		- example
```c++
		#include <iostream>
		#include <tuple>
		template <class T>
		void test(T t)
		{
			//tuple size can be known without t (only need to know T)
			int a[std::tuple_size<T>::value];
			std::cout<<std::tuple_size<T>::value<<'\n';
		}
		int main()
		{
			test(std::make_tuple(1,2,3.14));
		}
```
----

#### Sequence Container
- array, vector, deque, stack, queue...
- array
```c++
	template<class T, std::size_t N>
	class array {};
	std::array<int, 4> a; // this is better than the declaration like 'int a[10]'
	// 'a.front' can get the first element of array; 'a.back' can get the last one
	// 'a.data()' is used to get the address of array. '&a' is not working in this place.
```
- vector
```c++
	template<class T, class Alloc = std::allocator<T>> //Alloc: relate to data alignment. (speed)
														// std::allocator, boost::aligned_alloc...
	class vector{};

```
	- `push_back()` is always 'called by value' since it copies the value before push it into the vector, however, `emplace_back()` directly construct with the element in the place
	- `resize()` can be used when releasing the memory while `reserve()` can be used at the beginning to get the well-estimated amount of memory
		- `resize()` may not only change the size of the data but the location of it.
	- swap to release skill
```c++
	vector<int> b;
	a = std::move(b); //Using 'a' is actually using 'b'
```
	- `std::swap`

----
#### Associative Container
- `std::set`
```c++
	template<class T, class Comp = std::less<T>>, class Alloc = allocator<T>>
	class set
	{};

	std::set<int> a {1,2,3,4,-1}
```
- `std::multiset` is like 'set'
- `std::map` :
	- datatype is 'pair' (<'key', 'value'>)
	- get thing from map : use '.find($key)' and you will get the pointer to the data in return
- unordered_map
```c++
	template<class key, class T, class Hash = hash<key>, class Equal = equal_to<key>, class Alloc = ...>
```
- iterator
	- random access : <, >, <= / += / []
	- 	- bidirectional: decremental `a--`, `--a`
		-	- forward: multi_pass
			-	- input ==/!=/\* -> rvalue
				- output \* -> lvalue
	- That `*a==*b` is true doesn't guarantee that `a==b` is true.

----

#### Concepts
- in 'boost' library
- make compiler error message more readable
- example

```c++
	boost::concepts
	template<class T> requires std::less<T>
	T min(T x, T y)
	{
		//...
	}
```

----

#### Boost Traits

----

#### functor (function object)
- be able to maintain states

```c++
class QQ
{
	int n=0;
	int operator()(int a)
	{
		n++;
		return a+n;
	}
}

QQ q;
q(0); //=> 1
q(0); //=> 2
```

- lambda expression, function, functor, and function pointer are called *callable*. 
- 'callback' function = the function is called by others

----

#### std algorithm
- `for_each`
	- take only one parameter (`UnaryFunction`)
```c++
struct Sum
{
	Sum(): sum(0){}
	void operator()(int n){sum+=n;}
	int sum;
};
// calls Sum::operator for each number
Sum s = std::for_each(nums.begin(), nums.end(), Sum());
```
- range for: `for(auto&a : A)`
- `std::transform`
```c++
template< class InputIt, class OutputIt, class UnaryOperation >
OutputIt transform( InputIt first1, InputIt last1, OutputIt d_first,
                    UnaryOperation unary_op );
//to do: lower case-> upper case
string a("abc");
cap q;
std::transform(a.begin(), a.end(), a.begin(), q)
class cap
{
	char operator()(char a)
	{
		return a-26;
	}
}
```
- `copy`
- `std::unique`
	- size will be the same: (1,2,1,3,1,4) -> (1,2,3,4,0,0,0), so should be careful to resize it.
- c-style `qsort` is slower than cplusplus-style `sort` since `qsort` needs to dereference the funtion and value while `sort` doesn't need to do that(use template, functor).

----

#### Lambda Expression
- book *C++11/14 Boost*: page 31
- anonymous functor (without specific name)
- `[]` usually it sees as an identifier, however, in lambda, we keep it empty.
- the type of lambda funtion : `std::function`
- cannot be reused
- example
```c++
[](int a, int b)->int//return type: int
{
	return a+b;
}(3,5);
/*-----------------------*/
int a=5;
[a](int b)
{
	return b+a; //a: pass by value
}(3); //b is 3
/*-----------------------*/
int a=5;
[&a](int b)
{
	return b+a; //a: called by reference
}(3);
/*-----------------------*/
[=](int b)
{
	return b+a;
}(3);
/*-----------------------*/
//if you write a lambda function in a class, adding "this" in the bracket will allow you to reach all the variables in that class 
[this]() //or [*this]()
{
	//...
}
```
- generic lambda c++11
- in c++14
```c++
[](auto a, auto b)->auto
{
	return a+b; 
}(3, 5);

auto a = [](auto a, auto b){return a+b;};

std::function<int(int, int)> //use 'std::function<$return_type(parameters)>' to wrap the function
a = [](int a, int b){return a+b;}; // like giving a name "a" to the function
a(3,5); //usage
```
- do initialization in brackets
```c++
int x=4;
cout<<[&r=x, &x](){return r+x;}(); //4+4
cout<<[&r=x, x=x+1](){return r+x;}(); //4+5, in 'x=x+1', the first x is new x while the x in the right is the original x as outside.
```
----

#### std::function / std::bind / std::thread
- function
```c++
int (*f)(int, int) = sum;
std::function<int (int, int)>
a = [](int a, int b){return a+b;}; // a is a function
a = f; //rename the function
auto a = &sum;
```

- bind
	- always use 'pass_by_value'. If needed, can use `std::ref`(example is shown below)
```c++
auto a = std::bind(sum, 3, 5); // sum: callable
a(); //-> actually do sum(3,5);
/**********************************/
auto b = std::bind(sum, 1); // sum: callable
b(6); //-> do sum(1,6)
/**********************************/
int sum_minus(int a, int b, int c)
{
	return a+b-c;
}
auto c = std:bind(sum_minus, 1, 2);
a(3); //-> do sum(1,2,3) -> 1+2-3
/**********************************/
auto d = std::bind(sum_minus, std::place_holder::_3, std::place_holder::_2, std::place_holder::_1);
d(1,2,3); //-> sum(3,2,1) => the first parameter will go to place_holder::_1
/**********************************/
//if sum is a member function in class QQ
auto c = std:bind(&QQ::sum, 1, 2);
/**********************************/
void increment(int& a, int& b)
{
	a++; b++;
}
auto a = bind(increment);
int b = 3; int c = 4;
a(b,c);
cout<<b<<c; //still output "34" not "45" since bind always use pass_by_value
a(std::ref(b), std::ref(c));
cout<<b<<c; // output "45"
```
- thread
	- like `bind`, always pass_by_value(need to copy)
```c+
vector<vector<int>> all_vector(10);
void sort_vector(vector<int>&){}

for_each(all_vector.begin(), all_vector,end(), [](auto job){std::thread(sort_vector, job);};
//the above is same as the below
for(auto&a : all_vector)
	std::thread(sort_vector, a);
```
	- thread can't make sure when all the job will be done
		- add `t.join();` //t is a thread

----

#### C++ object model/inheritance (virtual, polymorphism)
- inheritance
```c++
class A
{
public:
	int x;
	void f();
}
class B: public A //A is base
{
public:
	int y;
	void p();
}
```
	-![alt text](image/inheritance-1.png)
	- casting
		- up casting (Derived -> Base) //(B->A): convert B to A
```c++
B b;
(A)b.x; //legal
(A)b.y; //illegal
```
		- down casting (Base -> Derived) //is dangerous
```c++
A a;
(B) a.y; //since a has no y, this doesn't work.
(B) a.x; //this works.
```
	- don't use like `(B) a.x;`, use :
		1. `static_cast`(invodke type convertion operator), 
		2. `dynamic_cast`(between classes of the smae hierachy), 
		3. `reinterpet_cast`(for pointers), 
		4. `const_cast`(add, remove, modify the variable)
	- example
```c++
char c;
static_cast<int>(c);

dynamic_cast<A>(b);
```
----

#### virtual / polymorphism 虛擬/多型

- virtual function is actually a real function. The existance of virtual function is for polymorphism.
- polymorphism can store objects of different types together.
```c++
vector<Base *> pVec;
Derived1 x;
Derived2 y;
pVec.push_back(&x);
pVec.push_back(&y);
for(auto& p: pVec)
{
	cout<<*p;
}

Base::operator<<
{
	this->y; //this won't work because y is in Derived2 not Base
}
```
```c++
class shape
{
	void display() virtual
	{
		cout<<"shape";
	}
};
class Rect :public shape
{
	void display() virtual
	{
		cout<<"Rect";
	}
}
class Square: public Rect
{
	void display_old()
	{
		shape::display();
	}
	void display() virtual
	{
		cout<<"Square";
	}
}

int main()
{
	shape* p;
	shape s;
	Rect r;
	Square ss;
	p=&r; // get the virtual pointer to the virtual table of r
	p.display(); //will display 'shape' if 'display()'in Rect is not declared as 'virtual'

	B *tr = &ss
	//want to do ptr->shape::area()
	ptr->display(); //it doesn't work since it is declared virtual.
	ptr->display_old(); //it works.
}
```
----

#### static polymorphism

- CRTP: Curiously, Recurring, Template, Pattern
- `boost::iterator`
- example
```c++
template<class Derived>
class A //Base
{
public:
	void interface()
	{
		static_cast<Derived*>(this)->imp(); //convert type: A -> Derived
	}
};
// Derived 1
class B: public A<B>
{
public:
	void imp()
	{
		cout<<"Derived1: B";
	}
};
// Derived 2
class C: public A<C>
{
public:
	void imp()
	{
		cout<<"Derived2: C";
	}
};

template <class T>
void f(A<T> t)
{
	t.interface();
}

int main()
{
	B b;
	C c;
	f(b);
	f(c);
}
```

----

## Learning note from homework
* [cppreference](https://en.cppreference.com/w/)
* do-while loop executes funtions in the loop at least once
* replace 'int a;' by 'auto a;' // let compiler to decide the type
* try to make everything template and to make every type 'auto'
* static member function example:
```c++
class test
{
	static int get_value(){return 3;}
}
int main()
{
	cout<<test::get_value()<<enld;
}
```
* use <random> library in C++11 to generate random numbers

```c++
	int main()
	{
		std::random_device rd; //generator
		std::default_random_engine gen = std::default_random_engine(rd());
		std::uniform_int_distribution<int> dis(1,10); //distribution
		dis(gen); //get the random number
	}
```

* `const function` always has `const` at the end of function declaration, and it means "reading of a class variables is OK inside of the function, but writing inside of this function will generate a compiler error." In C++11, if you want to writes some variables in a "const function", mark these variable "mutable" first.
* Use of `const`:
	- `const int * p_num` === `int const * num`, declares that 'p_num' is a variable pointer to a constant integer.
	- `int * const p_num` declares that 'p_num' is constant pointer to a variable integer
	- `int const * const p_num` declares that 'p_num' is constant pointer to a constant integer.
* `try` and `catch` can control the damage but make the program slower.

----
<a id="Effective-Modern-C++11"></a>
## Effective Modern C++11

#### Deducing Types

```c++
template<typename T>
void f(ParamType param)

f(expr); //deduce T and ParamType from expr
```

- T is dependent on the type of *expr* and the form of *ParamType*.
	- "Universal Reference" : 可以同時提供lvalue和rvalue窗口. Such parameters are declared like rvalue references, but they behave differently when lvalue arguments are passed in.
	- case 1: ParamType is a Reference or Pointer, but not a Universal Reference	
		- if *expr*'s type is a reference, ignore the reference part.
	- case 2: ParamType is a Universal Reference
		```c++
		template<typename T>
		void f(T&& param)//param is now a universal reference
		```
		- if *expr* is an lvalue: both T and ParamYpe are deduced to be lvalue references.
			- for example : `int x = 27; f(x);` -> x is lvalue, so T's and param's types are `int&`.
		- if *expr* is rvalue: same as case 1.
			- for example : `f(27);` -> 27 is rvalue, so T is `int`; param's type is `int&&`.
	- case 3: ParamTye is neither a Pointer nor a Reference
		```c++
		template<typename T>
		void f(T param); //param is passed by value
		```
		- ignore reference part, const part, and volatile.
	- arrays
	```c++
	template<typename T>
	void f(T param); //template with by-value parameter

	template<typename T>
	void ff(T& param); //template with by-reference parameter

	const char name[] = "ABC";
	const char * ptrToName = name;
	
	f(name); //name is array but T deduced as const char*
	ff(name); //the type deduced for T is the actual type of the array. 
				//In this case, T type = 'const char[3]', param type is 'const char (&)[3]'
	```
	- function arguments
	
	```c++
	void someFunc(int, double); //type is void(int, double)

	template<typename T>
	void f1(T param); //param passed by value

	template<typename T>
	void f2(T& param); //param passed by ref

	f1(someFunc); //param deduced as ptr-to-function; type is 'void (*)(int, double)'
	f2(someFunc); //param deduced as ref-to-function; type is 'void (&)(int, double)'
	```
	
- `decltype`
	- `auto` will strip off the reference . `decltype(auto)` (in C++14) says that `decltype` rules should be used during the deduction, thus, it is able to truly return what we want to return. 
	- Putting parentheses around a name can change the type that decltype reports for it.
	```c++
	decltype(auto) f1()
	{
		int x = 0;
		return x; //decltype(x) is int, so f1 returns int
	}
	decltype(auto) f2()
	{
		int x = 0;
		return (x); //decltype((x)) is int&, so f2 returns int&
					//return a reference to a local variable
	}
	```
----

## To-Do List
- while using template: typename VS class
- type&non-type
- container difference {set, list, vector, deque}
- fina a way to do like 'for i in range(10)'
- `future`
- `packaged_task`
- hw5 use inheritance
- `lower_bound`, `upper_bound`, `equal_range`
- `Boost::any` : can store any type but not so fast

----

## Book List
- *C++ concurrency in action : practical multithreading*
- *modern c++ design: generic programming and design atterns applied*
