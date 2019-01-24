[HOME](README.md)

# Table of contents
* [wild char](#wild-char-transform)

# wild char transform
```
const size_t cSize = strlen(prog);
std::wstring wc(cSize, L'#');
mbstowcs(&wc[0], prog, cSize);
Py_SetProgramName(&wc[0]);
```
