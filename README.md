![Design Patterns For Humans](https://cloud.githubusercontent.com/assets/11269635/23065273/1b7e5938-f515-11e6-8dd3-d0d58de6bb9a.png)

***
<p align="center">
:tada: Explicação ultra-simplificada dos design patterns! :tada:
</p>

<p align="center">
Um assunto capaz de dar um nó na cabeça de qualquer um. Aqui eu tento fazê-los 
grudar na sua cabeça (e quem sabe na minha) explicando da maneira mais <i>simples</i>.
</p>

***

:rocket: Introdução
=================

Design patterns são soluções para problemas recorrentes; **guias de como atacar certos problemas**.
Não são classes, pacotes nem bibliotecas que você bota na aplicação e espera a mágica acontecer.
Na realidade, são guias de como atacas certos problemas em certas situações.

> Design patterns solucionam problemas recorrentes; guias de como atacar certos problemas.

A Wikipedia os descreve como

> Em engenharia de software, um design pattern é uma solução geral reutilizável
em problemas recorrentes dentro de um determinado contexto de design de software.
Não é um design pronto, que pode ser transformado diretamente em código ou código de máquina.
É uma descrição ou _template_ de como resolver um problema que pode ser utilizado
em diversas situações diferentes.

:warning: Cuidado
-----------------
- Design patterns não são uma bala de prata para todos seus problemas
- Não tente forçá-los; senão coisas ruins acontecerão.
Tenha em mente que design patterns são soluções **para** problemas, 
e não soluções **para encontrar** problemas; então não pense demais.
- Se for utilizado no local correto de maneira correta, eles podem se provar
salvadores; senão eles podem acabar resultando numa bagunça de código horrorosa.

Types of Design Patterns
-----------------

* [De Criação](#creational-design-patterns)
* [Estruturais](#structural-design-patterns)
* [Comportamentais](#behavioral-design-patterns)


Design Patterns de Criação
==========================

Resumindo
> Padrões de criação focam em como instanciar um objeto ou grupo de objetos afins.

A Wikipedia diz
> Em engenharia de software, design patterns de criação são  padrões que lidam
com os mecanismos de criação de objetos, tentando criar os objetos de maneira
adequada àquela situação.
A forma basica de criação de objetos poderiam resultar em problemas de design
ou adicionar complexidade ao design.
Design patterns de criação resolvem este problema controlando a criação de objetos
de alguma forma.
 
 * [Simple Factory](#-simple-factory)
 * [Factory Method](#-factory-method)
 * [Abstract Factory](#-abstract-factory)
 * [Builder](#-builder)
 * [Prototype](#-prototype)
 * [Singleton](#-singleton)
 
🏠 Simple Factory
--------------
Exemplo do mundo real
> Considere, você está construindo uma casa e você precisa de portas. Seria uma bagunça se cada vez que você precisar de uma porta, você colocar em suas roupas de carpinteiro e começar a fazer uma porta em sua casa. Em vez disso você o obtém feito de uma fábrica.

Resumindo
> Simple factory simplesmente gera uma instância para o cliente sem expor qualquer lógica de instanciação para o cliente.

Wikipedia diz
> Na programação orientada a objetos (OOP), uma factory é um objeto para criar outros objetos - formalmente, uma factory é uma função ou método que retorna objetos de um protótipo ou classe variável de alguma chamada de método, que é assumida como "new".

**Exemplo Programático**

Primeiro, temos uma interface de porta e uma implementação
```csharp
public interface IDoor
{
    float Width { get; set; }
    float Height { get; set; }
}

public class WoodenDoor : IDoor
{
    public float Width { get; set; }
    public float Height { get; set; }

    public WoodenDoor(float width, float height)
    {
        Width = width;
        Height = height;
    }
}
```
Então nós temos nossa factory de porta, que faz a porta e retorna-a
```csharp
class DoorFactory
{
   public static IDoor MakeDoor(float width, float height)
   {
       return new WoodenDoor(width, height);
   }
}
```
E então ele pode ser usado como
```csharp
var door = DoorFactory.makeDoor(100, 200);
Console.WriteLine("Width: " + door.Width);
Console.WriteLine("Height: " + door.Height);
```

**Quando usar?**

Ao criar um objeto não é apenas algumas atribuições e envolve alguma lógica, faz sentido colocá-lo em uma factory dedicada em vez de repetir o mesmo código em todos os lugares.

🏭 Factory Method
--------------

Exemplo do mundo real
> Considere o caso de um gerente de contratação. É impossível para uma pessoa entrevistar para cada uma das posições. Com base na abertura do trabalho, ela tem que decidir e delegar as etapas da entrevista para diferentes pessoas.

Resumindo
> Ele fornece uma maneira de delegar a lógica de instanciação a classe filho.

Wikipedia diz
> Na programação baseada em classes, o padrão de método de factory é um padrão de criação que usa métodos de factory para lidar com o problema de criação de objetos sem precisar especificar a classe exata do objeto que será criado. Isso é feito criando objetos chamando um método de factory - ou especificado em uma interface e implementado por classe filho, ou implementado em uma classe base e opcionalmente substituído por classes derivadas - em vez de chamar um construtor.
 
 **Exemplo Programático**
 
Tomando o nosso exemplo de gerente de contratação acima. Primeiro de tudo, temos uma interface de entrevistador e algumas implementações para ele

```csharp
interface IInterviewer
{
    void AskQuestions();
}

class Developer : IInterviewer
{
    public void AskQuestions()
    {
        Console.WriteLine("Asking about design patterns!");
    }
}

class CommunityExecutive : IInterviewer
{
    public void AskQuestions()
    {
        Console.WriteLine("Asking about community building");
    }
}
```

Agora vamos criar o nosso `HiringManager`

```csharp
abstract class HiringManager
{
    // Factory method
    abstract public IInterviewer MakeInterviewer();
    
    public void TakeInterview()
    {
        var interviewer = MakeInterviewer();
        interviewer.AskQuestions();
    }
}
```
Agora, qualquer filho pode estendê-lo e fornecer o entrevistador necessário
```csharp
class DevelopmentManager : HiringManager
{
    public IInterviewer MakeInterviewer()
    {
        return new Developer();
    }
}

class MarketingManager : HiringManager
{
    public IInterviewer MakeInterviewer()
    {
        return new CommunityExecutive();
    }
}
```
E então ele pode ser usado como

```csharp
var devManager = new DevelopmentManager();
devManager.TakeInterview(); // Output: Asking about design patterns

var marketingManager = new MarketingManager();
marketingManager.TakeInterview(); // Output: Asking about community building.
```

**Quando usar?**

Útil quando há algum processamento genérico em uma classe, mas a sub-classe necessária é dinamicamente decidida em tempo de execução. Ou colocando em outras palavras, quando o cliente não sabe qual sub-classe exatamente precisará.

🔨 Abstract Factory
----------------

Real world example
> Extending our door example from Simple Factory. Based on your needs you might get a wooden door from a wooden door shop, iron door from an iron shop or a PVC door from the relevant shop. Plus you might need a guy with different kind of specialities to fit the door, for example a carpenter for wooden door, welder for iron door etc. As you can see there is a dependency between the doors now, wooden door needs carpenter, iron door needs a welder etc.

In plain words
> A factory of factories; a factory that groups the individual but related/dependent factories together without specifying their concrete classes. 
  
Wikipedia says
> The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes

**Programmatic Example**

Translating the door example above. First of all we have our `Door` interface and some implementation for it

```csharp
interface IDoor
{
    void GetDescription();
}

class WoodenDoor : IDoor
{
    public void GetDescription()
    {
        Console.WriteLine("I am a wooden door");
    }
}

class IronDoor : IDoor
{
    public void GetDescription()
    {
        Console.WriteLine("I am a iron door");
    }
}
```
Then we have some fitting experts for each door type

```csharp
interface IDoorFittingExpert
{
    void GetDescription();
}

class Welder : IDoorFittingExpert
{
    public void GetDescription()
    {
        Console.WriteLine("I can only fit iron doors");
    }
}

class Carpenter : IDoorFittingExpert
{
    public void GetDescription()
    {
        Console.WriteLine("I can only fit wooden doors");
    }
}
```

Now we have our abstract factory that would let us make family of related objects i.e. wooden door factory would create a wooden door and wooden door fitting expert and iron door factory would create an iron door and iron door fitting expert
```csharp
interface IDoorFactory
{
    IDoor MakeDoor();
    IDoorFittingExpert MakeFittingExpert();
}

// Wooden factory to return carpenter and wooden door
class WoodenDoorFactory : IDoorFactory
{
    public IDoor MakeDoor()
    {
        return new WoodenDoor();
    }

    public IDoorFittingExpert MakeFittingExpert() 
    {
        return new Carpenter();
    }
}

// Iron door factory to get iron door and the relevant fitting expert
class IronDoorFactory : IDoorFactory
{
    public IDoor MakeDoor()
    {
        return new IronDoor();
    }

    public IDoorFittingExpert MakeFittingExpert()
    {
        return new Welder();
    }
}
```
And then it can be used as
```csharp
var woodenFactory = new WoodenDoorFactory();

var door = woodenFactory.MakeDoor();
var expert = woodenFactory.MakeFittingExpert();

door.GetDescription();  // Output: I am a wooden door
expert.GetDescription(); // Output: I can only fit wooden doors

// Same for Iron Factory
var ironFactory = new IronDoorFactory();

var door = ironFactory.MakeDoor();
var expert = ironFactory.MakeFittingExpert();

door.GetDescription();  // Output: I am an iron door
expert.GetDescription(); // Output: I can only fit iron doors
```

As you can see the wooden door factory has encapsulated the `carpenter` and the `wooden door` also iron door factory has encapsulated the `iron door` and `welder`. And thus it had helped us make sure that for each of the created door, we do not get a wrong fitting expert.   

**When to use?**

When there are interrelated dependencies with not-that-simple creation logic involved

👷 Builder
--------------------------------------------
Real world example
> Imagine you are at Hardee's and you order a specific deal, lets say, "Big Hardee" and they hand it over to you without *any questions*; this is the example of simple factory. But there are cases when the creation logic might involve more steps. For example you want a customized Subway deal, you have several options in how your burger is made e.g what bread do you want? what types of sauces would you like? What cheese would you want? etc. In such cases builder pattern comes to the rescue.

In plain words
> Allows you to create different flavors of an object while avoiding constructor pollution. Useful when there could be several flavors of an object. Or when there are a lot of steps involved in creation of an object.
 
Wikipedia says
> The builder pattern is an object creation software design pattern with the intentions of finding a solution to the telescoping constructor anti-pattern.

Having said that let me add a bit about what telescoping constructor anti-pattern is. At one point or the other we have all seen a constructor like below:
 
```csharp
public Burger(size, cheese = true, pepperoni = true, tomato = false, lettuce = true)
{
}
```

As you can see; the number of constructor parameters can quickly get out of hand and it might become difficult to understand the arrangement of parameters. Plus this parameter list could keep on growing if you would want to add more options in future. This is called telescoping constructor anti-pattern.

**Programmatic Example**

The sane alternative is to use the builder pattern. First of all we have our burger that we want to make

```csharp
class Burger
{
    public int Size { get; }

    public bool Cheese { get; }
    public bool Pepperoni { get; }
    public bool Lettuce { get; }
    public bool Tomato { get; }
    
    public Burger(BurgerBuilder builder)
    {
        Size = builder.Size;
        Cheese = builder.Cheese;
        Pepperoni = builder.Pepperoni;
        Lettuce = builder.Lettuce;
        Tomato = builder.Tomato;
    }
}
```

And then we have the builder

```csharp
class BurgerBuilder
{
    public int Size { get; }

    public bool Cheese { get; private set; }
    public bool Pepperoni { get; private set; }
    public bool Lettuce { get; private set; }
    public bool Tomato { get; private set; }

    public BurgerBuilder(int size)
    {
        Size = size;
    }
    
    public BurgerBuilder AddPepperoni()
    {
        Pepperoni = true;
        return this;
    }
    
    public BurgerBuilder AddLettuce()
    {
        Lettuce = true;
        return this;
    }
    
    public BurgerBuilder AddCheese()
    {
        Cheese = true;
        return this;
    }
    
    public BurgerBuilder AddTomato()
    {
        Tomato = true;
        return this;
    }
    
    public Burger Build()
    {
        return new Burger(this);
    }
}
```
And then it can be used as:

```csharp
var burger = new BurgerBuilder(14)
		.AddPepperoni()
		.AddLettuce()
		.AddTomato()
		.Build();
```

**When to use?**

When there could be several flavors of an object and to avoid the constructor telescoping. The key difference from the factory pattern is that; factory pattern is to be used when the creation is a one step process while builder pattern is to be used when the creation is a multi step process.

🐑 Prototype
------------
Real world example
> Remember dolly? The sheep that was cloned! Lets not get into the details but the key point here is that it is all about cloning

In plain words
> Create object based on an existing object through cloning.

Wikipedia says
> The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects.

In short, it allows you to create a copy of an existing object and modify it to your needs, instead of going through the trouble of creating an object from scratch and setting it up.

**Programmatic Example**

In C#, it can be easily done using `ICloneable`
  
```csharp
class Sheep : ICloneable
{
    public string Name { get; set; }
    public string Category { get; set; }

    public Sheep(string name, string category = "Mountain Sheep")
    {
        Name = name;
        Category = category;
    }
	
    public object Clone()
    {
    	return this.MemberwiseClone();
    }
}
```
Then it can be cloned like below
```csharp
var original = new Sheep('Jolly');
Console.WriteLine(original.Name); // Jolly
Console.WriteLine(original.Category); // Mountain Sheep

// Clone and modify what is required
var cloned = original.Clone();
cloned.Name = "Dolly";
Console.WriteLine(cloned.Name); // Dolly
Console.WriteLine(cloned.Category); // Mountain Sheep
```

Also you could use the magic method `Clone` to modify the cloning behavior.

**When to use?**

When an object is required that is similar to existing object or when the creation would be expensive as compared to cloning.

💍 Singleton
------------
Real world example
> There can only be one president of a country at a time. The same president has to be brought to action, whenever duty calls. President here is singleton.

In plain words
> Ensures that only one object of a particular class is ever created.

Wikipedia says
> In software engineering, the singleton pattern is a software design pattern that restricts the instantiation of a class to one object. This is useful when exactly one object is needed to coordinate actions across the system.

Singleton pattern is actually considered an anti-pattern and overuse of it should be avoided. It is not necessarily bad and could have some valid use-cases but should be used with caution because it introduces a global state in your application and change to it in one place could affect in the other areas and it could become pretty difficult to debug. The other bad thing about them is it makes your code tightly coupled plus it mocking the singleton could be difficult.

**Programmatic Example**

To create a singleton, make the constructor private, disable cloning, disable extension and create a static variable to house the instance
```csharp
// Example taken from http://csharpindepth.com/Articles/General/Singleton.aspx
// There is a lot of caveat. So read it!
public sealed class President
{
    private static readonly President instance = new President();

    // Explicit static constructor to tell C# compiler
    // not to mark type as beforefieldinit
    static President()
    {
    }

    private President()
    {
    }

    public static President Instance
    {
        get
        {
            return instance;
        }
    }
} 
```
Then in order to use
```csharp
var president1 = President.Instance;
var president2 = President.Instance;

Console.WriteLine(President1 == President2); // true
```

Design Patterns Estruturais
==========================

Resumindo
> Padrões estruturais basicamente se preocupam com a composição de objetos
ou em outras palavras como entidades usam umas às outras.
Ou ainda outra explicação seria como elas ajudam a responder "Como construir
um componente de software?".

A Wikipedia diz
> Em engenharia de software, design patterns estruturais são padrões que facilitam
o design através da identificação de uma maneira simples de construir as relações entre as
entidades.

 * [Adapter](#-adapter)
 * [Bridge](#-bridge)
 * [Composite](#-composite)
 * [Decorator](#-decorator)
 * [Facade](#-facade)
 * [Flyweight](#-flyweight)
 * [Proxy](#-proxy)

🔌 Adapter
-------
Exemplo do mundo real
> Considere que você tem algumas fotos em seu cartão de memória e que você precisa transferi-los para o seu computador. E para transferi-los você precisa de algum tipo de adaptador que seja compatível com as portas do computador. Neste caso o leitor de cartões é um adaptador.
> Outro exemplo seria o famoso adaptador de alimentação; Um plugue de três pernas não pode ser conectado a uma saída de duas pontas, ele precisa usar um adaptador de energia que torna compatível com a saída de duas pontas.
> Outro exemplo seria um tradutor traduzindo palavras ditas por uma pessoa a outra.

Resumindo
> O padrão Adapter permite que você envolva um objeto incompatível em um adaptador para torná-lo compatível com outra classe.

A Wikipidia diz
> Na engenharia de software, o padrão do Adapter é um padrão de design de software que permite que a interface de uma classe existente seja usada como outra interface. É freqüentemente usado para fazer com que as classes existentes trabalhem com outras sem modificar seu código-fonte.

**Exemplo Programático**

Considere um jogo onde há um caçador e ele caça leões.

Primeiro temos uma interface `Lion` que todos os tipos de leões têm que implementar.

```csharp
interface ILion
{
    void Roar();
}

class AfricanLion : ILion
{
    public void Roar() {}
}

class AsianLion : ILion
{
    public void Roar() {}
}
```
E o caçador espera que qualquer implementação da interface `ILion` para caçar.
```csharp
class Hunter
{
    public void Hunt(ILion lion)
    {
    }
}
```

Agora vamos dizer que temos de adicionar um `WildDog` no nosso jogo para que o caçador possa caçar isso também. Mas não podemos fazer isso diretamente porque o cão tem uma interface diferente. Para torná-lo compatível para o nosso caçador, vamos ter que criar um adaptador compatível.
 
```csharp
// Isso precisa ser adicionado ao jogo
class WildDog
{
    public void Bark() {}
}

// Adaptador em torno do cão selvagem para torná-lo compatível com o nosso jogo
class WildDogAdapter : ILion
{
    private Dog { get; set; }

    public WildDogAdapter(WildDog dog)
    {
        Dog = dog;
    }
    
    public void Roar()
    {
        Dog.Bark();
    }
}
```
E agora o `WildDog` pode ser usado em nosso jogo usando` WildDogAdapter`.

```csharp
var wildDog = new WildDog();
wildDogAdapter = new WildDogAdapter(wildDog);

var hunter = new Hunter();
hunter.Hunt(wildDogAdapter);
```

🚡 Bridge
------
Exemplo do mundo real
> 
Considere que você tem um site com páginas diferentes e supostamente o usuário pode alterar o tema. O que você faria? Criaria várias cópias de cada uma das páginas para cada um dos temas ou você apenas criaria o tema separado e carregar com base nas preferências do usuário? Bridge permite que você faça a segunda opção.

![Com e sem o Bridge](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

Resumindo
> Bridge pattern é sobre preferir a composição sobre herança. Detalhes de implementação são empurrados de uma hierarquia, para outro objeto com uma hierarquia separada.

Wikipedia diz
> O bridge é um padrão de projeto usado na engenharia de software que destina-se a "desacoplar uma abstração de sua implementação para que os dois possam variar independentemente".

**Exemplo Programático**

Traduzindo nosso exemplo de WebPage acima. Aqui temos a hierarquia `IWebPage`

```csharp
interface IWebPage
{
    string GetContent();
}

class About : IWebPage
{
    private ITheme Theme { get; set; }
    
    public About(ITheme theme)
    {
        Theme = theme;
    }
    
    public string GetContent()
    {
        return "About page in " + Theme.GetColor();
    }
}

class Careers : IWebPage
{
   private ITheme Theme { get; set; }
   
   public Careers(ITheme theme)
   {
       Theme = theme;
   }
   
   public string GetContent()
   {
       return "Careers page in " + Theme.GetColor();
   } 
}
```
E a hierarquia de tema separada
```csharp
interface ITheme
{
    string GetColor();
}

class DarkTheme : ITheme
{
    public string GetColor()
    {
        return "Dark Black";
    }
}
class LightTheme : ITheme
{
    public string getColor()
    {
        return "Off white";
    }
}
class AquaTheme : ITheme
{
    public string GetColor()
    {
        return "Light blue";
    }
}
```
E ambas as hierarquias
```csharp
var darkTheme = new DarkTheme();

var about = new About(darkTheme);
var careers = new Careers(darkTheme);

Console.WriteLine(about.GetContent()); // "About page in Dark Black";
Console.WriteLine(careers.GetContent()); // "Careers page in Dark Black";
```

🌿 Composite
-----------------

Real world example
> Every organization is composed of employees. Each of the employees has same features i.e. has a salary, has some responsibilities, may or may not report to someone, may or may not have some subordinates etc.

In plain words
> Composite pattern lets clients to treat the individual objects in a uniform manner.

Wikipedia says
> In software engineering, the composite pattern is a partitioning design pattern. The composite pattern describes that a group of objects is to be treated in the same way as a single instance of an object. The intent of a composite is to "compose" objects into tree structures to represent part-whole hierarchies. Implementing the composite pattern lets clients treat individual objects and compositions uniformly.

**Programmatic Example**

Taking our employees example from above. Here we have different employee types

```csharp
interface IEmployee
{
    string Name { get; }
    float Salary { get; set; }
    IEmployee[] Roles  { get; }
}

class Developer : IEmployee
{
    public float Salary { get; set; }
    public string Name { get; }
    public IEmployee[] Roles  { get; }

    public Developer(string name, float salary)
    {
        Name = name;
        Salary = salary;
    }
}

class Designer : IEmployee
{
    public float Salary { get; set; }
    public string Name { get; }
    public IEmployee[] Roles  { get; }

    public Designer(string name, float salary)
    {
        Name = name;
        Salary = salary;
    }
}
```

Then we have an organization which consists of several different types of employees

```csharp
class Organization
{
    private IList<IEmployee> Employees { get; } = new List<IEmployee>();

    public void AddEmployee(IEmployee employee)
    {
        Employees.Add(employee);
    }

    public float GetNetSalaries() => Employees.Sum(employee => employee.Salary);
}
```

And then it can be used as

```csharp
// Prepare the employees
var john = new Developer("John Doe", 12000);
var jane = new Designer("Jane", 10000);

// Add them to organization
var organization = new Organization();
organization.AddEmployee(john);
organization-.AddEmployee(jane);

Console.WriteLine("Net salaries: " + organization.GetNetSalaries()); // Net Salaries: 22000
```

☕ Decorator
-------------

Real world example

> Imagine you run a car service shop offering multiple services. Now how do you calculate the bill to be charged? You pick one service and dynamically keep adding to it the prices for the provided services till you get the final cost. Here each type of service is a decorator.

In plain words
> Decorator pattern lets you dynamically change the behavior of an object at run time by wrapping them in an object of a decorator class.

Wikipedia says
> In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. The decorator pattern is often useful for adhering to the Single Responsibility Principle, as it allows functionality to be divided between classes with unique areas of concern.

**Programmatic Example**

Lets take coffee for example. First of all we have a simple coffee implementing the coffee interface
```csharp
interface ICoffee
{
    int Cost { get; }
    string Description { get; }
}

class SimpleCoffee : ICoffee
{
    public int Cost { get; } = 10;
    public string Description { get; } = "Simple coffee";
}
```
We want to make the code extensible to allow options to modify it if required. Lets make some add-ons (decorators)
```csharp
class MilkCoffee : ICoffee
{
    protected ICoffee Coffee { get; }
    
    public int Cost { get { return Coffee.Cost + 2; } }
	
    public string Description { get { return Coffee.Description + ", milk"; } }
	
    public MilkCoffee(ICoffee coffee)
    {
        Coffee = coffee;
    }
}

class WhipCoffee : ICoffee
{
    protected ICoffee Coffee { get; }

    public int Cost { get { return Coffee.Cost + 5; } }
	
    public string Description { get { return Coffee.Description + ", whip"; } }
	
    public WhipCoffee(ICoffee coffee)
    {
        Coffee = coffee;
    }
}

class VanillaCoffee : ICoffee
{
    protected ICoffee Coffee { get; }

    public int Cost { get { return Coffee.Cost + 3; } }
	
    public string Description { get { return Coffee.Description + ", vanilla"; } }
	
    public VanillaCoffee(ICoffee coffee)
    {
        Coffee = coffee;
    }
}
```
Lets make a coffee now
```csharp
var someCoffee = new SimpleCoffee();
Console.WriteLine(someCoffee.Cost()); // 10
Console.WriteLine(someCoffee.Description()); // Simple Coffee

someCoffee = new MilkCoffee(someCoffee);
Console.WriteLine(someCoffee.Cost()); // 12
Console.WriteLine(someCoffee.Description(); // Simple Coffee, milk

someCoffee = new WhipCoffee(someCoffee);
Console.WriteLine(someCoffee.Cost()); // 17
Console.WriteLine(someCoffee.Description()); // Simple Coffee, milk, whip

someCoffee = new VanillaCoffee($someCoffee);
Console.WriteLine(someCoffee.Cost()); // 20
Console.WriteLine(someCoffee.Description()); // Simple Coffee, milk, whip, vanilla
```

📦 Facade
----------------

Real world example
> How do you turn on the computer? "Hit the power button" you say! That is what you believe because you are using a simple interface that computer provides on the outside, internally it has to do a lot of stuff to make it happen. This simple interface to the complex subsystem is a facade.

In plain words
> Facade pattern provides a simplified interface to a complex subsystem.

Wikipedia says
> A facade is an object that provides a simplified interface to a larger body of code, such as a class library.

**Programmatic Example**
Taking our computer example from above. Here we have the computer class

```csharp
class Computer
{
    public void GetElectricShock()
    {
        Console.WriteLine("Ouch!");
    }

    public void MakeSound()
    {
        Console.WriteLine("Beep beep!");
    }

    public void ShowLoadingScreen()
    {
        Console.WriteLine("Loading..");
    }

    public void Bam()
    {
        Console.WriteLine("Ready to be used!");
    }

    public void CloseEverything()
    {
        Console.WriteLine("Bup bup bup buzzzz!");
    }

    public void Sooth()
    {
        Console.WriteLine("Zzzzz");
    }

    public void PullCurrent()
    {
        Console.WriteLine("Haaah!");
    }
}
```
Here we have the facade
```csharp
class ComputerFacade
{
    protected Computer Computer;

    public ComputerFacade(Computer computer)
    {
        Computer = computer;
    }

    public void TurnOn()
    {
        Computer.GetElectricShock();
        Computer.MakeSound();
        Computer.ShowLoadingScreen();
        Computer.Bam();
    }

    public void TurnOff()
    {
        Computer.CloseEverything();
        Computer.PullCurrent();
        Computer.Sooth();
    }
}
```
Now to use the facade
```csharp
var computer = new ComputerFacade(new Computer());
computer.TurnOn(); // Ouch! Beep beep! Loading.. Ready to be used!
computer.TurnOff(); // Bup bup buzzz! Haah! Zzzzz
```

🍃 Flyweight
---------

Real world example
> Did you ever have fresh tea from some stall? They often make more than one cup that you demanded and save the rest for any other customer so to save the resources e.g. gas etc. Flyweight pattern is all about that i.e. sharing.

In plain words
> It is used to minimize memory usage or computational expenses by sharing as much as possible with similar objects.

Wikipedia says
> In computer programming, flyweight is a software design pattern. A flyweight is an object that minimizes memory use by sharing as much data as possible with other similar objects; it is a way to use objects in large numbers when a simple repeated representation would use an unacceptable amount of memory.

**Programmatic example**
Translating our tea example from above. First of all we have tea types and tea maker

```csharp
// Anything that will be cached is flyweight. 
// Types of tea here will be flyweights.
class KarakTea
{
}

// Acts as a factory and saves the tea
class TeaMaker
{
    protected Dictionary<string, KarakTea> AvailableTea = new Dictionary<string, KarakTea>();

    public KarakTea Make(string preference)
    {
        if (!AvailableTea.Keys.Contains(preference))
	{
            AvailableTea.Add(preference, new KarakTea());
        }

        return AvailableTea[preference];
    }
}
```
Then we have the `TeaShop` which takes orders and serves them
```csharp
class TeaShop
{    
    protected Dictionary<int, KarakTea> Orders = new Dictionary<int, KarakTea>();
    protected TeaMaker TeaMaker;

    public TeaShop(TeaMaker teaMaker)
    {
        TeaMaker = teaMaker;
    }

    public void TakeOrder(string teaType, int table)
    {
        Orders[table] = TeaMaker.Make(teaType);
    }

    public void Serve()
    {
        foreach(var table in Orders.Select(order => order.Key))
        {
            Console.WriteLine("Serving tea to table# " + table);
        }
    }
}
```
And it can be used as below

```csharp
var teaMaker = new TeaMaker();
var shop = new TeaShop(teaMaker);

shop.TakeOrder("less sugar", 1);
shop.TakeOrder("more milk", 2);
shop.TakeOrder("without sugar", 5);

shop.Serve();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
```

🎱 Proxy
-------------------
Real world example
> Have you ever used an access card to go through a door? There are multiple options to open that door i.e. it can be opened either using access card or by pressing a button that bypasses the security. The door's main functionality is to open but there is a proxy added on top of it to add some functionality. Let me better explain it using the code example below.

In plain words
> Using the proxy pattern, a class represents the functionality of another class.

Wikipedia says
> A proxy, in its most general form, is a class functioning as an interface to something else. A proxy is a wrapper or agent object that is being called by the client to access the real serving object behind the scenes. Use of the proxy can simply be forwarding to the real object, or can provide additional logic. In the proxy extra functionality can be provided, for example caching when operations on the real object are resource intensive, or checking preconditions before operations on the real object are invoked.

**Programmatic Example**
Taking our security door example from above. Firstly we have the door interface and an implementation of door

```csharp
interface IDoor
{
    void Open();
    void Close();
}

class LabDoor : IDoor
{
    public void Open()
    {
        Console.WriteLine("Opening lab door");
    }

    public void Close()
    {
        Console.WriteLine("Closing the lab door");
    }
}
```
Then we have a proxy to secure any doors that we want
```csharp
class Security
{
    protected IDoor Door;

    public Security(IDoor door)
    {
        Door = door;
    }

    public void Open(string password)
    {
        if (Authenticate(password))
        {
            Door.Open();
        }
        else
        {
            Console.WriteLine("Big no! It ain't possible.");
        }
    }

    public bool Authenticate(string password)
    {
        return password == "$ecr@t";
    }

    public void Close()
    {
        Door.Close();
    }
}
```
And here is how it can be used
```csharp
var door = new Security(new LabDoor());
door.Open("invalid"); // Big no! It ain't possible.

door.Open("$ecr@t"); // Opening lab door
door.Close(); // Closing lab door
```
Yet another example would be some sort of data-mapper implementation. For example, I recently made an ODM (Object Data Mapper) for MongoDB using this pattern where I wrote a proxy around mongo classes while utilizing the magic method `__call()`. All the method calls were proxied to the original mongo class and result retrieved was returned as it is but in case of `find` or `findOne` data was mapped to the required class objects and the object was returned instead of `Cursor`.

Design Patterns Comportamentais
==========================

Resumindo
> Está preocupado com a atribuição de responsabilidades entre os objetos.
O que diferencia com os padrões estruturais é que eles não especificam somente
a estrutura, mas também traçam os padrões para as passagem/comunicação de mensagens entre eles.
Em outras palavras, eles ajudam a responder "Como rodar um comportamento no componente de software?".

A Wikipedia diz
> Em engenharia de software, design patterns comportamentais são padrões que 
identificam padrões comuns de comunicação entre os objetos e concretizam este padrão.
Desta forma, estes padrões aumentam a flexibilidade em executar estas comunicações.

* [Chain of Responsibility](#-chain-of-responsibility)
* [Command](#-command)
* [Iterator](#-iterator)
* [Mediator](#-mediator)
* [Memento](#-memento)
* [Observer](#-observer)
* [Visitor](#-visitor)
* [Strategy](#-strategy)
* [State](#-state)
* [Template Method](#-template-method)

🔗 Chain of Responsibility
-----------------------

Real world example
> For example, you have three payment methods (`A`, `B` and `C`) setup in your account; each having a different amount in it. `A` has 100 USD, `B` has 300 USD and `C` having 1000 USD and the preference for payments is chosen as `A` then `B` then `C`. You try to purchase something that is worth 210 USD. Using Chain of Responsibility, first of all account `A` will be checked if it can make the purchase, if yes purchase will be made and the chain will be broken. If not, request will move forward to account `B` checking for amount if yes chain will be broken otherwise the request will keep forwarding till it finds the suitable handler. Here `A`, `B` and `C` are links of the chain and the whole phenomenon is Chain of Responsibility.

In plain words
> It helps building a chain of objects. Request enters from one end and keeps going from object to object till it finds the suitable handler.

Wikipedia says
> In object-oriented design, the chain-of-responsibility pattern is a design pattern consisting of a source of command objects and a series of processing objects. Each processing object contains logic that defines the types of command objects that it can handle; the rest are passed to the next processing object in the chain.

**Programmatic Example**

Translating our account example above. First of all we have a base account having the logic for chaining the accounts together and some accounts

```php
abstract class Account {
    protected $successor;
    protected $balance;

    public function setNext(Account $account) {
        $this->successor = $account;
    }
    
    public function pay(float $amountToPay) {
        if ($this->canPay($amountToPay)) {
            echo sprintf('Paid %s using %s' . PHP_EOL, $amount, get_called_class());
        } else if ($this->successor) {
            echo sprintf('Cannot pay using %s. Proceeding ..' . PHP_EOL, get_called_class());
            $this->successor->pay($amountToPay);
        } else {
            throw Exception('None of the accounts have enough balance');
        }
    }
    
    public function canPay($amount) : bool {
        return $this->balance <= $amount;
    }
}

class Bank extends Account {
    protected $balance;

    public function __construct(float $balance) {
        $this->$balance = $balance;
    }
}

class Paypal extends Account {
    protected $balance;

    public function __construct(float $balance) {
        $this->$balance = $balance;
    }
}

class Bitcoin extends Account {
    protected $balance;

    public function __construct(float $balance) {
        $this->$balance = $balance;
    }
}
```

Now let's prepare the chain using the links defined above (i.e. Bank, Paypal, Bitcoin)

```php
// Let's prepare a chain like below
//      $bank->$paypal->$bitcoin
//
// First priority bank
//      If bank can't pay then paypal
//      If paypal can't pay then bit coin

$bank = new Bank(100);          // Bank with balance 100
$paypal = new Paypal(200);      // Paypal with balance 200
$bitcoin = new Bitcoin(300);    // Bitcoin with balance 300

$bank->setNext($paypal);
$paypal->setNext($bitcoin);

// Let's try to pay using the first priority i.e. bank
$bank->pay(259);

// Output will be
// ==============
// Cannot pay using bank. Proceeding ..
// Cannot pay using paypal. Proceeding ..: 
// Paid 259 using Bitcoin!
```

👮 Command
-------

Real world example
> A generic example would be you ordering a food at restaurant. You (i.e. `Client`) ask the waiter (i.e. `Invoker`) to bring some food (i.e. `Command`) and waiter simply forwards the request to Chef (i.e. `Receiver`) who has the knowledge of what and how to cook. 
> Another example would be you (i.e. `Client`) switching on (i.e. `Command`) the television (i.e. `Receiver`) using a remote control (`Invoker`).

In plain words
> Allows you to encapsulate actions in objects. The key idea behind this pattern is to provide the means to decouple client from receiver.

Wikipedia says
> In object-oriented programming, the command pattern is a behavioral design pattern in which an object is used to encapsulate all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object that owns the method and values for the method parameters.

**Programmatic Example**

First of all we have the receiver that has the implementation of every action that could be performed
```php
// Receiver
class Bulb {
    public function turnOn() {
        echo "Bulb has been lit";
    }
    
    public function turnOff() {
        echo "Darkness!";
    }
}
```
then we have an interface that each of the commands are going to implement and then we have a set of commands
```php
interface Command {
    public function execute();
    public function undo();
    public function redo();
}

// Command
class TurnOn implements Command {
    protected $bulb;
    
    public function __construct(Bulb $bulb) {
        $this->bulb = $bulb;
    }
    
    public function execute() {
        $this->bulb->turnOn();
    }
    
    public function undo() {
        $this->bulb->turnOff();
    }
    
    public function redo() {
        $this->execute();
    }
}

class TurnOff implements Command {
    protected $bulb;
    
    public function __construct(Bulb $bulb) {
        $this->bulb = $bulb;
    }
    
    public function execute() {
        $this->bulb->turnOff();
    }
    
    public function undo() {
        $this->bulb->turnOn();
    }
    
    public function redo() {
        $this->execute();
    }
}
```
Then we have an `Invoker` with whom the client will interact to process any commands
```php
// Invoker
class RemoteControl {
    
    public function submit(Command $command) {
        $command->execute();
    }
}
```
Finally let's see how we can use it in our client
```php
$bulb = new Bulb();

$turnOn = new TurnOn($bulb);
$turnOff = new TurnOff($bulb);

$remote = new RemoteControl();
$remoteControl->submit($turnOn); // Bulb has been lit!
$remoteControl->submit($turnOff); // Darkness!
```

Command pattern can also be used to implement a transaction based system. Where you keep maintaining the history of commands as soon as you execute them. If the final command is successfully executed, all good otherwise just iterate through the history and keep executing the `undo` on all the executed commands. 

➿ Iterator
--------

Real world example
> An old radio set will be a good example of iterator, where user could start at some channel and then use next or previous buttons to go through the respective channels. Or take an example of MP3 player or a TV set where you could press the next and previous buttons to go through the consecutive channels or in other words they all provide an interface to iterate through the respective channels, songs or radio stations.  

In plain words
> It presents a way to access the elements of an object without exposing the underlying presentation.

Wikipedia says
> In object-oriented programming, the iterator pattern is a design pattern in which an iterator is used to traverse a container and access the container's elements. The iterator pattern decouples algorithms from containers; in some cases, algorithms are necessarily container-specific and thus cannot be decoupled.

**Programmatic example**
In PHP it is quite easy to implement using SPL (Standard PHP Library). Translating our radio stations example from above. First of all we have `RadioStation`

```php
class RadioStation {
    protected $frequency;

    public function __construct(float $frequency) {
        $this->frequency = $frequency;    
    }
    
    public function getFrequency() : float {
        return $this->frequency;
    }
}
```
Then we have our iterator

```php
use Countable;
use Iterator;

class StationList implements Countable, Iterator {
    /** @var RadioStation[] $stations */
    protected $stations = [];
    
    /** @var int $counter */
    protected $counter;
    
    public function addStation(RadioStation $station) {
        $this->stations[] = $station;
    }
    
    public function removeStation(RadioStation $toRemove) {
        $toRemoveFrequency = $toRemove->getFrequency();
        $this->stations = array_filter($this->stations, function (RadioStation $station) use ($toRemoveFrequency) {
            return $station->getFrequency() !== $toRemoveFrequency;
        });
    }
    
    public function count() : int {
        return count($this->stations);
    }
    
    public function current() : RadioStation {
        return $this->stations[$this->counter];
    }
    
    public function key() {
        return $this->counter;
    }
    
    public function next() {
        $this->counter++;
    }
    
    public function rewind() {
        $this->counter = 0;
    }
    
    public function valid(): bool
    {
        return isset($this->stations[$this->counter]);
    }
}
```
And then it can be used as
```php
$stationList = new StationList();

$stationList->addStation(new Station(89));
$stationList->addStation(new Station(101));
$stationList->addStation(new Station(102));
$stationList->addStation(new Station(103.2));

foreach($stationList as $station) {
    echo $station->getFrequency() . PHP_EOL;
}

$stationList->removeStation(new Station(89)); // Will remove station 89
```

👽 Mediator
========

Real world example
> A general example would be when you talk to someone on your mobile phone, there is a network provider sitting between you and them and your conversation goes through it instead of being directly sent. In this case network provider is mediator. 

In plain words
> Mediator pattern adds a third party object (called mediator) to control the interaction between two objects (called colleagues). It helps reduce the coupling between the classes communicating with each other. Because now they don't need to have the knowledge of each other's implementation. 

Wikipedia says
> In software engineering, the mediator pattern defines an object that encapsulates how a set of objects interact. This pattern is considered to be a behavioral pattern due to the way it can alter the program's running behavior.

**Programmatic Example**

Here is the simplest example of a chat room (i.e. mediator) with users (i.e. colleagues) sending messages to each other. 

First of all, we have the mediator i.e. the chat room 

```php
// Mediator
class ChatRoom implements ChatRoomMediator {
    public function showMessage(User $user, string $message) {
        $time = date('M d, y H:i');
        $sender = $user->getName();

        echo $time . '[' . $sender . ']:' . $message;
    }
}
```

Then we have our users i.e. colleagues
```php
class User {
    protected $name;
    protected $chatMediator;

    public function __construct(string $name, ChatRoomMediator $chatMediator) {
        $this->name = $name;
        $this->chatMediator = $chatMediator;
    }
    
    public function getName() {
        return $this->name;
    }
    
    public function send($message) {
        $this->chatMediator->showMessage($this, $message);
    }
}
```
And the usage
```php
$mediator = new ChatRoom();

$john = new User('John Doe', $mediator);
$jane = new User('Jane Doe', $mediator);

$john->send('Hi there!');
$jane->send('Hey!');

// Output will be
// Feb 14, 10:58 [John]: Hi there!
// Feb 14, 10:58 [Jane]: Hey!
```

💾 Memento
-------
Real world example
> Take the example of calculator (i.e. originator), where whenever you perform some calculation the last calculation is saved in memory (i.e. memento) so that you can get back to it and maybe get it restored using some action buttons (i.e. caretaker). 

In plain words
> Memento pattern is about capturing and storing the current state of an object in a manner that it can be restored later on in a smooth manner.

Wikipedia says
> The memento pattern is a software design pattern that provides the ability to restore an object to its previous state (undo via rollback).

Usually useful when you need to provide some sort of undo functionality.

**Programmatic Example**

Lets take an example of text editor which keeps saving the state from time to time and that you can restore if you want.

First of all we have our memento object that will be able to hold the editor state

```php
class EditorMemento {
    protected $content;
    
    public function __construct(string $content) {
        $this->content = $content;
    }
    
    public function getContent() {
        return $this->content;
    }
}
```

Then we have our editor i.e. originator that is going to use memento object

```php
class Editor {
    protected $content = '';
    
    public function type(string $words) {
        $this->content = $this->content . ' ' . $words;
    }
    
    public function getContent() {
        return $this->content;
    }
    
    public function save() {
        return new EditorMemento($this->content);
    }
    
    public function restore(EditorMemento $memento) {
        $this->content = $memento->getContent();
    }
}
```

And then it can be used as 

```php
$editor = new Editor();

// Type some stuff
$editor->type('This is the first sentence.');
$editor->type('This is second.');

// Save the state to restore to : This is the first sentence. This is second.
$saved = $editor->save();

// Type some more
$editor->type('And this is third.');

// Output: Content before Saving
echo $editor->getContent(); // This is the first sentence. This is second. And this is third.

// Restoring to last saved state
$editor->restore($saved);

$editor->getContent(); // This is the first sentence. This is second.
```

😎 Observer
--------
Real world example
> A good example would be the job seekers where they subscribe to some job posting site and they are notified whenever there is a matching job opportunity.   

In plain words
> Defines a dependency between objects so that whenever an object changes its state, all its dependents are notified.

Wikipedia says
> The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods.

**Programmatic example**

Translating our example from above. First of all we have job seekers that need to be notified for a job posting
```php
class JobPost {
    protected $title;
    
    public function __construct(string $title) {
        $this->title = $title;
    }
    
    public function getTitle() {
        return $this->title;
    }
}

class JobSeeker implements Observer {
    protected $name;

    public function __construct(string $name) {
        $this->name = $name;
    }

    public function onJobPosted(JobPost $job) {
        // Do something with the job posting
        echo 'Hi ' . $this->name . '! New job posted: '. $job->getTitle();
    }
}
```
Then we have our job postings to which the job seekers will subscribe
```php
class JobPostings implements Observable {
    protected $observers = [];
    
    protected function notify(JobPost $jobPosting) {
        foreach ($this->observers as $observer) {
            $observer->onJobPosted($jobPosting);
        }
    }
    
    public function attach(Observer $observer) {
        $this->observers[] = $observer;
    }
    
    public function addJob(JobPost $jobPosting) {
        $this->notify($jobPosting);
    }
}
```
Then it can be used as
```php
// Create subscribers
$johnDoe = new JobSeeker('John Doe');
$janeDoe = new JobSeeker('Jane Doe');
$kaneDoe = new JobSeeker('Kane Doe');

// Create publisher and attach subscribers
$jobPostings = new JobPostings();
$jobPostings->attach($johnDoe);
$jobPostings->attach($janeDoe);

// Add a new job and see if subscribers get notified
$jobPostings->addJob(new JobPost('Software Engineer'));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer
```

🏃 Visitor
-------
Real world example
> Consider someone visiting Dubai. They just need a way (i.e. visa) to enter Dubai. After arrival, they can come and visit any place in Dubai on their own without having to ask for permission or to do some leg work in order to visit any place here; just let them know of a place and they can visit it. Visitor pattern lets you do just that, it helps you add places to visit so that they can visit as much as they can without having to do any legwork.

In plain words
> Visitor pattern lets you add further operations to objects without having to modify them.
    
Wikipedia says
> In object-oriented programming and software engineering, the visitor design pattern is a way of separating an algorithm from an object structure on which it operates. A practical result of this separation is the ability to add new operations to existing object structures without modifying those structures. It is one way to follow the open/closed principle.

**Programmatic example**

Let's take an example of a zoo simulation where we have several different kinds of animals and we have to make them Sound. Let's translate this using visitor pattern 

```php
// Visitee
interface Animal {
    public function accept(AnimalOperation $operation);
}

// Visitor
interface AnimalOperation {
    public function visitMonkey(Monkey $monkey);
    public function visitLion(Lion $lion);
    public function visitDolphin(Dolphin $dolphin);
}
```
Then we have our implementations for the animals
```php
class Monkey implements Animal {
    
    public function shout() {
        echo 'Ooh oo aa aa!';
    }

    public function accept(AnimalOperation $operation) {
        $operation->visitMonkey($this);
    }
}

class Lion implements Animal {
    public function roar() {
        echo 'Roaaar!';
    }
    
    public function accept(AnimalOperation $operation) {
        $operation->visitLion($this);
    }
}

class Dolphin implements Animal {
    public function speak() {
        echo 'Tuut tuttu tuutt!';
    }
    
    public function accept(AnimalOperation $operation) {
        $operation->visitDolphin($this);
    }
}
```
Let's implement our visitor
```php
class Speak implements AnimalOperation {
    public function visitMonkey(Monkey $monkey) {
        $monkey->shout();
    }
    
    public function visitLion(Lion $lion) {
        $lion->roar();
    }
    
    public function visitDolphin(Dolphin $dolphin) {
        $dolphin->speak();
    }
}
```

And then it can be used as
```php
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
```
We could have done this simply by having a inheritance hierarchy for the animals but then we would have to modify the animals whenever we would have to add new actions to animals. But now we will not have to change them. For example, let's say we are asked to add the jump behavior to the animals, we can simply add that by creating a new visitor i.e.

```php
class Jump implements AnimalOperation {
    public function visitMonkey(Monkey $monkey) {
        echo 'Jumped 20 feet high! on to the tree!';
    }
    
    public function visitLion(Lion $lion) {
        echo 'Jumped 7 feet! Back on the ground!';
    }
    
    public function visitDolphin(Dolphin $dolphin) {
        echo 'Walked on water a little and disappeared';
    }
}
```
And for the usage
```php
$jump = new Jump();

$monkey->accept($speak);   // Ooh oo aa aa!
$monkey->accept($jump);    // Jumped 20 feet high! on to the tree!

$lion->accept($speak);     // Roaaar!
$lion->accept($jump);      // Jumped 7 feet! Back on the ground! 

$dolphin->accept($speak);  // Tuut tutt tuutt! 
$dolphin->accept($jump);   // Walked on water a little and disappeared
```

💡 Strategy
--------

Real world example
> Consider the example of sorting, we implemented bubble sort but the data started to grow and bubble sort started getting very slow. In order to tackle this we implemented Quick sort. But now although the quick sort algorithm was doing better for large datasets, it was very slow for smaller datasets. In order to handle this we implemented a strategy where for small datasets, bubble sort will be used and for larger, quick sort.

In plain words
> Strategy pattern allows you to switch the algorithm or strategy based upon the situation.

Wikipedia says
> In computer programming, the strategy pattern (also known as the policy pattern) is a behavioural software design pattern that enables an algorithm's behavior to be selected at runtime.
 
**Programmatic example**

Translating our example from above. First of all we have our strategy interface and different strategy implementations

```php
interface SortStrategy {
    public function sort(array $dataset) : array; 
}

class BubbleSortStrategy implements SortStrategy {
    public function sort(array $dataset) : array {
        echo "Sorting using bubble sort";
         
        // Do sorting
        return $dataset;
    }
} 

class QuickSortStrategy implements SortStrategy {
    public function sort(array $dataset) : array {
        echo "Sorting using quick sort";
        
        // Do sorting
        return $dataset;
    }
}
```
 
And then we have our client that is going to use any strategy
```php
class Sorter {
    protected $sorter;
    
    public function __construct(SortStrategy $sorter) {
        $this->sorter = $sorter;
    }
    
    public function sort(array $dataset) : array {
        return $this->sorter->sort($dataset);
    }
}
```
And it can be used as
```php
$dataset = [1, 5, 4, 3, 2, 8];

$sorter = new Sorter(new BubbleSortStrategy());
$sorter->sort($dataset); // Output : Sorting using bubble sort

$sorter = new Sorter(new QuickSortStrategy());
$sorter->sort($dataset); // Output : Sorting using quick sort
```

💢 State
-----
Real world example
> Imagine you are using some drawing application, you choose the paint brush to draw. Now the brush changes its behavior based on the selected color i.e. if you have chosen red color it will draw in red, if blue then it will be in blue etc.  

In plain words
> It lets you change the behavior of a class when the state changes.

Wikipedia says
> The state pattern is a behavioral software design pattern that implements a state machine in an object-oriented way. With the state pattern, a state machine is implemented by implementing each individual state as a derived class of the state pattern interface, and implementing state transitions by invoking methods defined by the pattern's superclass.
> The state pattern can be interpreted as a strategy pattern which is able to switch the current strategy through invocations of methods defined in the pattern's interface.

**Programmatic example**

Let's take an example of text editor, it lets you change the state of text that is typed i.e. if you have selected bold, it starts writing in bold, if italic then in italics etc.

First of all we have our state interface and some state implementations

```php
interface WritingState {
    public function write(string $words);
}

class UpperCase implements WritingState {
    public function write(string $words) {
        echo strtoupper($words); 
    }
} 

class LowerCase implements WritingState {
    public function write(string $words) {
        echo strtolower($words); 
    }
}

class Default implements WritingState {
    public function write(string $words) {
        echo $words;
    }
}
```
Then we have our editor
```php
class TextEditor {
    protected $state;
    
    public function __construct(WritingState $state) {
        $this->state = $state;
    }
    
    public function setState(WritingState $state) {
        $this->state = $state;
    }
    
    public function type(string $words) {
        $this->state->write($words);
    }
}
```
And then it can be used as
```php
$editor = new TextEditor(new Default());

$editor->type('First line');

$editor->setState(new UpperCaseState());

$editor->type('Second line');
$editor->type('Third line');

$editor->setState(new LowerCaseState());

$editor->type('Fourth line');
$editor->type('Fifth line');

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
```

📒 Template Method
---------------

Real world example
> Suppose we are getting some house built. The steps for building might look like 
> - Prepare the base of house
> - Build the walls
> - Add roof
> - Add other floors
> The order of these steps could never be changed i.e. you can't build the roof before building the walls etc but each of the steps could be modified for example walls can be made of wood or polyester or stone.
  
In plain words
> Template method defines the skeleton of how a certain algorithm could be performed, but defers the implementation of those steps to the children classes.
 
Wikipedia says
> In software engineering, the template method pattern is a behavioral design pattern that defines the program skeleton of an algorithm in an operation, deferring some steps to subclasses. It lets one redefine certain steps of an algorithm without changing the algorithm's structure.

**Programmatic Example**

Imagine we have a build tool that helps us test, lint, build, generate build reports (i.e. code coverage reports, linting report etc) and deploy our app on the test server.

First of all we have our base class that specifies the skeleton for the build algorithm
```php
abstract class Builder {
    
    // Template method 
    public final function build() {
        $this->test();
        $this->lint();
        $this->assemble();
        $this->deploy();
    }
    
    public abstract function test();
    public abstract function lint();
    public abstract function build();
    public abstract function deploy();
}
```

Then we can have our implementations

```php
class AndroidBuilder extends Builder {
    public function test() {
        echo 'Running android tests';
    }
    
    public function lint() {
        echo 'Linting the android code';
    }
    
    public function assemble() {
        echo 'Assembling the android build';
    }
    
    public function deploy() {
        echo 'Deploying android build to server';
    }
}

class IosBuilder extends Builder {
    public function test() {
        echo 'Running ios tests';
    }
    
    public function lint() {
        echo 'Linting the ios code';
    }
    
    public function assemble() {
        echo 'Assembling the ios build';
    }
    
    public function deploy() {
        echo 'Deploying ios build to server';
    }
}
```
And then it can be used as

```php
$androidBuilder = new AndroidBuilder();
$androidBuilder->build();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

$iosBuilder = new IosBuilder();
$iosBuilder->build();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
```

## 🚦 Wrap Up Folks

And that about wraps it up. I will continue to improve this, so you might want to watch/star this repository to revisit. Also, I have plans on writing the same about the architectural patterns, stay tuned for it.

## 👬 Contribution

- Report issues
- Open pull request with improvements
- Spread the word
- Reach out to me directly at kamranahmed.se@gmail.com or [@kamranahmedse](http://twitter.com/kamranahmedse)

## License
MIT © [Kamran Ahmed](http://kamranahmed.info)
