title: 使用Designer编写PyQt程序的简单流程
date: 2015-07-06 22:34:56
tags: PyQt
---

PyQt是一个非常方便的图形界面程序库，非常适合快速实现一个图形界面程序。Designer是Qt自带的一个界面设计工具，非常强大易用。

## 使用Designer的一般流程
1. 打开Designer，编辑一个界面文件，保存为ui后缀的文件。假设我们创建了一个Main Window界面，这个ui文件名为MyWindow.ui。
2. 使用pyuic4工具把ui文件转换成代码
	```
	pyuic4 -i 0 MyWindow.ui -o MyWindow.py
	```
	在Windows上，可以写一个bat批处理来方便生成。使用下面这种方式可以在一个bat中添加多个转换。
	```
	cmd /c "pyuic4 -i 0 MyWindow.ui -o MyWindow.py"
	```

3. 查看生成的MyWindow.py文件，发现里面有一个类Ui_MainWindow。运行下面的代码就可以实现界面的展示了。
	```python
	from MyWindow import Ui_MainWindow

	class MainWindow(QtGui.QMainWindow, Ui_MainWindow):

		def __init__(self, parent=None):
			super(MainWindow, self).__init__(parent)
			self.setupUi(self)

	if __name__ == "__main__":
		app = QtGui.QApplication(sys.argv)

		window = MainWindow()
		window.show()

		sys.exit(app.exec_())
	```
4. 接下来，按照需要扩展其他功能。