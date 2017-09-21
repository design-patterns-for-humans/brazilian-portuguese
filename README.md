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
Na realidade, são guias de como atacar certos problemas em certas situações.

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
> Considere que você está construindo uma casa e que precise de portas. Seria uma bagunça se cada vez que você precisar de uma porta, você tenha que colocar suas roupas de carpinteiro e começar a cria-la em sua casa. Em vez disso você a obtém feita por uma fábrica.

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
> Considere o caso de um gerente de contratação. É impossível para apenas uma pessoa entrevistar candidatos para cada uma das posições. Com base na abertura do trabalho, ela tem que decidir e delegar as etapas da entrevista para diferentes pessoas.

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

Exemplo do mundo real
> Extenderemos o nosso exemplo sobre portas, visto no Simple Factory. Baseado em sua necessidade você pode obter uma porta de madeira de uma loja que vende portas de madeira, umaporta de ferro de uma loja que vende portas de ferro ou uma porta de PVC, se for a uma loja que venda porta deste tipo. Além disto, você precisará de uma pessoa com tipos diferentes de especializações para preparar cada porta.Por exemplo;um carpinteiro para portas de madeira, soldador para portas de ferro e etc. Como pode perceber, existem dependências entre portas, portas de madeira com carpinteiros e portas de ferro com soldador.


Resumindo
> Uma Factory de Factorues; uma factory que agrupa outras factories com o seu relacionado/dependente sem especificar sua classe concreta.

  
Wikipedia diz
> O Abstract Factory Pattern provê uma maneira de encapsular um grupo de Factories que tem um tema em comum sem especificar suas classes concretas. 

 **Exemplo Programático**

Traduzindo o acima sobre as portas. Primeiro, temos nossa interface `Door` e algumas implementações dela.

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
Então, nós temos alguns especialistas em montagem para cada tipo de porta.

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
Agora, nós temos uma Factory abstrata que poderá nos deixa criar objetos relacionados a uma mesma família, por exemplo; uma Factory de portas de madeira criará uma porta de madeira e um especialista em montagem de portas de mandeira e a Factory de portas de metal criará uma porta de metal com um especialista em montagem de portas de metal.

```csharp
interface IDoorFactory
{
    IDoor MakeDoor();
    IDoorFittingExpert MakeFittingExpert();
}

// Factory de portas de madeira retorna carpinteiro e porta de madeira
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

// Factory de portas de ferro retornará porta de ferro e um soldador
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
E poderá ser usado desta maneira
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

Como pode reparar, a Factory de porta de madeira encapsula o `carpinteiro` e a `porta de madeira` e a Factory de porta de ferro encapsula o `soldador` e a `porta de ferro`. E com isto nos ajuda a ter certeza que para cada porta criada nós não tenhamos um especialista em montagem errado.


**Quando usar?**

Quado existir uma interralação de dependencia com uma lógica de criação não tão simples.

👷 Builder
--------------------------------------------
Exemplo do mundo real
> Imagine que você está no McDonald's e pede uma oferta em específico, diríamos, "Big Mac" e eles te entregam o 
sanduíche *sem nenhuma pergunta*; isto é um exemplo de uma simples fábrica.
Mas existem alguns casos em que a lógica de criação pode envolver mais passos.
Por exemplo, você quer uma oferta do Subway personalizada, você tem várias opções em como seu sanduíche é feito,
por exemplo, que pão você quer? Quais tipos de molho você quer? Qual queijo? etc.
Nesses casos o padrão builder vem ao resgate.

Resumindo
> Permite que você crie diferentes variedades de um objeto enquanto evita poluição do construtor.
Útil quando poderia haver diversas variedades de um objeto.
Ou quando há muitos passos envolvida na criação de um objeto.

Wikipedia diz
> O padrão Builder é um design pattern de criação com a intenção de encontrar a solução 
anti-pattern "constructor telescoping".

Tendo dito isto, permita-me adicionar um pouco sobre o que é o anti-pattern "tesleslcoping constructor".
Em um momento ou outro todos nós já vimos um construtor como este abaixo:
 
```csharp
public Burger(size, cheese = true, pepperoni = true, tomato = false, lettuce = true)
{
}
```

Como você pode notar; o número de parâmetros do construtor pode sair de controle rapidamente e pode ficar
complicado entender a ordem dos parâmetros. E mais, esta lista de parâmetros poderia continuar crescendo caso
queira adicionar mais opções no futuro. Isto é chamado de anti-pattern "telescoping constructor".


**Exemplo Programático**

A alternativa sensata é utilizar o padrão Builder. Antes de mais nada temos o sanduíche que queremos fazer

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

E então temos o builder


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
E então pode ser utilizado como:

```csharp
var burger = new BurgerBuilder(14)
		.AddPepperoni()
		.AddLettuce()
		.AddTomato()
		.Build();
