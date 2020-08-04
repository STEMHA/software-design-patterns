抽象工厂模式
本课程详细介绍了另一种创新设计模式的工作，该模式允许我们以灵活和可扩展的方式创建产品系列。

它是什么 ？
在上一课中，我们学习了工厂方法模式。我们看到了如何使用工厂方法为F-16的变体建模。但是除了F-16，还有许多我们需要代表的飞机。假设客户购买了一架Boeing-747供首席执行官旅行，现在希望您的软件为这种新型飞机提供支持。

抽象工厂模式解决了创建相关产品系列的问题。例如，F-16需要引擎，驾驶舱和机翼。波音747需要相同的零件，但它们是波音特有的。任何飞机都需要这三个相关的零件，但这些零件将是飞机和特定于供应商的。您能在这里看到一种模式吗？我们需要一个框架来为每架飞机创建相关零件，F-16的零件族，波音747的零件族等等。

形式上，抽象工厂模式定义为定义一个接口，以创建相关或相关对象的族，而无需指定其具体类。

类图
类图由以下实体组成

抽象工厂
混凝土厂
抽象产品
混凝土制品
客户
小部件
类图

例
抽象工厂可以被视为超工厂或工厂工厂。该模式实现了产品系列的创建，而没有向客户透露具体的类。让我们考虑一个例子。假设您正在为航空业创建一个仿真软件，并且需要将不同的飞机表示为对象。但是在表示飞机之前，您还需要将飞机的不同部分表示为对象。现在，让我们选择三个：驾驶舱，机翼和发动机。现在说您要代表的第一架飞机是强大的F-16。您可能会为F-16的每个部件编写三个类别。在您的代码中，您可能会消耗刚刚创建的三个类，如下所示：

    public void main() {
        F16Cockpit f16Cockpit = new F16Cockpit();
        F16Engine f16Engine = new F16Engine();
        F16Wings f16Wings = new F16Wings();
    
        List<F16Engine> engines = new ArrayList<>();
        engines.add(f16Engine);
        for (F16Engine engine : engines) {
            engine.start();
        }
    }
如果您的模拟软件起飞，并且您需要将其扩展到其他飞机，那么看起来天真无邪的代码片段可能会引起严重的头痛。以下是上述代码片段存在问题的列表：

这三个部分的具体类别已直接向消费​​者公开。

F-16有几个具有不同引擎的变体，并且说将来如果您想使匹配的引擎对象返回变体，您将需要子F16Engine类化，这也将需要更改使用者代码段。

代码片段中的“列表”是用具体的类参数设置的，将来，如果您添加另一个飞机引擎对象，那么即使所有飞机的引擎有些相似，也无法将新引擎添加到列表中。

我们将一一解决这些问题，然后看看抽象工厂模式将如何出现。

编码到接口而不是实现
良好的面向对象设计的基本原则之一是隐藏具体的类并向客户端公开接口。对象响应一组请求，这些请求可以通过对象类实现的接口捕获。客户应该知道对象响应什么请求，而不是实现。

在我们的示例中，我们可以创建一个接口IEngine，该接口公开method start()。F16Engine类将如下更改：

public interface IEngine {
 
    void start();
}
 
public class F16Engine implements IEngine {
 
    @Override
    public void start() {
        System.out.println("F16 engine on");
    }
}
通过以上更改，请查看相应的使用者代码如何更改

    public void main() {
        IEngine f16Engine = new F16Engine();
        List<IEngine> engines = new ArrayList<>();
        engines.add(f16Engine);
        for (IEngine engine : engines) {
            engine.start();
        }
    }
突然，使用者代码没有任何类实现F-16引擎并与接口一起工作的实现细节。但是，我们仍然想隐藏new F16Engine()部分代码。我们不想让消费者知道我们要实例化什么类。接下来讨论。

建立工厂
代替在客户端代码中新建对象，我们将使用一个类来负责制造所请求的对象并将其返回给客户端。我们将之称为“此类”，F16Factory因为它可以创建F16飞机的各个部分并将其交付给提出要求的客户。该类将具有以下形状。

