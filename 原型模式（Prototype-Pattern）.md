原型模式
本课讨论如何使用原型模式从现有对象创建新对象。

它是什么 ？
原型模式涉及通过复制现有对象来创建新对象。制作副本的对象称为原型。您可以将原型对象视为创建其他对象的种子对象，但是您可能会问为什么我们要创建对象副本，为什么不只是重新创建它们呢？原型对象的动机如下：

有时创建新对象比复制现有对象要贵。

想象一下，一个类仅在运行时加载，而您不能静态访问其构造函数。运行时环境会自动为每个动态加载的类创建一个实例，并将其注册到原型管理器中。该应用程序可以从原型管理器请求对象，该对象又可以返回原型的克隆。

通过改变原型实例中克隆对象的值，可以大大减少系统中的类数。

形式上，模式被定义为使用原型实例作为模型来指定要创建的对象的类型，并制作原型的副本以创建新的对象。

类图
类图由以下实体组成
- Prototype
- Concrete Prototype
- Client


例
让我们举个例子来更好地理解原型模式。我们以飞机为例。我们创建了一个代表F-16的类。但是，我们也知道F-16有一些变体。我们可以对F16类进行子类化以表示每个变体，但随后我们将在系统中得到几个子类。此外，我们假设F16变体仅因其发动机类型而有所不同。那么一种可能的情况是，我们仅保留一个F16类来代表飞机的所有版本，但是我们为引擎添加了一个二传手。这样，我们可以创建一个F16对象作为原型，将其克隆为各种版本，然后使用正确的引擎类型组合克隆的喷气机对象，以表示飞机的相应变体。

首先我们创建一个界面

public interface IAircraftPrototype {
 
    void fly();
 
    IAircraftPrototype clone();
 
    void setEngine(F16Engine f16Engine);
}
F-16类将实现如下接口：

public class F16 implements IAircraftPrototype {
 
    // default engine
    F16Engine f16Engine = new F16Engine();
 
    @Override
    public void fly() {
        System.out.println("F-16 flying...");
    }
 
    @Override
    public IAircraftPrototype clone() {
        // Deep clone self and return the product
        return new F16();
    }
 
    public void setEngine(F16Engine f16Engine) {
        this.f16Engine = f16Engine;
    }
}
客户可以像这样行使模式：

public class Client {
 
    public void main() {
 
        IAircraftPrototype prototype = new F16();
 
        // Create F16-A
        IAircraftPrototype f16A = prototype.clone();
        f16A.setEngine(new F16AEngine());
 
        // Create F16-B
        IAircraftPrototype f16B = prototype.clone();
        f16B.setEngine(new F16BEngine());
    }
}
请注意，接口IAircraftPrototypeclone方法返回抽象类型。客户不知道具体的子类。本Boeing747类可以一样好实现相同的接口，并在它的途中产生原型的复制品。客户如果通过原型，IAircraftPrototype将不知道克隆的具体子类是F16还是Boeing747。

原型模式有助于消除子类化，因为原型对象的行为可以通过将它们与子部分组成来改变。

浅拷贝与深拷贝
原型模式要求原型类或接口实现该clone()方法。克隆可以是浅或深。假设我们的F-16类具有type的成员对象F16Engine。在浅表副本中，克隆的对象将指向与原型相同的F16Engine对象。引擎对象最终将在两者之间共享。但是，在深层副本中，克隆的对象将获得其自己的引擎对象以及其中的任何嵌套对象的副本。原型和克隆之间不会共享任何嵌套或以其他方式共享的字段。

动态加载
原型模式还有助于动态加载类。允许动态加载的语言框架将创建已加载类的实例，并将其注册到管理实体中。应用程序可以在运行时从管理器请求已加载类的对象。请注意，应用程序无法静态访问类的构造函数。

其他例子
在Java中，根Object类公开一个clone方法。该类实现interface java.lang.Cloneable。
注意事项
clone由于循环引用，实现该方法可能具有挑战性。
