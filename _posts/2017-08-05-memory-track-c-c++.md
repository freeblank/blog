---
layout: "post"
title: "Memory Track In C/C++"
date: "2017-08-05 17:32"
---

# Game Crash
Hey, the game crash again! it's iPhone9 the best mobile in the world, we need make the game running on iPhone5, what's wrong about the app! have you heard about this at your office? The scene is too big and colorful, so many sprite we need show, sound and music is much rich and so on you said. Always you will got the answer is that i don't care about, i just want the app can run on iPhone5, we need more users can play our game. How can we solve the problem?
<!--more-->
# Memory Track
At first, you need to know who ate the memory and how much they have ate, you can get the answer with some tools which is very profession. At the point i want to discuss is how can we do it by myself, As we know the memory have been taken by stack, heap, static and so on, but most memory will be taken by heap, one solution is manage memory which you can get the answer by memory manager at the beginning at the game project. But now my project have almost done, we need to search all the codes to find the keys new and malloc to replace them. Unfortunately it's too hard to do the work, so we need find some solution to do it. Maybe we can use some macros?

> Two standard C preprocessor macros are the key: the \__FILE__ and \__Line__ macros

As we know you manage memory in C with three functions: malloc realloc free, so we need hake the functions:

```c
#define malloc(size) (malloc_custom(size, __FILE__, __LINE__))
#define realloc(ptr, size) (realloc_custom(ptr, size, __FILE__, __LINE__))
#define free(ptr) (free_custom(ptr))
```

for example:
```c
char *pStr = (char *)malloc(sizeof(char)*2000);
free(pStr);
// will hake to
char *pStr = (char *)malloc_custom(sizeof(char)*2000, __FILE__, __LINE__);
free_custom(pStr)
```

now you can create a map to record memory at malloc_custom with the key is pointer of pStr and the value is \__FILE__ and \__LINE__, another map is with the key is \__FILE__ and \__LINE__ and the value is alloc size. reduce alloc size at free_custom

```cpp
void* malloc_custom(size_t size, const char* filename, int line) {
    void *ptr = malloc(size);
    MemoryTrack::getInstance()->recordMemory(ptr, size);
    MemoryTrack::getInstance()->recordMemoryMore(ptr, filename, line);

    return ptr;
}

void* realloc_custom(void* ptr, size_t size, const char* filename, int line) {
    MemoryTrack::getInstance()->removeMemory(ptr);

    ptr = realloc(ptr, size);
    MemoryTrack::getInstance()->recordMemory(ptr, size);
    MemoryTrack::getInstance()->recordMemoryMore(ptr, filename, line);

    return ptr;
}

void free_custom(void *ptr) {
    MemoryTrack::getInstance()->removeMemory(ptr);

    free(ptr);
}
```

In C++ there are two operators: new and delete, we will hake and override them:

```cpp
void* operator new(size_t size) {
    void* ptr = malloc(size);
    if (g_bTrack) {
        g_bTrack = false;
        MemoryTrack::getInstance()->recordMemory(ptr, size);
        g_bTrack = true;
    }

    return ptr;
}


void* operator new[](size_t size) {
    void* ptr = malloc(size);

    if (g_bTrack) {
        g_bTrack = false;
        MemoryTrack::getInstance()->recordMemory(ptr, size);
        g_bTrack = true;
    }


    return ptr;
}

void operator delete(void* ptr) throw() {
    if (g_bTrack) {
        g_bTrack = false;
        MemoryTrack::getInstance()->removeMemory(ptr);
        g_bTrack = true;
    }

    free(ptr);
}
void operator delete[](void* ptr) throw() {
    if (g_bTrack) {
        g_bTrack = false;
        MemoryTrack::getInstance()->removeMemory(ptr);
        g_bTrack = true;
    }

    free(ptr);
}

#define new new(__FILE__, __LINE__)
```

for example:

```cpp
Object *ptr = new Object();
delete ptr;
// will be hake to
Object *ptr = new(__FILE__, __LINE__) Object();
delete ptr;

// how about this?
Object *ptr2 = new(ptr) Object();
// will be hake to
Object *ptr2 = new(__FILE__, __LINE__)(ptr) Object();
```

At last we get the code clearly in error, if i just want to record the size, i will just need override operator new, but i need to record \__FILE__ and \__LINE__, how to replace replacement new like "new(ptr) Object()"? it's clearly that "new" must be the last word to make sure it compile successful. If the result of operator new can be an argument for a function, that will be make sense, The astute you will notice that we can define a class called "TrackInfo" store \__FILE__ and \__LINE__, an overloaded operator (like &,*,+,-, whatever it is) with the arguments "TrackInfo" and result of operator new, why not define an operator in "TraceInfo"? you will find the answer soon. Ah, there will be a new question? the type of result of operator new i don't know, so i need make the overloaded operator to a template function. if i define in "TraceInfo" which will be a template class, how can i make the macros! so the result will be:

```cpp
class TraceInfo {
public:
    TraceInfo(const char *i_filename, int i_line) : filename(i_filename) , line(i_line) {}

public:
    std::string filename;
    int line;
};

template<class T> inline T* operator&(const TraceInfo& trace_info, T* ptr) {
    MemoryTrack::getInstance()->recordMemoryMore(ptr, trace_info.filename.c_str(), trace_info.line);
    return ptr;
}
```

now we can track the memory, we need use the macros to record, and if we don't need it, we need undefine the macros to make sure it won't affect other codes like c++ standard library.
There is a test code here:

```cpp
int main(int argc, const char * argv[]) {
    int* i_objectTest1 = new int[20];
    i_objectTest1[1] = 20;
    MemoryTrack::getInstance()->dump();

    delete[] i_objectTest1;
    MemoryTrack::getInstance()->dump();

    ObjectTest *i_objectTest2 = new ObjectTest();
    ObjectTest2 *i_objectTest3 = new ObjectTest2();
    MemoryTrack::getInstance()->dump();

    ObjectTest2 *i_objectTest4 = new(i_objectTest3) ObjectTest2();
    MemoryTrack::getInstance()->dump();

    delete i_objectTest2;
    delete i_objectTest4;
    MemoryTrack::getInstance()->dump();

    TestNew *i_testNew = new TestNew();

    MemoryTrack::getInstance()->dump();
    delete i_testNew;
    MemoryTrack::getInstance()->dump();

    return 0;
}
```

result
```cpp
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/main.cpp:29		80

dump memory info:
all memory is free

dump memory info:
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/main.cpp:36		8
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/main.cpp:37		12

dump memory info:
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/main.cpp:36		8
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/main.cpp:37		12

dump memory info:
all memory is free

dump memory info:
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/TestNew.cpp:14		80
/Users/FreeBlank/Documents/GitHub/MemoryTrack/MemoryTrack/main.cpp:47		1

dump memory info:
all memory is free
```

you will get the full source here[Github MemoryTrack](https://github.com/freeblank/MemoryTrack.git)