public class F16Factory {
 
    public IEngine createEngine() {
        return new F16Engine();
    }
 
    public IWings createWings() {
        return new F16Wings();
    }
 
    public ICockpit createCockpit() {
        return new F16Cockpit();
    }
}
假设我们将F16Factory构造函数中的对象传递给客户端代码，并且现在它将像这样创建对象：

    public void main(F16Factory f16Factory) {
        IEngine f16Engine = f16Factory.createEngine();
        List<IEngine> engines = new ArrayList<>();
        engines.add(f16Engine);
        for (IEngine engine : engines) {
            engine.start();
        }
    }
请注意，此设置如何使我们能够自由更改代表F16Engine的具体类，只要它提交到IEngine接口即可。我们可以重命名，增强或修改我们的类，而不会引起客户端的重大更改。还要注意，通过仅改变传递给客户端构造函数的工厂类，我们就可以为客户端提供全新飞机的相同零件。接下来讨论。

工厂工厂
如果我们可以将相同的客户端代码片段用于其他飞机，例如波音747或俄罗斯的MiG-29，那不是很好吗？如果我们可以让所有传递给客户的工厂都同意实施该createEngine()方法，那么客户代码将继续适用于各种飞机工厂。但是所有工厂必须承诺一个将要实现其方法的通用接口，这个通用接口将是抽象工厂。

实作
让我们从一个接口开始，该接口定义不同飞机工厂需要实现的方法。客户端代码是针对抽象工厂编写的，但在运行时由具体工厂组成。

public interface IAircraftFactory {
 
    IEngine createEngine();
 
    IWings createWings();
 
    ICockpit createCockpit();
}
注意，当指代“接口”时，我们指的是Java抽象类或Java接口。在这种情况下，如果任何产品都有默认实现，我们本可以使用抽象类。create方法不会返回具体的产品，而是将工厂消费者与零件的具体实现分离的接口。

抽象工厂模式的正式定义是，抽象工厂模式定义了一个用于创建相关产品系列的接口，而无需指定具体类。这里IAircraftFactory是形式化定义中的接口，并注意其创建方法如何不返回具体部分，而是由具体部分的类实现的接口。

接下来，让我们定义两架飞机的工厂。

public class F16Factory implements IAircraftFactory {
 
    @Override
    public IEngine createEngine() {
        return new F16Engine();
    }
 
    @Override
    public IWings createWings() {
        return new F16Wings();
    }
 
    @Override
    public ICockpit createCockpit() {
        return new F16Cockpit();
    }
}
 
public class Boeing747Factory implements IAircraftFactory {
 
    @Override
    public IEngine createEngine() {
        return new Boeing747Engine();
    }
 
    @Override
    public IWings createWings() {
        return new Boeing747Wings();
    }
 
    @Override
    public ICockpit createCockpit() {
        return new Boeing747Cockpit();
    }
}
混凝土工厂将负责制造F-16或波音专用发动机，座舱和机翼。为简洁起见，每个部分都有一个对应的产品界面，我们不会列出。代表零件的接口是：

IEngine

ICockpit

IWings

实际上，所有创建方法都是已被覆盖的工厂方法。实际上，在实现抽象工厂模式时会使用工厂方法模式。为了简洁起见，我们跳过了列出发动机，机翼和驾驶舱的具体类别。

在上一课中，我们为F-16创建了一个类，其中包含一个public方法fly()。该方法在内部调用该makeF16()方法，并且在飞机制造之后，taxi()在打印fly语句之前调用了该方法。在我们的方案中，所有飞机都应遵循相同的模式。他们首先制造出来，然后在跑道上滑行，然后飞走。因此，我们可以为执行这三个任务的飞机创建一个类。注意，我们如何不创建代表两个飞机的单独类别，即F-16和波音747，而是一个Aircraft可以同时代表两个类别的单一类别。

// Incomplete skeleton of the class.
public class Aircraft {
 
    IEngine engine;
    ICockpit cockpit;
    IWings wings;
 