```

**Quando utilizar?**

Quando poderia haver diversas variedades de um objeto e para evitar "constructor telescoping".
A diferença chave para o padrão factory é que; o padrão factory é utilizado quando o processo de criação é feito num 
único passo, enquanto o padrão builder é utilizado quando a criação é um processo que envolvem vários passos.

🐑 Prototype
------------
Exemplo do mundo real

> Lembra da dolly? A ovelha que foi clonada! Não vamos entrar nos detalhes mas o ponto chave aqui é que é tudo sobre
clonagem.

Resumindo
> Criar objetos baseado num existente através de clonagem.


Wikipedia diz
> O padrão prototype é um padrão criacional no desenvolvimento de software.
Ele é utilizado quando o tipo do objeto a ser criado é determinada por uma instancia prototipada, a qual é clonada
para produzir novos objetos.

Ou seja, ela te permite criar uma cópia de um objeto existente e modificá-lo de acordo com suas necessidades,
ao invés de ter que passar por toda dificuldade de criar um objeto do zero e configurá-lo.

**Exemplo programático**

Em C#, isto pode ser facilmente feito utilizando `ICloneable`
  
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
Então ele pode ser clonado assim
```csharp
var original = new Sheep('Jolly', 'Mountain Sheep');
Console.WriteLine(original.Name); // Jolly
Console.WriteLine(original.Category); // Mountain Sheep

// Clone and modify what is required
var cloned = original.Clone();
cloned.Name = "Dolly";
Console.WriteLine(cloned.Name); // Dolly
Console.WriteLine(cloned.Category); // Mountain Sheep
```

Você também poderia utilizar o método mágico `Clone` e modificar o comportamente de clonagem.

**Quando utilizar?**

Quando um objeto requer que seja similar a um objeto similar existente ou quando a criação seria cara demais
se comparada com a clonagem.


💍 Singleton
------------
Exemplo do mundo real
> Só pode haver um presidente de um pais por vez. O mesmo presidente entra em ação quando o dever chama.
Presidente aqui é um singleton.

Resumindo
> Garante que apenas um objeto de uma determinada classe será criado.

Wikpedia diz
> Em engenharia de software, o padrão singleton é um padrão de projeto que restringe a instanciação de uma classe
para um único objeto. Isto é útil quando exatamente um objeto é necessário para coordenar ações por todo o sistema.

O padrão singleton na realidade é considerado um anti-pattern e deve-se evitar usá-lo demais.
Não é necessariamente ruim e pode haver alguns casos válidos mas deve ser utilizado com cuiidado pois introduz
um estado global à sua aplicação e mudanças em um lugar pode ocasionar efeitos em outras areas e pode acabar
se tornando bem difícil de debugar.
A outra coisa ruim sobre ele é que torna seu código fortemente acoplado, em mais, mockar um singleton pode ser difícil.

**Exemplo Programático**

Para criar um singleton, faça o construtor privado, desabilite clonagem, desabilite extensão e crie uma variável estática para instancias de casa.

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
Então para utilizar
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

Exemplo do mundo real
> Toda organização é composta por empregados. Cada um dos empregados possuirá as mesmas caracteristicas, tais como salário, possui responsabilidades, pode ou não ter que se reportar a algém ou pode ou não ter subordinados.

Resumindo
> Composite Pattern deixa o cliente tratar objetos individuais de maneira uniforme.

Wikipedia diz
> Em engenharia de software, o Composite Patter é um design pattern de particionamento. O Composite Pattern descreve que um grupo de objetos deverá ser tratado da mesma maneira que apenas uma instancia de um objeto. A intensão de um Composite é "compor" objetos em estrutura de arvore representando parte de uma hierarquia. Implementar o Composite Patter é permitir que clientes tratem objetos individuais e composições de maneira uniforme.

**Exemplo programático**

Usaremos nosso exemplo acima com funcionários. Temos diferentes tipos de funcionários.

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

Então, temos uma organização que é composta por inumero tipos diferentes de funcionários.

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

E ele pode ser usado como.

```csharp
// Prepara os funcionários 
var john = new Developer("John Doe", 12000);
var jane = new Designer("Jane", 10000);

// Adicione-os à organização
organization = new Organization();
organization.AddEmployee(john);
organization-.AddEmployee(jane);

