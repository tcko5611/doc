[HOME](../README.md)

# Table of Contents
  
# Basic usage
* generate a config file
  ```
  $ doxygen -g Doxygen
  ```
* change the Dosygen to generate all(include functions)
  ```
  EXTRACT_ALL            = NO (to YES)
  ```
* generate document
  ```
  $ doxygen Doxygen
  $ cd latex
  $ make
  $ gvfs-open refmax.pdf
  $ cd ../html
  $ firefox index.html
  ```