    protected Aircraft makeAircraft() {
        //TODO: provide implementation
    }
 
    private void taxi() {
        System.out.println("Taxing on runway");
    }
 
    public void fly() {
        Aircraft aircraft = makeAircraft();
        aircraft.taxi();
        System.out.println("Flying");
    }
}
现在，我们将使该makeAircraft方法为空。首先让我们看看客户如何请求F-16和Boeing-747对象。

public class Client {
 
    public void main() {
 
        // Instantiate a concrete factory for F-16
        F16Factory f16Factory = new F16Factory();
        
        // Instantiate a concrete factory for Boeing-747
        Boeing747Factory boeing747Factory = new Boeing747Factory();
        
        // Lets create a list of all our airplanes
        Collection<Aircraft> myPlanes = new ArrayList<>();
        
        // Create a new F-16 by passing in the f16 factory
        myPlanes.add(new Aircraft(f16Factory));
 
        // Create a new Boeing-747 by passing in the boeing factory
        myPlanes.add(new Aircraft(boeing747Factory));
 
        // Fly all your planes
        for (Aircraft aircraft : myPlanes) {
            aircraft.fly();
        }
 
    }
}
我们需要在类中添加一个构造函数Aircraft，该构造函数将存储传入的工厂对象并使用工厂创建飞机零件。只需将飞机对象与其他工厂组成，我们就能得到另一架飞机。飞机级的完整版本如下所示：

public class Aircraft {
 
    IEngine engine;
    ICockpit cockpit;
    IWings wings;
    IAircraftFactory factory;
 
    public Aircraft(IAircraftFactory factory) {
        this.factory = factory;
    }
 
    protected Aircraft makeAircraft() {
        engine = factory.createEngine();
        cockpit = factory.createCockpit();
        wings = factory.createWings();
        return this;
    }
 
    private void taxi() {
        System.out.println("Taxing on runway");
    }
 
    public void fly() {
        Aircraft aircraft = makeAircraft();
        aircraft.taxi();
        System.out.println("Flying");
    }
}
客户只需要实例化正确的工厂并将其传递。工厂的消费者或客户就是这个Aircraft类。我们可以创建一个接口IAircraft来表示该班级Aircraft依次将要实现的所有飞机，但是对于我们的有限示例，则没有必要。

生成的代码易于扩展和灵活。
为了将我们当前的示例与工厂方法模式课程中讨论的示例联系在一起，我们可以选择将其F16Factory进一步子类化以为F-16 的A和B变体创建工厂。我们还可以对现有F16Factory工厂进行参数化，以使用一个枚举来指定变量模型，并相应地在switch语句中返回正确的部分。

其他例子
抽象工厂对于在不同操作系统上工作的框架和工具包特别有用。例如，如果您的库为UI提供了精美的小部件，那么您可能需要在MacOS上工作的一系列产品以及在Windows上工作的类似产品。同样，IDE中使用的主题可以是另一个示例。如果您的IDE支持浅色和深色主题，则可以通过更改创建小部件的具体工厂，使用抽象工厂模式来创建属于浅色或深色主题的小部件。

javax.xml.parsers.DocumentBuilderFactory.newInstance() 将退还您的工厂。

javax.xml.transform.TransformerFactory.newInstance() 将退还您的工厂。

注意事项
天真的读者可能会发现工厂方法模式和抽象工厂模式是相似的。两者之间的区别在于动机。工厂方法模式通常负责创建单个产品，而抽象工厂模式则创建整个相关产品系列。此外，在工厂方法模式中，我们使用继承来创建更专业的产品，而在抽象工厂模式中，我们通过传入消耗来创建所需产品的工厂来实践对象组合。

在我们的飞机示例中，我们可以简单地通过为其创建一个具体工厂来添加新飞机。但是，请注意，如果将直升机添加到机队中，并且需要飞机没有的零件，那么我们需要IAircraftFactory使用仅适用于直升机的零件的另一种创建方法来扩展接口。由于新组件不是飞机的一部分，这将把更改层叠到需要返回null的现有工厂。

最好将混凝土工厂表示为单例对象