Console.WriteLine("Net salaries: " + organization.GetNetSalaries()); // Net Salaries: 22000
```

☕ Decorator
-------------

Exemplo do mundo real

> Imagine você gerenciando uma oficina de carro com muitos serviços. Agora, como você calcularia a conta a ser cobrada? Você escolhe um serviço e começa a adicionar a este serviço preços por demais serviços prestados, de maneira dinâmica, até você ter o custo total final. Aqui que cada um destes serviços seria um decorator.

Resumindo
> Decorator Pattern permite que você altere o comportamento de um objeto em tempo de execução, envolvendo-o por um objeto de uma classe Decorator. 

Wikipedia diz
> Em programação orientada a objeto, o Decorator Pattern permite que se adicione comportamentos para um objeto, tanto de maneira estatica quanto dinâmica, sem afetar o comportamento de outros objetos da mesma classe. O Decorator Pattern é muito útil para ser aderente ao principio de responsabilidade única, uma vez que ele permite que funcionalidades sejam divididas entre classes que compartilhe uma única preocupação.

**Exemplo programático**

Vamos pegar o café como exemplo. Primero de tudo, nós temos um simples café implementando a interface café.
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
Nós queremos deixar o código extensivel para permitir opções de modificação caso seja necessário. Vamos fazer alguns complementos(Decorators).
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
Vamos fazer um café agora!
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

Exemplo do mundo real
> Como voce liga seu compudator? "Aperto o botão ligar", você diz! Isto é o que você pensa, já que está utilizando uma simples interface exposta pelo computador. Internamente ele faz muitas coisas para que o "ligar" ocorra. Esta interface simples para um subsistema complexo é o Facade.

Resumindo
> O Pattern Facade provê uma interface simples para um subsistema complexo.

Wikipedia diz
> Uma Facade é um objeto que provê uma interface simples para uma grande porção de código como uma Class Library.

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

Exemplo do mundo real
> Você já tomou chá fresco de alguma barraca? Eles geralmente fazem mais que um copo que você solicitou e guardam o que restou para servir a outro cliente que também solicitar, assim economizam recursos, como por exemplo gás. Flyweight Patter é sobre isto: compartilhar.


Resumindo
> Ele é usado para minimizar o uso de memoria ou gasto computacional, compartilhando o máximo que der com objetos semelhantes.


O Wikipedia diz
> Em programa de computadores, Flyweight é um padrão de design de software. Um Flyweight Pattern é um objeto que minimiza o uso de memória, compartilhando o máximo possível de indormações com objetos similares; Esta é uma maneira de usar objetos em larga escala quando uma simples representação repedida poderia usar uma iniceitavel quantidade de memória.

**Exemplo programatico**
Traduzindo o nosso exemplo acima sobre o chá. Primeiro de tudo, temos tipos de chá e preparadores de chá.

```csharp
// Quanquer coisa que for cacheada é Flyweight
// Tipos de chá será Flyweight
class KarakTea
{
}

// Atua como uma factory e salva os chás
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
Então, nós temos o 'TeaShop' o qual pega os pedidos e os serve
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
E isto pode ser usado como no caso a baixo

```csharp
var teaMaker = new TeaMaker();
var shop = new TeaShop(teaMaker);

shop.TakeOrder("less sugar", 1);
shop.TakeOrder("more milk", 2);
shop.TakeOrder("without sugar", 5);

shop.Serve();
// Servindo chá para a mesa #1
// Servindo chá para a mesa #2
// Servindo chá para a mesa #5
```

🎱 Proxy
-------------------
Exemplo do mundo real 
> Você já utilizou um cartão de acesso para passar por uma porta? Existem muitas opções para abrir uma porta, por exemplo, você poderia tanto usar um cartão de aecsso quanto apertar um botão para passar por sua segurança. A função principla desta porta é abrir, porém existe um proxy antes disto que adiciona outras funcionalidades. Deixe-me explicar melhor isto usando o exemplo em código a seguir.

Resumindo
> Usando o Proxy Patter, uma classe irá representar a funcionalidade de outra classe.

Wikipedia diz
> Um Proxy, de forma geral, é uma classe funcionando como uma interface para outra coisa. Um Proxy é um envolucro (wrapper) ou Agent Object que é chamado pelo cliente para acessar o objeto real por traz da cena. O uso de Proxy pode ser simplesmente para repassar para um objeto real ou então adicionar lógica a ele. Na lógica adicional que o Proxy pode adicionar temos por exemplo cache para operações que consomem muitos recursos.

**Exemplo Programatico**
Tendo o exempo da porta a cima, primeiramnete precisaremos criar a interface de porta e implementa-la.

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
Então, nós temos um Proxy para adicionar segurança a porta que quisermos.
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
E assim é como ele pode ser utilizado
```csharp
var door = new Security(new LabDoor());
door.Open("invalid"); // Big no! It ain't possible.

door.Open("$ecr@t"); // Opening lab door
door.Close(); // Closing lab door
```
Outro exemplo que podemos ter com este Patter é a implementação de data-mapper. Por exemplo, recentemente criei um ODM (Object Data Mapper) para MopngoD usando este Pattern, onde escrevi um Proxy sobre as camadas de classes que utiizassem o comando mágico `__call()`. Todas as chamadas de métodos passam pelo Proxy e depois pelo original do Mongo e o resultado retornado é o mesmo para todos eles, menos para os metodos `find()`e `findOne()`, onde os dados foram mapeados para os seus respectivos objetos antes de serem retornados. 


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

## 🚦 Envolva-se 

E isto é sobre envolver-se. Irei continuar a melhorar este arquivo, então é possível que você queira clicar em watch/star deste repositório para reve-lo no futuro. A, também tenho planos para escrever um repositório semelhante sobre Padrões de Arquitetura (archtectural patterns), fique ligado!

## 👬 Contribuição
- Reporte issues
- Abra um pull request com melhorias
- Espelhe aos amigos

Este repositório foi do inglês traduzido [daqui](https://github.com/kamranahmedse/design-patterns-for-humans)
e você pode encontrar o autor original em kamranahmed.se@gmail.com ou [@kamranahmedse](http://twitter.com/kamranahmedse)

## Licensa
MIT © [Kamran Ahmed](http://kamranahmed.info)
