# OpenGL简介



OpenGL到底是什么。         

一般它被认为是一个API(Application Programming Interface, 应用程序编程接口)，包含了一系列可以操作图形、图像的函数。     
然而，**OpenGL本身并不是一个API，它仅仅是一个由Khronos组织制定并维护的规范(Specification)。**


OpenGL规范严格规定了每个函数该如何执行，以及它们的输出值。      
至于内部具体每个函数是如何实现(Implement)的，将由OpenGL库的开发者自行决定。          

因为OpenGL规范并没有规定实现的细节，具体的OpenGL库允许使用不同的实现，只要其功能和结果与规范相匹配（亦即，作为用户不会感受到功能上的差异）。

实际的OpenGL库的开发者通常是显卡的生产商。你购买的显卡所支持的OpenGL版本都为这个系列的显卡专门开发的。           
当你使用Apple系统的时候，OpenGL库是由Apple自身维护的。在Linux下，有显卡生产商提供的OpenGL库，也有一些爱好者改编的版本。这也意味着任何时候OpenGL库表现的行为与规范规定的不一致时，基本都是库的开发者留下的bug。

### 立即渲染模式(Immediate mode) && 核心模式(Core-profile)
早期的OpenGL使用立即渲染模式（Immediate mode，也就是固定渲染管线），这个模式下绘制图形很方便。OpenGL的大多数功能都被库隐藏起来，开发者很少有控制OpenGL如何进行计算的自由。而开发者迫切希望能有更多的灵活性。随着时间推移，规范越来越灵活，开发者对绘图细节有了更多的掌控。立即渲染模式确实容易使用和理解，但是效率太低。因此从OpenGL3.2开始，规范文档开始废弃立即渲染模式，并鼓励开发者在OpenGL的核心模式(Core-profile)下进行开发，这个分支的规范完全移除了旧的特性。

当使用OpenGL的核心模式时，OpenGL迫使我们使用现代的函数。当我们试图使用一个已废弃的函数时，OpenGL会抛出一个错误并终止绘图。现代函数的优势是更高的灵活性和效率，然而也更难于学习。立即渲染模式从OpenGL实际运作中抽象掉了很多细节，因此它在易于学习的同时，也很难让人去把握OpenGL具体是如何运作的。现代函数要求使用者真正理解OpenGL和图形编程，它有一些难度，然而提供了更多的灵活性，更高的效率，更重要的是可以更深入的理解图形编程。

这也是为什么我们的教程面向OpenGL3.3的核心模式。虽然上手更困难，但这份努力是值得的。

现今，更高版本的OpenGL已经发布（写作时最新版本为4.5），你可能会问：既然OpenGL 4.5 都出来了，为什么我们还要学习OpenGL 3.3？答案很简单，所有OpenGL的更高的版本都是在3.3的基础上，引入了额外的功能，并没有改动核心架构。新版本只是引入了一些更有效率或更有用的方式去完成同样的功能。因此，所有的概念和技术在现代OpenGL版本里都保持一致。当你的经验足够，你可以轻松使用来自更高版本OpenGL的新特性。



### 依赖库

简要来说，GLAD、GLEW都是一个图形库，可以理解为是在显卡驱动上给渲染用户一个统一的API；   
而GLUT、FreeGLUT、GLFW这三个是用于图形开发的辅助工具库，主要用于创建和管理OpenGL环境、操作窗口等。

OpenGL只是一个标准/规范，具体的实现是由驱动开发商针对特定显卡实现的。但是在你真正能够在程序中使用OpenGL之前，你需要对他进行初始化，但是由于OpenGL是跨平台的，所以也没有一个标准的方式进行初始化。OpenGL初始化分为两个阶段：

- 第一个阶段，你需要创建一个OpenGL上下文环境，这个上下文环境存储了所有与OpenGL相关的状态（OpenGL是一个状态机），上下文位于操作系统中某个进程中，一个进程可以创建多个上下文，每一个上下文都可以描绘一个不同的可视界面，就像应用程序中的窗口；简单来理解就是为了创建一个窗口；而GLUT、FreeGLUT、GLFW库就是用于干这件事的。

- 第二个阶段，你需要定位所有需要在OpenGL中使用的函数(由于OpenGL驱动版本众多，它大多数函数的位置都无法在编译时确定下来，需要在运行时查询。任务就落在了开发者身上，开发者需要在运行时获取函数地址并将其保存在一个函数指针中供以后使用)，而GLEW、GLAD就是干这件事的。



### GLFW

GLFW是一个专门针对OpenGL的C语言库，它提供了一些渲染物体所需的最低限度的接口。它允许用户创建OpenGL上下文，定义窗口参数以及处理用户输入。       
简单来说，GLFW负责创建窗口，处理窗口相关的事件（如键盘和鼠标输入），并提供一个OpenGL上下文供你的程序使用。
这些库节省了我们书写操作系统相关代码的时间，提供给我们一个窗口和一个OpenGL上下文用来渲染。       



### GLAD

然后，我们有GLAD。由于OpenGL驱动版本众多，它大多数函数的位置都无法在编译时确定下来，需要在运行时查询。所以任务就落在了开发者身上，开发者需要在运行时获取函数地址并将其保存在一个函数指针中供以后使用。取得地址的方法因平台而异,代码非常复杂，而且很繁琐，我们需要对每个可能使用的函数都要重复这个过程。幸运的是，有些库能简化此过程，其中GLAD是目前最新，也是最流行的库。GLAD是用来管理OpenGL的函数指针的，所以在调用任何OpenGL的函数之前我们需要初始化GLAD。GLAD也可以使OpenGL基础渲染变得简单。


