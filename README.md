java c
Assignment 4: unique
Making and Using a Binary-Tree Abstract Data Type
CSE 13S, Fall 2024
1 Introduction
In this assignment you are going to write a program, unique, that will count the number unique entries in an unordered list of numbers. Here is an example of using unique:
$ ./unique 314 96064 76 76 95064 76 Enter      ←− run the program
3 76      ←− program output: one line per unique integer, sorted by value and prefixed by count
1 314
2 95064
The program’s output reports that the input data has three 76s, one 314, and two 95064s. Regardless of their input order, the numbers are reported in ascending numerical order (76 → 314 → 95064). Each number is prefixed by the number of times that it appears in the input.
2 Binary Search Tree
Your program will store data in a “binary tree.” A binary tree is a data structure that has a single root node and that satisfies the requirement that each node has 0, 1, or 2 children. Below is an example of a binary tree with three nodes. The 314 node has two children (left and right), and the other two nodes have no children (indicated by NULL).

The binary trees that you use in this assignment will have constraints that are based on the values of the nodes’ keys. Specifically, starting at any node, all keys found in the node’s left subtree must be less than the node’s key (for example, in the figure above, 76 < 314). Also, all keys found in the node’s right subtree must be greater than the node’s key (for example, in the figure above, 95064 > 314). When a binary tree meets these key constraints, it is a “binary search tree,” and it can be used to quickly store and search for keys.
An important observation about binary search trees is that for all sets with more than one key, there are several different-shape trees that represent the the set. For example, sets with three different keys can be stored in five differently shaped trees. Those five trees are the binary search tree on the previous page and the four additional trees below.

