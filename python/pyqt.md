[HOME](README.md)

# PyQt 
* direct include mainwinodw.ui
```
base, form = uic.loadUiType('mainwindow.ui')
class MainWindow(base, form):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
	self.setupUi(self)
```
* using pyuic5 and include Ui_mainwindow.py
  * in Makefile
  ```
  pyuic5 mainwindow.ui -o Ui_mainwindow.py
  ```
  * in py file
  ```
  from Ui_mainwindow import Ui_MainWindow
  class MainWindow(QMainWindow, Ui_MainWindow):
  def __init__(self, parent = None, jsonFile = None):
    super(MainWindow, self).__init__(parent)
    self.setupUi(self)
  ```