到了这些感觉不想学了，又是GLFW、又是GLAD，每个都要看，每个都要学，太麻烦了。      

这里我们可以使用Qt进行开发，Qt里面对OpenGL做了封装实现，我们不用再借助GLFW、GLAD，直接使用Qt进行配置开发就可以了。   

## Qt开发OpenGL

QOpenGLWidget是一个渲染OpenGL图形的窗口部件。 QOpenGLWidget提供的功能是把OpenGL 图形显示功能整合到Qt 应用程序当中。它非常容易使用：把我们的子类继承QOpenGLWidget，然后就可以像使用其它QWidget那样使用

QGLWidget与QOpenGLWidget的区别:

1、QGLWidget中是直接调用opengl指令，个人觉得比较方便，逻辑清晰，可以了解opengl底层的接口；

2、QOpenGLWidget是调用QOpenGLFunctions中封装接口，从Qt5.4开始，Qt推荐使用QOpenGLWidget。



### QOpenGLWidget
有了他就不需要用GLFW

QOpenGLWidget提供了三个便捷的虚函数，可以重载，用来重新实现典型的OpenGL任务:     
 
- paintGL: 渲染OpenGL场景。widget需要更新时调用。
- resizeGL: 设置OpenGL视口、投影等。widget调整大小(或首次显示)时调用。
- initializeGL: 设置OpenGL资源和状态。第一次调用resizeGL()/paintGL()之前调用一次。   

如果想在其它地方触发paintGL() 进行重绘，不直接调用paintGL() 函数。可以使用update()函数。QWidget::update();

当调用 initializeGL()，paintGL()，paintGL() ，窗口中OpenGL渲染的上下文使用的是当前的。如果需要在其它地方调用标注的OpenGL API的函数(例如：在我们自己窗口的构造函数或者在我们自己绘制函数中)，我们需要先调用makeCurrent();



### QOpenGLFunctions_X_X_Core

QOpenGLFunctions_X_X_Core提供OpenGL X.X版本核心模式的所有功能，是对OpenGL函数的封装。
有了它就不需要用GLAD

其中的initializeOpenGLFunctions用于初始化OpenGL函数，将Qt里的函数指针指向显卡的函数，之后调用的OpenGL函数才是可用的。



后面我们都使用3.3版本进行开发。

### 开始

1. 使用qt新建一个项目，并在ui文件中增加OpenGL Widget， ![image](https://github.com/CharonChui/Pictures/blob/master/openglwidget_1.png?raw=true)
2. 新建MyOpenGlWidget类继承QOpenGLWidget和QOpenGLFunctions_3_3_Core。

```C
#ifndef MYOPENGLWIDGET_H
#define MYOPENGLWIDGET_H

#include <QWidget>
#include <QOpenGLWidget>
#include <QOpenGLFunctions_3_3_Core>

class MyOpenGlWidget : public QOpenGLWidget, public QOpenGLFunctions_3_3_Core
{
    Q_OBJECT
public:
    explicit MyOpenGlWidget(QWidget *parent = nullptr);

    // 实现这三个函数
    virtual void initializeGL();
    virtual void resizeGL(int w, int h);
    virtual void paintGL();
signals:

};

#endif // MYOPENGLWIDGET_H

```

![image](https://github.com/CharonChui/Pictures/blob/master/qopenglwidget.png?raw=true)
![image](https://github.com/CharonChui/Pictures/blob/master/qopenglfunctions.png?raw=true)

所以需要打开项目的.pro文件增加对opengl和openglwidgets的支持:  
```C
QT       += openglwidgets
QT       += opengl
```

```C
#include "myopenglwidget.h"

unsigned int VAO, VBO;

float vertices[] = {
    -0.5f, -0.5f, 0.0f,
    0.5f, -0.5f, 0.0f,
    0.0f, 0.5f, 0.0f,
};

MyOpenGlWidget::MyOpenGlWidget(QWidget *parent)
    : QOpenGLWidget{parent}
{
}

void MyOpenGlWidget::initializeGL()
{
    // 必须先初始化
    bool success = initializeOpenGLFunctions();
    qDebug() << success;
}

void MyOpenGlWidget::resizeGL(int w, int h)
{

}

void MyOpenGlWidget::paintGL()
{
    glClearColor(1.0f, 0.0f, 0.0f, 1.0f);
    glClear(GL_COLOR_BUFFER_BIT);
}
```

然后将ui文件中的OpenGL Widget提升为我们自定义的MyOpenGlWidget.

![image](https://github.com/CharonChui/Pictures/blob/master/opengl_widget_prove.png?raw=true)

好了，执行一下，发现报错了。 
【注意】:在一些平台(例如macOS)上，当请求OpenGL核心配置(即上下文)时，在构造QApplication实例之前必须的调用QSurfaceFormat::setDefaultFormat()，。这是为了确保在使用正确的版本和配置文件创建所有内部上下文时，上下文之间的资源共享保持正常工作。


配置格式：要获取与给定OpenGL版本或配置文件兼容的上下文，或请求深度和模具缓冲区

全局地设置请求的格式，以便在应用程序的生命周期中应用于所有窗口和上下文，这样更简单、更健壮。

main.cpp
```C
#include "mainwindow.h"

#include <QApplication>
#include <QSurfaceFormat>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);

    QSurfaceFormat format;
    format.setDepthBufferSize(24);
    format.setStencilBufferSize(8);
    format.setVersion(3, 3);
    format.setProfile(QSurfaceFormat::CoreProfile);
    QSurfaceFormat::setDefaultFormat(format);


    MainWindow w;
    w.show();
    return a.exec();
}

```

![image](https://github.com/CharonChui/Pictures/blob/master/qt_opengl_run.png?raw=true)



