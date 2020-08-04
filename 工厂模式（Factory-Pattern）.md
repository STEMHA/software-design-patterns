工厂方法模式
本课讨论如何赋予派生类创建适当对象的责任。

它是什么 ？
工厂生产商品，软件工厂生产对象。通常，用Java创建对象的过程如下：

SomeClass  someClassObject  = new SomeClass （）;  
上述方法的问题在于，使用SomeClass的对象的代码现在突然变得依赖于的具体实现SomeClass。使用new创建对象没有什么错，但是它伴随着将我们的代码紧密耦合到具体的实现类的负担，这是违反接口代码而不是实现的代码。

形式上，工厂方法定义为提供一个用于创建对象的接口，但将对象的实际实例委托给子类。

类图
类图由以下实体组成

产品
混凝土制品
创作者
具体造物主
小部件
类图

例
继续我们的飞机示例场景，让我们假设我们正在尝试为F-16战斗机建模。客户代码需要为喷气式战斗机构造发动机对象并进行飞行。该类的幼稚实现将如下所示：

 
public class F16 {
 
    F16Engine engine;
    F16Cockpit cockpit;
 
    protected void makeF16() {
        engine = new F16Engine();
        cockpit = new F16Cockpit();
    }
 
    public void fly() {
        makeF16();
        System.out.println("F16 with bad design flying");
    }
}
 
public class Client {
 
    public void main() {
 
        // We instantiate from a concrete class, thus tying
        // ourselves to it
        F16 f16 = new F16();
        f16.fly();
    }
}
在上面的代码中，我们致力于使用F16该类的具体实现。如果公司提供飞机的较新版本并且我们需要在计划中代表它们怎么办？这将使我们在新建 F16实例的地方更改客户端代码。一种解决方法是，将对象的创建封装在另一个对象中，该对象仅负责对F-16的请求变体进行更新。首先，假设我们要表示F16 的A和B变体，那么代码如下所示：

public class F16SimpleFactory {
 
    public F16 makeF16(String variant) {
 
        switch (variant) {
        case "A":
            return new F16A();
        case "B":
            return new F16B();
        default:
            return new F16();
        }
    }
}
上面是简单工厂的示例，它实际上不是模式，而是常见的编程习惯。您也可以将make方法标记为静态，以跳过工厂对象创建步骤。但是，由于静态方法不能在子类中覆盖，因为它们是类的唯一方法，因此我们将不能对Static Factory进行子类化。请记住，简单工厂和静态工厂与工厂方法模式不同。

但是，如果我们希望将F16对象部分的创建保持在同一类中，并且仍然能够引入新的F16变体，则可以对F16进行子类化，并将正确的F16变体对象的创建委托给子类处理。该变体。这正是工厂方法模式！这里的方法就是使makeF16()我们像生产合适的F16变体的工厂那样工作。继续，我们介绍两个像这样的子类

public class F16 {
 
    IEngine engine;
    ICockpit cockpit;
 
    protected F16 makeF16() {
        engine = new F16Engine();
        cockpit = new F16Cockpit();
        return this;
    }
 
    public void taxi() {
        System.out.println("F16 is taxing on the runway !");
    }
 
    public void fly() {
        // Note here carefully, the superclass F16 doesn't know
        // what type of F-16 variant it was returned.
        F16 f16 = makeF16();
        f16.taxi();
        System.out.println("F16 is in the air !");
    }
}
 
public class F16A extends F16 {
 
    @Override
    public F16 makeF16() {
        super.makeF16();
        engine = new F16AEngine();
        return this;
    }
}
 
public class F16B extends F16 {
 
    @Override
    public F16 makeF16() {
        super.makeF16();
        engine = new F16BEngine();
        return this;
    }
}
我们使用继承来继承和专门化引擎对象。工厂方法可能提供也可能不提供默认或通用实现，但是通过覆盖create / make方法，可以让子类专门化或修改产品。在我们的示例中，变体模型仅具有不同的发动机，但座舱相同。客户端代码现在可以使用更新的模型，如下所示：

public class Client {
    public void main() {
        Collection<F16> myAirForce = new ArrayList<F16>();
        F16 f16A = new F16A();
        F16 f16B = new F16B();
        myAirForce.add(f16A);
        myAirForce.add(f16B);
 
        for (F16 f16 : myAirForce) {
            f16.fly();
        }
    }
}
请注意，工厂方法模式返回一个抽象类型，可以是Java接口或Java抽象类。在我们的情况下，超类F16不知道它是从makeF16()方法返回的F16的哪个变体。一般设置是超类具有除创建方法之外的所有方法的实现。create方法可以是抽象的，也可以带有默认实现，然后由超类的其他方法调用。正确的对象的创建是子类的责任。

与简单/静态工厂的差异
工厂方法模式似乎与简单工厂或静态工厂非常相似，但是主要区别在于，简单工厂无法像工厂方法模式那样通过继承来生产各种产品。

其他例子
工厂方法模式遍布工具箱和框架。只要类不提前知道需要实例化哪些子类对象，就可以使用该模式。这是设计框架时的常见问题，在该框架中，框架的使用者应扩展框架，以提供抽象类和挂钩功能或对象创建。

Java API公开了几种工厂方法：

java.util.Calendar.getInstance()
java.util.ResourceBundle.getBundle()
java.text.NumberFormat.getInstance()
注意事项
该模式可能会导致太多的子类，并且差别很小。
如果子类扩展了功能，则超类不能使用它，除非将其下转换为具体类型。向下转换可能在运行时失败。
