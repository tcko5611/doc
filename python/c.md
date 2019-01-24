[HOME](README.md)
# Table of Contents
* [pdb basic usage](#pdb-basic-usage)
* [Debug with pdb module](#Debug-with-pdb-module)
* [Debug with idle](#Debug-with-idle)

# pyhton 2
* b : set break point, filename:lineno | function | classname.methodname
* diable : disable break point, bpnumber
* cl : clear break point, bpnumber 
* d : down stack
* u : up stack
* s : step in
* n : next
* c : continue
* unt : until to next line
* r : return
* p : print expression


# python 3
* using the following option to turn on pdb
```
#include <Python.h>
#include "cckC.h"

struct module_state {
  PyObject *error;
};

#define GETSTATE(m) ((struct module_state*)PyModule_GetState(m))

static PyObject *
dspfpy_rpt(PyObject *self, PyObject *args)
{
  char *dspfNm, * subNm, *outNm;
  double rmin, rmax;
  char *scope, *rmscope;
  int num, num_max_reff,sort;
  char *skip;
  int mt;
  
  if (!PyArg_ParseTuple(args, "sssddssiiisi", &dspfNm, &subNm, &outNm, &rmin, 
			&rmax, &scope, &rmscope, &num, &num_max_reff, &sort,
			&skip,&mt))
    return NULL;
  int sts = rptDspfResWrapper(dspfNm, subNm, outNm, rmin, rmax, scope, rmscope, 
			      num, num_max_reff, sort, skip, mt);
  if (sts < 0) {
    return NULL;
  }
  return PyLong_FromLong(sts);
}

static PyMethodDef dspfpy_methods[] = {
  { "rptDspfRes",  (PyCFunction)dspfpy_rpt, METH_VARARGS,
    "Execute rpt dspf res."},
  {NULL, NULL, 0, NULL}        /* Sentinel */
};

static int dspfpy_traverse(PyObject *m, visitproc visit, void *arg) {
  Py_VISIT(GETSTATE(m)->error);
  return 0;
}

static int dspfpy_clear(PyObject *m) {
  Py_CLEAR(GETSTATE(m)->error);
  return 0;
}


static struct PyModuleDef moduledef = {
  PyModuleDef_HEAD_INIT,
  "dspfpy",
  NULL,
  sizeof(struct module_state),
  dspfpy_methods,
  NULL,
  dspfpy_traverse,
  dspfpy_clear,
  NULL
};

#define INITERROR return NULL

PyMODINIT_FUNC
PyInit_dspfpy(void)
{
  PyObject *m = PyModule_Create(&moduledef);
  if (m == NULL)
    INITERROR;
  struct module_state *st = GETSTATE(m);
  
  st->error = PyErr_NewException("dspfpy.error", NULL, NULL);
  if (st->error == NULL) {
    Py_INCREF(m);
    INITERROR;
  }
  return m;
}
```
