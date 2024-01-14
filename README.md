# cxxpython

A C++ Wrapper of the Python C Api 

## Getting Started 

This is a example of the [python docs](https://docs.python.org/3/extending/extending.html) example:
```cpp
#define PY_SSIZE_T_CLEAN
#include <CxxPython.h>
#include <cstdlib> // system function
#include <iostream>

static CxxPyError* error = new CxxPyError { "spam.error", nullptr, nullptr };

static CxxPyObject *
spam_system(CxxPyObject *self, CxxPyObject *args)
{
    std::string command;
    int sts;

    if (!args->parseTuple("s", &command))
        return nullptr;
    sts = system(command);
    if (sts < 0)
        return error->raise("System command failed"); 
    return new CxxPyObject { sts };
}



static CxxPyMethodDef SpamMethods[] = {
    {"system",  spam_system, CXX_METH_VARARGS, "Execute a shell command."},
    { nullptr }        /* Sentinel */
};

static struct CxxPyModuleDef spammodule = {
    CxxPyModuleDef::HEAD_INIT,
    "spam",   /* name of module */
    "The example of CxxPython", /* module documentation, may be NULL */
    -1,       /* size of per-interpreter state of the module,
                 or -1 if the module keeps state in global variables. */
    SpamMethods
};

CxxPyMODINIT_FUNC
CxxPyInit_spam(void)
{
    return new CxxPyModule { spammodule };
}

int
main(int argc, char *argv[])
{
    wchar_t *program = CxxPyUtil::decodeLocale(argv[0], NULL);
    if (program == nullptr) {
        std::cerr << "Fatal error: cannot decode argv[0]" << std::endl;
        exit(1);
    }

    CxxPython cxx_python { program };
  
    if (python.appendInitTab("spam", CxxPyInit_spam) == -1) {
        std::cerr << "Error: could not extend in-built modules table" << std::endl;
        exit(1);
    }

    /* Initialize the Python interpreter.  Required.
       If this step fails, it will be a fatal error. */
    cxx_python.init();

    /* Optionally import the module; alternatively,
       import can be deferred until the embedded script
       imports it. */
    CxxPyObject *pmodule = cxx_python.import("spam");
    if (!pmodule) {
        cxx_python.raise_error();
    }

    CxxPyMem::free(program);
    return 0;
}





