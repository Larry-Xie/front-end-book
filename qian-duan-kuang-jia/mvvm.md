# MVVM

## 一、引言

对于现在的前端框架，我们经常会听到 MVVM 这个名词。 MVC，MVP 和 MVVM 都是常见的软件架构模式（Architectural Pattern），它通过分离关注点来改进代码的组织方式。不同于设计模式（Design Pattern），只是为了解决一类问题而总结出的抽象方法，一种架构模式往往使用了多种设计模式。

MVVM 是在 MVC 的基础上衍生出来的，我们需要知道它们的概念和异同点。

MVC 针对于传统的依赖服务器的实现，而 MVVM 促进了前后端的分离。

## 二、MVC

### 1. What is MVC

The MVC framework is an architectural pattern that separates an applications into three main logical components Model, View, and Controller. Hence the abbreviation MVC. The full form MVC is Model View Controller.

In this architecture, a component is built to handle specific development aspects of an application. MVC separates the business logic and presentation layer from each other. This architectural pattern mainly used for desktop graphical user interfaces (GUIs).

### 2. MVC Pattern <a href="#3" id="3"></a>

![MVC Architecture](https://www.guru99.com/images/1/122118\_0445\_MVCTutorial1.png)

Three important MVC the components are:

* **Model:** It includes all the data and its related logic.
* **View:** Present data to the user or handles user interaction.
* **Controller:** An interface between Model and View components.

Let’s see each of this component in detail:

#### Model

The model component stores data and related logic. It represents data that is being transferred between controller components or any other related business logic.

For example, a Controller object helps you to retrieve the customer info from the database. It manipulates data and sends it back to the database or use it to render the same data.

#### View

A View is that part of the Application that represents the presentation of data. Views are created by the data gathered from the model data. A view requests the Model to give information so that it resents the output to the user.

The View also represents the data from charts, diagrams, and table. For example, any customer view will include all the UI components like text boxes, dropdowns, etc.

#### Controller

The Controller is that part of the Application that handles the user interaction. The Controller interprets the mouse and keyboard inputs from the user, informing the Model and the View to change as appropriate.

A Controller sends commands to the Model to update its state(E.g., Saving a specific document). The Controller also sends commands to its associated view to change the View’s presentation (For example, scrolling a particular document).

### 3. Features of MVC <a href="#5" id="5"></a>

Here are important features of MVC:

* Easy and frictionless testability. Highly testable, extensible and pluggable framework
* It also you to leverage existing features offered by ASP.NET, Django, JSP, etc.
* It offers full control over your HTML as well as your URLs.
* It supports Test Driven Development (TDD)
* This architecture offers separation of logic
* Allows routing for SEO Friendly URLs.
* Offers to map for comprehensible and searchable URLs.

### 4. Advantages of MVC <a href="#8" id="8"></a>

Here, are advantages/pros of MVC

* Easier support for a new type of clients
* The development of the various components can be performed parallelly.
* It avoids complexity by dividing an application into separate (MVC) units
* It only uses a front controller pattern that processes web application requests using a single controller.
* Offers the best support for test-driven development
* It works well for Web apps, which are supported by large teams of web designers and developers.
* It provides a clean separation of concerns(SoC).
* All classed and objects are independent of each other so that you can test them separately.
* MVC allows logical grouping of related actions on a controller together.

### 5. Disadvantages of MVC <a href="#10" id="10"></a>

Here, are cons/drawback of MVC

* Business logic is mixed with Ul
* Hard to reuse and implement tests
* No formal validation support
* Increased complexity and Inefficiency of data
* The difficulty of using MVC with the modern user interface
* There is a need for multiple programmers to conduct parallel programming.
* Knowledge of multiple technologies is required.

## 三、MVVM

### 1. What is MVVM <a href="#2" id="2"></a>

MVVM architecture facilitates a separation of development of the graphical user interface with the help of mark-up language or GUI code. The full form of MVVM is Model–View–ViewModel.

The view model of MVVM is a value converter that means that it is view model’s responsibility for exposing the data objects from the Model in such a way that objects are easily managed and presented.

### 2. MVVM Pattern <a href="#4" id="4"></a>

Here, is a pattern for MVVM:

![MVVM Architecture](https://www.guru99.com/images/2/041720\_1054\_MVCvsMVVMKe2.png)

MVVM architecture offers two-way data binding between view and view-model. It also helps you to automate the propagation of modifications inside View-Model to the view. The view-model makes use of observer pattern to make changes in the view-model.

Let’s see each other this component in detail:

#### Model

The model stores data and related logic. It represents data that is being transferred between controller components or any other related business logic.

For example, a Controller object will retrieve the student info from the school database. It manipulates data and sends it back to the database or use it to render the same data.

#### View:

The View stands for UI components like HTML, CSS, jQuery, etc. In MVVC pattern view is held responsible for displaying the data which is received from the Controller as an outcome. This View is also transformed Model (s) into the User Interface (UI).

#### View Model:

The view model is responsible for presenting functions, commands, methods, to support the state of the View. It is also accountable to operate the model and activate the events in the View.

### 3. Features of MVVM <a href="#6" id="6"></a>

Here, are features of MVVM architecture:

* MVVM is written for desktop application with data binding capabilities – XAML and the INotifyPropertyChanged interface
* If you want to do modification in the View-Model, the View-Model uses an observer pattern.
* The MVVM pattern is mostly used by [WPF](https://www.guru99.com/sap-bods-tutorial.html), Silverlight, nRoute, etc.

### 4. Advantages of MVVM <a href="#9" id="9"></a>

Here, are pros/benefits of MVVM

* Business logic is decoupled from Ul
* Easy to maintain and test
* Easy to reuse components
* Loosely coupled architecture: MVVM makes your application architecture as loosely coupled.
* You can write unit test cases for both the viewmodel and Model layer without the need to reference the View’.

### 5. Disadvantages of MVVM <a href="#11" id="11"></a>

Here, are cons/drawback of MVVM

* Maintenance of lots of codes in controller
* Some people think that for simple UIs of MVVM architecture can be overkill.
* Not offers tight coupling between the view and view model

## 四、MVC vs MVVM

### 1. Key Difference

* MVC framework is an architectural pattern that separates an applications into three main logical components Model, View, and Controller. On the other hand MVVM facilitates a separation of development of the graphical user interface with the help of mark-up language or GUI code
* In MVC, controller is the entry point to the Application, while in MVVM, the view is the entry point to the Application.
* MVC Model component can be tested separately from the user, while MVVM is easy for separate unit testing, and code is event-driven.
* MVC architecture has “one to many” relationships between Controller & View while in MVVC architecture, “one to many” relationships between View & View Model.

### 2. Comparison <a href="#7" id="7"></a>

Here, are the important difference between MVVM and MVC

![](https://www.guru99.com/images/2/041720\_1054\_MVCvsMVVMKe3.png)

| MVC                                                           | MVVM                                                                          |
| ------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Controller is the entry point to the Application.             | The view is the entry point to the Application.                               |
| One to many relationships between Controller & View.          | One to many relationships between View & View Model.                          |
| View Does not have reference to the Controller                | View have references to the View-Model.                                       |
| MVC is Old Model                                              | MVVM is a relatively New Model.                                               |
| Difficult to read, change, to unit test, and reuse this Model | The debugging process will be complicated when we have complex data bindings. |
| MVC Model component can be tested separately from the user    | Easy for separate unit testing and code is event-driven.                      |

## 五、参考 <a href="#8" id="8"></a>

* [MVC vs MVVM: Key Differences with Examples](https://www.guru99.com/mvc-vs-mvvm.html)
* [浅析前端开发中的 MVC/MVP/MVVM 模式](https://juejin.cn/post/6844903480126078989)
* [耽误你的十分钟，让MVVM原理还给你](https://juejin.cn/post/6844903586103558158)
