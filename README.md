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

Tipos de Design Patterns
-----------------

* [De Criação](#design-patterns-de-criação)
* [Estruturais](#design-patterns-estruturais)
* [Comportamentais](#design-patterns-comportamentais)


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

Ao criar um objeto que envolve alguma lógica e não apenas algumas atribuições, faz sentido colocá-lo em uma factory dedicada em vez de repetir o mesmo código em todos os lugares.

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

**Exemplo programatico**
Usando o exemplo sobre o computador acima, vamos criar uma classe para representa-la.

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
Aqui temos uma Facade
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
Como usar a classe Facade.
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

Exemplo do mundo real
> Por exemplo, você possui três métodos de pagamento (`A`, `B` e `C`) configurados em sua conta. Cada um destes com uma quantia diferente; em `A` você tem R$100, Em `B` você tem R$300, Em `C` você tem R$1000, e a preferência para pagamentos é `A` ou então `B` ou então `C`. Você tenta comprar algo que custe R$210. Usando a Chain of Responsibility, primeiramente a conta `A` é verificada para saber se pode comprar, se sim a compra é feita e a cadeia é quebrada, caso contrario, a verificação continual até encontrar uma que seja possível utilizar. Aqui `A`, `B` e `C` são as ligações da cadeia e todo o fenomeno é a Chain of Responsibility.

Resumindo
> Este Pattern ajuda a criar uma cadeia de objetos. Requer entradas de uma saída e lê objeto por objeto até encontrar um resultado adequado.

Wikipedia diz
> Em orientação a objeto, o Pattern Chain of Responsibility é um desing que consiste de um fonte com objetos de comandos e uma série de objetos de processamento. Cada objeto de processamento contem uma lógica que define objetos de comando que ele pode realizar; O demais é passado para o proximo objeto da cadeia.

**Exemplo programático**
Traduzindo noss exemplo a cima sobre contas. Primeiramente, temos uma conta base mantendo a lógica para a cadeia de contas juntas e algumas contas.

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

Agora, vamos preparar a cadeia usando os linques definidos a cima (exemplo: Banco, Paypal e Bitcoin)

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

Exemplo do mundo real
> Um exemplo genérico seria você pedindo comida em um restaurante. Você (i.e. `Client`) pede ao garçon (i.e. `Invoker`) trazer comida (i.e. `Command`) e o garçon simplesmente leva seu pedido ao Chef (i.e. `Receiver`) que tem o conhecimento de o que e como cozinhar. 
> Outro exemplo seria você (i.e. `Client`) ligando (i.e. `Command`) a televisão (i.e. `Receiver`) usando um controle remoto (`Invoker`).

Resumindo
> O padrão Command permite que você encapsule ações em objetos. A ideia principal por trás desse padrão é prover os meios de desacoplar o cliente do receptor.

A Wikipedia diz
> Na programação orientada a objeto, o padrão Command é um padrão de projeto comportamental no qual um objeto é usado para encapsular toda informação necessária para executar uma ação ou ativar um evento em um momento posterior. Esta informação inclui o nome do método, o objeto que possui o método e os valores para os parâmetros do método.

**Exemplo Programático**

Primeiramente, temos o receptor que tem a implementação de todas as ações que podem ser executadas.
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
Então temos uma interface que cada um dos comandos vai implementar e em seguida, temos um conjunto de comandos.
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
Então temos um `Invoker` com o qual o `Client` vai interagir para processar qualquer comando.
```php
// Invoker
class RemoteControl {
    
    public function submit(Command $command) {
        $command->execute();
    }
}
```
Finalmente, vamos ver como podemos usá-lo com o nosso `Client`
```php
$bulb = new Bulb();

$turnOn = new TurnOn($bulb);
$turnOff = new TurnOff($bulb);

$remote = new RemoteControl();
$remoteControl->submit($turnOn); // Bulb has been lit!
$remoteControl->submit($turnOff); // Darkness!
```

O padrão Command também pode ser utilizado para implementar um sistema baseado em transações onde você continua mantendo o histórico de comandos assim que você os executa. Se o comando final é executado com sucesso, tudo bem. Caso contrário, simplesmente itere sobre pelo histórico e execute o comando `undo` em todos os comandos executados. 

➿ Iterator
--------

Exemplo do mundo real
> Um antigo aparelho de radio será um bom exemplo para iterador, onde o usuário começa em alguma estação e então usa os botões próximo ou anterior para navegar entre as demais. Ou use o exemplo de um MP3 player ou uma TV, onde você pode apertar os botões próximo e anterior para percorrer os canais em sequência, em outras palavras, todos eles fornecem uma interface para iterar através dos respectivos canais, músicas ou estações de rádio.
 

Resumindo
> Ele apresenta uma maneira de acessar os elementos de um objeto sem expor a toda a estrutura.

Wikipedia diz
> Na programação orientada a objetos, um iterador se refere tanto ao objeto que permite ao programador percorrer um container, (uma coleção de elementos) particularmente listas,quanto ao design pattern Iterator, no qual um iterador é usado para percorrer um container e acessar seus elementos. O padrão Iterator desacopla os algoritmos dos recipientes, porém em alguns casos, os algoritmos são necessariamente específicos dos containers e, portanto, não podem ser desacoplados.

**Exemplo Programático**
Com PHP é relativamente fácil implementar usando SPL (Biblioteca PHP Padrão). Traduzindo nosso exemplo das estações de rádio para o código abaixo. Primeiramente nós temos a 'EstacaoDeRadio' 


```php
class EstacaoDeRadio { \\classe que representa a estação de rádio
    protected $frequencia;

    public function __construct(float $frequencia) {
        $this->frequencia = $frequencia;    
    }
    
    public function getFrequencia() : float {
        return $this->frequencia;
    }
}
```
Aqui temos nosso iterador

```php
use Countable;
use Iterator;

class EstacaoList implements Countable, Iterator {
    /** @var EstacaoDeRadio[] $estacoes */
    protected $estacoes = [];
    
    /** @var int $contador */
    protected $contador;
    
    public function addEstacao(EstacaoDeRadio $estacao) {
        $this->estacoes[] = $estacao;
    }
    
    public function removeEstacao(EstacaoDeRadio $toRemove) {
        $toRemoveFrequencia = $toRemove->getFrequencia();
        $this->estacoes = array_filter($this->estacoes, function (EstacaoDeRadio $estacao) use ($toRemoveFrequencia) {
            return $estacao->getFrequencia() !== $toRemoveFrequencia;
        });
    }
    
    public function count() : int {
        return count($this->estacoes);
    }
    
    public function current() : EstacaoDeRadio {
        return $this->estacoes[$this->contador];
    }
    
    public function key() {
        return $this->contador;
    }
    
    public function next() {
        $this->contador++;
    }
    
    public function rewind() {
        $this->contador = 0;
    }
    
    public function valid(): bool
    {
        return isset($this->estacoes[$this->contador]);
    }
}
```
E então ele pode ser usado como
```php
$estacaoList = new EstacaoList();

$estacaoList->addEstacao(new Estacao(89));
$estacaoList->addEstacao(new Estacao(101));
$estacaoList->addEstacao(new Estacao(102));
$estacaoList->addEstacao(new Estacao(103.2));

foreach($estacaoList as $estacao) {
    echo $estacao->getFrequencia() . PHP_EOL;
}

$estacaoList->removeEstacao(new Estacao(89)); // Iremos remover a estação 89
```

👽 Mediator
========


Exemplo do mundo real
> Um exemplo comum pode ser quando você fala com alguém em seu telefone celular, neste caso há um provedor de rede situado entre vocês e sua conversa passa através do provedor ao invés de ser enviada diretamente de um celular ao outro. Neste caso, o provedor de rede é mediador.

Resumindo
> O pattern Mediator adiciona um objeto de terceiros (chamado mediador) para controlar a interação entre os dois objetos (chamados colegas). Isso ajuda a reduzir o acoplamento entre as classes que estão comunicando entre si. Pois agora elas não têm a necessidade de conhecer a implementação uma da outra. 

Wikipedia diz
> Na engenharia de software, o pattern mediator define um objeto que encapsula como conjunto de objetos interagem. Este pattern um pattern comportamental (behavioral pattern), pois ele pode alterar o comportamento do programa.

**Exemplo Programático**

Temos aqui um exemplo simples de uma sala de bate-papo (i.e. mediador) com usuários (i.e. colegas) enviando mensagens uns aos outros.

Em primeiro lugar, temos o mediador, isto é, a sala de bate-papo


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

Então nós temos nossos usuários i.e. colegas
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
E seu uso
```php
$mediator = new ChatRoom();

$john = new User('John Doe', $mediator);
$jane = new User('Jane Doe', $mediator);

$john->send('Olá!');
$jane->send('Oi!');

// A saída será
// Feb 14, 10:58 [John]: Olá!
// Feb 14, 10:58 [Jane]: Hey!
```

💾 Memento
-------
Exemplo do mundo real
> Veja o exemplo da calculadora (i.e. originador), onde sempre que você executa um cálculo, o último cálculo é salvo na memória (i.e. memento) assim você pode acessá-lo novamente e talvez retornar a ele pressionando alguns botões (i.e. armazenador).

Resumindo
> O pattern Memento é sobre capturar e armazenar o estado atual de um objeto de uma forma que isso possa ser restaurado futuramente de forma tranquila.

Wikipedia diz
> O pattern Memento é um  design pattern que proporciona a habilidade de restaurar um objeto a seu estado anterior (desfazer por meio de reversão).


Normalmente útil quando você precisa fornecer uma funcionalidade como a de desfazer.

**Exemplo Programático**

Vamos utilizar o exemplo do editor de texto que continua guardando o estado de tempo em tempo e que você pode restaurar se quiser. 

Em primeiro lugar, temos o nosso objeto memento que poderá guardar o estado do editor


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

Então nós temos nosso editor i.e. originador que irá utilizar nosso objeto memento

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

Em seguida o Editor pode ser usado da seguinte forma

```php
$editor = new Editor();

// Escreva alguma coisa
$editor->type('Esta é a primeira sentença.');
$editor->type('Esta é a segunda.');

// Salva o estado, para restaurar para : Esta é a primeira sentença. Esta é a segunda.
$saved = $editor->save();

// Escreva mais alguma coisa
$editor->type('E esta é a terceira.');

// Saída: Conteúdo salvo anteriormente
echo $editor->getContent(); // Esta é a primeira sentença. Esta é a segunda. E esta é a terceira.

// Restaura para o último estado salvo
$editor->restore($saved);

$editor->getContent(); // Esta é a primeira sentença. Esta é a segunda.
```

😎 Observer
--------
Exemplo do mundo real
> Um bom exemplo seriam pessoas que estão buscando emprego e que se inscrevem em algum site de divulgação de vagas e são notificados quando é anunciada uma oferta de emprego correspondente.

Resumindo
> Define uma dependência entre objetos de maneira que quando um objeto altera seu estado, todos seus dependentes são notificados.

Wikipedia diz
> O padrão Observer é um padrão de design de software no qual um objeto, chamado de objeto de interesse, mantêm uma lista de seus dependentes, chamados observadores, e os notifica automaticamente de qualquer alteração no estado, geralmente através de chamadas a um dos seus métodos.

**Exemplo Programático**

Traduzindo nosso exemplo acima. Primeiro, nós temos buscadores de emprego que precisam ser notificados de anúncios de vagas
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
Então nós temos nossa publicação de vagas na qual os que estão buscando emprego vão se inscrever
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
Então pode ser usado como
```php
// Cria buscadores de emprego
$johnDoe = new JobSeeker('John Doe');
$janeDoe = new JobSeeker('Jane Doe');
$kaneDoe = new JobSeeker('Kane Doe');

// Cria publicação de vagas e atribui buscadores
$jobPostings = new JobPostings();
$jobPostings->attach($johnDoe);
$jobPostings->attach($janeDoe);

// Adiciona uma nova vaga e verifica se os buscadores são notificados
$jobPostings->addJob(new JobPost('Software Engineer'));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer
```

🏃 Visitor
-------
Exemplo do mundo real
> Considere alguém visitando Dubai. Eles só precisam de uma maneira (i.e. visto) para entrar em Dubai. Após a chegada, eles podem visitar qualquer lugar de Dubai por conta própria sem ter que pedir permissão ou para caminhar a fim de visitar qualquer lugar aqui; basta saber a respeito do lugar e eles podem visitá-lo. O padrão Visitor deixa você fazer justamente isso, ele ajuda você a adicionar lugares a serem visitados de maneira que eles possam visitar o máximo de locais possíveis sem a necessidade de caminhar para encontrá-los.

Resumindo
> O padrão Visitor permite que você adicione operações adicionais a objetos sem ter que modificá-los.
    
Wikipedia diz
> Na programação orientada a objetos e engenharia de software, o padrão de design visitor é uma maneira de separar um algoritmo da estrutura de um objeto no qual ele opera. Um resultado prático dessa separação é a habilidade de adicionar novas operações a objetos existentes sem ter que modificar suas estruturas. É uma maneira de seguir o princípio de aberto/fechado.

**Exemplo Programático**

Vamos tomar como exemplo uma simulação de zoológico onde nós temos vários tipos diferentes de animais e temos que fazer eles emitirem Sons. Vamos traduzir isso utilizando o padrão visitor

```php
// Visitado
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
Então nós temos nossas implementações para os animais
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
Vamos implementar nosso visitor
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

E pode ser usado como
```php
$monkey = new Monkey();
$lion = new Lion();
$dolphin = new Dolphin();

$speak = new Speak();

$monkey->accept($speak);    // Ooh oo aa aa!    
$lion->accept($speak);      // Roaaar!
$dolphin->accept($speak);   // Tuut tutt tuutt!
```
Nós poderíamos ter feito isso simplesmente tendo uma hierarquia de herança para os animais, mas aí teríamos que modificar os animais sempre que precisássemos adicionar novas ações a eles. Mas agora não precisaremos alterá-los. Por exemplo, vamos supor que nos pediram para adicionar o comportamento de pulo aos animais, nós podíamos adicionar isso criando um novo visitor i.e.

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
E para utilização
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

Exemplo do mundo real
> Considere o exemplo de ordenação, nós implementamos o algoritmo de Bubble Sort mas a quantidade de dados começou a crescer e o algoritmo começou a ficar muito lento. Para contornar o problema nós implementamos o Quick Sort. Embora o algoritmo Quick Sort performe melhor para conjuntos de dados muito grandes, era muito lento para conjuntos menores. Para resolver a situação implementamos uma estratégia na qual para conjuntos de dados pequenos, seja utilizado o Bubble Sort e para conjuntos de dados maiores, utilizado o Quick Sort.

Resumindo
> O padrão de Strategy permite a troca de algoritmo ou estratégia baseado na situação.

Wikipedia diz
> Na programação de computadoes, o padrão Strategy (também conhecido como padrão Policy) é um padrão comportamental de design de software que permite que o comportamento de um algoritmo seja selecionado em tempo de execução.
 
**Exemplo Programático**

Traduzindo nosso exemplo acima. Primeiramente devemos ter nossa interface de Strategy e diferentes implementações de estratégia.

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
 
E temos nosso client, que vai utilizar qualquer uma das estratégias

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

E pode ser usado como
```php
$dataset = [1, 5, 4, 3, 2, 8];

$sorter = new Sorter(new BubbleSortStrategy());
$sorter->sort($dataset); // Output : Sorting using bubble sort

$sorter = new Sorter(new QuickSortStrategy());
$sorter->sort($dataset); // Output : Sorting using quick sort
```

💢 State
-----
Exemplo do mundo real
> Imagine que você está utilizando alguma aplicação gráfica e escolhe um pincel para desenhar. O pincel muda seu comportamento baseado na cor selecionada i.e. se você seleciona a cor vermelha, ele irá desenhar em vermelho, se selecionar azul então será em azul etc.

Resumindo
> Permite que você mude o comportamento de uma classe quando o estado muda.

Wikipedia diz
> O padrão State é um padrão comportamental de design de software que implementa uma máquina de estados de maneira orientada à objetos. Com o padrão State, uma máquina de estados é implementada através da implementação de cada um dos estados individuais como uma classe derivada da interface do padrão State, e implementando transições de estado através de chamadas de métodos definidos na superclasse do padrão.
> O padrão State pode ser interpretado como um padrão de estratégia que é capaz de alterar a estratégia corrente através de invocações de métodos definidos na interface do padrão.

**Exemplo Programático**

Vamos tomar como exemplo um editor de texto, ele permite que você altere o estado do texto que é digitado i.e. se você selecionou negrito, irá começar a escrever em negrito, se selecionou itálico então em itálico etc.

Primeiramente temos nossa interface de estado e algumas implementações de estado.

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
Então nós temos nosso editor
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
Então pode ser usado como
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

Exemplo do mundo real
> Suponha que estejamos construindo uma casa. Os passos para sua construção podem se parecer com
> - Preparar o alicerce da casa
> - Construir os muros
> - Construir um teto
> - Construir outros andares

> A ordem destes passos não pode ser mudada i.e. Você não pode construir um teto antes de construir as paredes e etc mas cada um dos passos pode ser modificado, por exemplo, muros podem ser construídos de madeira, poliéster ou pedra.
  
Resumindo
> O Template method define o esqueleto de como um algoritmo pode ser executado, mas delega a implementação destes passos para a classes filhas.
 
A Wikipedia diz
> Na engenharia de software, o padrão template method é um padrão de projeto comportamental que define o esqueleto de programa de um algoritmo em operação, delegando alguns passos para as subclasses. Ele permite que seja possível redefinir alguns passos do algoritmo sem mudar a estrutura do mesmo.

**Exemplo Programático**

Imagine que tenhamos uma build tool que nos auxilia a testar, fazer análise de código, criar a build, gerar relatórios da build (i.e. relatórios de cobertura, relatórios de análise de código etc) e dar deploy do nosso app no servidor de testes.

Primeiramente, temos nossa classe base que especifica o esqueleto para o algoritmo de build
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

Então, podemos ter nossa implementação

```php
class AndroidBuilder extends Builder {
    public function test() {
        echo 'Rodando testes do android';
    }
    
    public function lint() {
        echo 'Análisando o código do android';
    }
    
    public function assemble() {
        echo 'Montando a build do android';
    }
    
    public function deploy() {
        echo 'Fazendo o deploy da build do android para o servidor';
    }
}

class IosBuilder extends Builder {
    public function test() {
        echo 'Rodando testes do ios';
    }
    
    public function lint() {
        echo 'Análisando o código do ios';
    }
    
    public function assemble() {
        echo 'Montando a build do ios';
    }
    
    public function deploy() {
        echo 'Fazendo o deploy da build do ios para o servidor';
    }
}
```
Que então, podem ser usadas como

```php
$androidBuilder = new AndroidBuilder();
$androidBuilder->build();

// Output:
// Rodando testes do android
// Análisando o código do android
// Montando a build do android
// Fazendo o deploy da build do android para o servidor

$iosBuilder = new IosBuilder();
$iosBuilder->build();

// Output:
// Rodando testes do ios
// Análisando o código do ios
// Montando a build do ios
// Fazendo o deploy da build do ios para o servidor
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