The shape of a binary search tree is determined by the order in which keys are added to the tree. There are six different orders for three keys, and these create five different tree shapes: four of the key orders create the four trees above, and the two remaining key orders, (314, 76, 95064) and (314, 95064, 76), create the tree on the previous page. Remember this fact when testing your program. The order of the keys can change the shape of the binary search tree that is generated.
In all five binary search trees, the constraints on the key values still are met.
2.1 Searching
You can search any binary search tree for a desired key with iterative steps:
1. Start at the root.
2. Check whether the desired key is greater-than, equal-to, or less-than the node’s key.
3. If the keys are equal, your search is done.
4. Otherwise, repeat the check, but with the node’s left child node (if the desired key is less than the node’s key) or with the node’s right child node (if the desired key is greater than the node’s key).
□ To do: This is an exercise. You don’t need to turn it in. Using the five binary search trees shown earlier, start at the root and search for the node with key 76. That means going left when the desired key is less than the node’s key and going right when the desired key is greater than the node’s key. Do you see how the algorithm always makes left/right decisions that lead to the desired key? Also do you see that the decisions always follow a downward path?
□ To do: (Another exercise.) Now search for a key that is not in the tree, such as 500. In that case, the search reaches a NULL value. So either the algorithm will find the desired key, or its search will be unsuccessful and will end at a NULL pointer (root, left, or right).
2.2 Adding a Key
One can add a key to a binary search tree using a similar search. However, your program will perform. one of two actions depending on whether the tree already contains a node with the desired key.
If a key search successfully finds a node with the desired key, then increment the node’s counter. Oth-erwise, create a new node for the key and add the node to the tree where the NULL pointer was found.
The pseudocode below shows how to add a key to a binary search tree. To do the last line of the algorithm, you need to find a way to update the appropriate NULL pointer.
tree_add(KEY):
Search the binary tree for KEY. If the KEY is not found, then save a pointer to the NULL pointer for later updating. We mean to point to a field of a Tree or Node. We don't mean to point to a local variable.
If the search above finds a node that already has the KEY, then
Increment that node's counter.
Else
// The search ended at a NULL pointer.
Create a new node.
Set the new node's key to KEY.
Set the new node's counter to 1.
Overwrite the NULL pointer to point to the new node.
Being able to overwrite a NULL pointer that is encountered during the search is a critical part of your algorithm. So if the search ends with a NULL pointer, save a pointer to that pointer to allow later updating.
Of course, we mean that you need to be able to update a part of the tree data structure: the root field of the Tree or the left or right field of a Node. We don’t mean that you should overwrite any local NULL pointer variable since it is not part of the tree data structure and won’t affect it.
2.3 Traversing a Binary Tree
“Traversing” a tree means visiting all of its nodes.
There are many kinds of traversal algorithms. unique is going to use two of them: an “in-order” traversal (for printing a tree) and a “post-order” traversal (for freeing a tree).
Since a binary tree is a recursive data type, you can traverse the entire tree using a recursive function (a function that calls itself). An “in-order” traversal is shown below. You would call the function, passing the root of the tree as the node parameter. “Visiting” a node means doing something with the node, such as printing it.
traverse_in_order(node):
If the node is not NULL, then
traverse_in_order(node->left)
visit the node (print it)
traverse_in_order(node->right)
The “in” part of “in-order traversal” means that the “visiting” part of the algorithm is performed in-between the recursive function calls. Remember that our definition of a binary tree requires that all keys of the left subtree come before the key of the subtree’s root? That means that by recursing down the left child before visiting the subtree’s root, all of the lower keys will be printed before the root’s key is printed. The same argument goes for the right subtree. Therefore, all keys will be printed in sorted order.
Our traversal algorithm’s “visiting” part could be placed at two other places: after the two recursive function calls or before them. Below are algorithms that do “post-order” and “pre-order” traversals. Compare them with each other and with the in-order traversal. Notice that the left subtree always is traversed before the right subtree, but each of the algorithms visits the node at different times.
traverse_post_order(node):
If the node is not NULL, then
traverse_post_order(node->left)
traverse_post_order(node->right)
visit the node (free it)
traverse_pre_order(node):
If the node is not NULL, then
visit the node
traverse_pre_order(node->left)
traverse_pre_order(node->right)
So the node is visited before both recursive calls with a “pre-order” traversal, the node is visited after both recursive calls with a “post-order” traversal, and the node is visited between the recursive calls with an “in-order” traversal.
• in-order traversal: You will use this when printing the nodes of a binary search tree in sorted order.
• post-order traversal: You will use this when freeing the nodes of a tree.
• pre-order traversal: Not used in this assignment, but can be used when copying a binary tree.
3 Abstract Data Types in C
The C programming language supports a few native data types such as int, char, and double. We can use variables of these data types directly in expressions and function calls.
C also lets us construct complicated data types like trees, but these data types cannot appear directly in expressions or function calls. So to make it easier to use these complicated data types, one provides several functions to access or manipulate the complicated data type. We call such a collection of types and functions an “abstract data type” (ADT).
An Abstract Data Type (ADT) groups one or more type definitions with one or more function definitions. Sometimes the ADT provides little or no direct access to the ADT’s internal variables.
The ADT for our binary search tree is defined in the file tree.h. You will see two type definitions and five function definitions. First, there is the definition of the Node data type. Each Node has four parts: the node’s key, a count of the number of times that that key was seen, and two pointers to the left and right 代 写CSE 13S, Fall 2024 Making and Using a Binary-Tree Abstract Data Type Assignment 4C/C++
代做程序编程语言children of the node (which are Nodes themselves). If a node has no left child, then its left pointer is NULL; if the node has no right child, then its right pointer is NULL. then
typedef struct s_node Node;      ←− the type is called Node
struct s_node {
int key;      ←− the integer key
int count;      ←− the number of times that the key was seen
Node *left;      ←− pointer to the left child or NULL
Node *right;      ←− pointer to the right child or NULL
};
Next is the definition of the Tree data type. It has only a single field, which is a pointer to the root node of a tree. Why do we define a second structure for a tree if we already have a structure for a node? Because an empty tree is represented by a NULL root field, and we still need a data structure to store the NULL pointer.
typedef struct s_tree Tree;      ←− the type is called Tree
struct s_tree {
Node *root;      ←− a Tree has a single root Node.
};
Finally, here are the function definitions for the binary search tree ADT. These functions let you create (“alloc”) a new tree and destroy (“free”) it. You also can add a new key to the tree and print the tree. A final function lets you dump a visual representation of the tree for debugging purposes.
Tree *tree_alloc(void);
void tree_add(Tree *tree, int key);
void tree_print(Tree *tree);
void tree_free(Tree **p);
void tree_dump(Tree *tree);
You can define more functions that you find useful.
4 Your Task
4.1 Copy the Resource Files
□ To do: Synchronize the resources repository on your laptop’s Multipass instance. Then copy the files for asgn4 into your repository.
$ cd      ←− go to your “home” directory
$ cd f24/13s/resources      ←− go to your laptop’s copy of the resources repository
$ git pull      ←− synchronize your laptop’s copy of the resources repository
$ cd ..      ←− this changes your current directory to ∼/f24/13s
$ cp resources/asgn4/* yourcruzid/asgn4      ←− copy the files for asgn4
$ cd yourcruzid/asgn4      ←− get ready to run the demo executable and write your program
4.2 Submit a draft for design.pdf
□ To do: Read through this document and make notes about what you need to do. In particular, pay attention to sections 4.4 and 4.5.
□ To do: Read through the starter file tree.c. Notice that there are eight functions. You will be writing new “bodies” for six of them. □ To do: Read through the starter file unique.c. Notice that there are eight functions. You will be writing new “bodies” for six of them.
□ To do: Create a draft of your design document, design.pdf. The purpose of this draft is to document your initial ideas about the design. Your draft should include pseudocode for your the seven functions that you will be writing (six functions in tree.c and main() in unique.c). Read through sections 4.4 and 4.5 and look in the file tree.c for more information on the functions. You can add more functions to your design if you want.
For each of your program’s functions (there should be at least eight) see sections ?? and 4.5 and file tree.c. Here is the structure of your design.pdf document:
1. Purpose: What is the function’s purpose?
2. Parameters: What are the names, types, and uses of the functions input parameters?
3. Return Value: What is the type and purpose of the function’s return value? (If there is one.)
4. Pseudocode: Give the pseudocode for your function. You do not need to provide syntactically legal C. Steps can be sentences. The purpose here is to think about what each function will do, and to write down those thoughts.
□ To do: add/commit/push/submit commit ID for the design.pdf by Tuesday, November 12th at 11:30 pm. Note: You can submit this part before the due date. We are asking for a draft. That means your best initial guess of the design of your program’s functions. Submit that draft, and the move onto the next steps. We are giving you until Tuesday in case you have questions for tutors, but really it would be best to starting writing your program’s functions before the draft deadline.
4.3 Make a Makefile
Why do you use a Makefile? Because you need to make two executable files: unique and unittests.
Unlike some other programming languages, if you have multiple C program files, you create an executable file through multiple intermediate “object files” (unique.o and tree.o are combined to make unique, and unittests.o and tree.o are combined to make unittests). The steps are shown below. These are what we want the make command to do. (Add the -g option to the clang command to make it possible to use a debugger.)
What make Does when Compiling Two Executables:
clang -c -g tree.c      ←− create the “object file” tree.o from tree.c
clang -c -g unique.c     ←− create the “object file” unique.o from unique.c
clang -c -g unittests.c     ←− create the “object file” unittests.o from unittests.c
clang unique.o tree.o -o unique      ←− create “executable file” unique from .o files
clang unittests.o tree.o -o unittests      ←− create “executable file” unittests from .o files
□ To do: Create the file Makefile to make the unique and unittests executables.
• Remember that the make program will make the first target that is listed in your Makefile, along with any other dependent files. (The “target” is underlined in the dependency line below.) So if the first dependency line of a Makefile is the one below, then the Makefile will execute commands to make unique and unittests (which is what you want).
When looking at a Makefile dependency line, remember, “this depends on that .”
.PHONY: all      ←− tell make that all isn’t really a file that gets made
all: unique unittests
• Remember that Assignment 3 and the lecture on October 28 demonstrated using a Makefile.
□ To do: Run your Makefile to compile unique and unittests.
Running your Makefile
$ make      ←− That’s all that’s needed.
4.4 Write and Test the Functions of the Tree ADT
Look in tree.c for the details about each of these first six functions. I also ask you to run some tests.
1.
□ To do: Write tree_print() and tree_print_node().
□ To do: Confirm that the first six unit tests pass when you run ./unittests 6.
$ ./unittests 6 Enter      ←− run the first six tests
Running 6 of 18 tests
test 1:
expect no output
-----
-----
test 2:
expect one line "1 100"
-----
1 100
-----
(omitting additional output)
If a test fails, then you will need to open unittests.c and look at the code on and near the offending line.
2.
□ To do: Write tree_alloc().
□ To do: Confirm that the first seven unit tests pass when you run ./unittests 7.
3.
□ To do: Write tree_add().
□ To do: Confirm that the first 12 unit tests pass when you run ./unittests 12.
4.
□ To do: Write test_free() and test_free_node().
□ To do: Confirm that all 18 unit tests pass when you run ./unittests 18.
4.5 Write unique.c
□ To do: Write check_number(). See unique.c for the description.
□ To do: Write main(). The pseudocode is below.
main():
allocate a tree
for i in (1 ... argc - 1)
if argv[i] is "-d" then
skip this argument, but note that -d was seen
else
check_number(argv[i]);
tree_add(tree, atoi(argv[i]));
if -d was seen then
tree_dump()
else
tree_print()
free the tree
4.6 Test Your Program
To receive full credit, the output of your program must match the output of a reference program that we provide. You can use the diff program as you did in prior assignments to compare the output of the reference program and the output of your program.
You can use the -d option to generate a textual representation of a binary tree. (The root is on the left. Tilt your head to the left to see the tree.)
$ ./unique -d 314 95064 76 76 96064 76 Enter      ←− run the program with “-d”
/ NULL      ←− program output: representation of a binary tree (tilted to the left)
/ 95064 (x2)
\ NULL
< 314 (x1)
/ NULL
\ 76 (x3)
\ NULL
As with Assignment 3, we’ve given you a test script. that automates the comparison! The test script. is called runtest.sh. Here are three examples of running the test script.
Testing in a Multipass Shell
$ source runtest.sh 314 76 95064      ←− Run a comparison test with normal output
$ source runtest.sh -d 95064 314 76      ←− Run a comparison test with debugging output
4.7 Update your design.pdf
□ To do: Did your final program deviate from the draft of design.pdf that you submitted? Update design.pdf to match your program.
4.8 Submit: add/commit/push/submit commit ID
You need to submit these files:
• design.pdf
• Makefile
• unique.c
• tree.c
• unittests.c
• tree.h
□ To do: git add all of the files above.
□ To do: git commit -m 'reason'      (you can give any reason for this commit)
□ To do: git push
□ To do: Submit your final commit ID to Canvas by Wednesday November 13th at 11:30 pm.







         
加QQ：99515681  WX：codinghelp  Email: 99515681@qq.com
