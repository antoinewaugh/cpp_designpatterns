# cpp_designpatterns

These notes are based upon D. Pluralight

# overview

* creational patterns (builder, factory, prototype, singleton)
* ...
* ...
* ...

# Preliminaries

* SOLID
* Dependency Injection
* Monads Maybe

## SOLID Design Principles

### Single Responsibility Principle

The single responsibility principle does not imply a class should do one thing (thats what methods are for), but rather a class should have just _one reason to change_. 

#### Violating Principle

```
struct Journal {
  std::string title;
  std::vector<std::string> entires;
  
  void add(std::string const&);
  void save(std::string const&); // <---- this breaks single responsibility. journal is not responsible to save itself
}
```

Whenever you decide that the format or medium in which the Journal should be saved the current design requires you to modify the Journal class itself.

#### Conforming Principle

```
struct PersistenceManager {
  static void save(Journal const&, std::string& filename); // better design where responsibilty of persistence has been moved to another class
}
```

### Open-Closed Principle

Very closely related to single responsibility, the open-closed principle suggests a class should be open for extension but closed for modification.

#### Violating Pinciple

The below snippet, requires the ProductFilter class to be extended every time we wish to add a filter. This is problematic as the class is in a constantly changing state, and would not allow new filters to be written by others without having access to modify the class itself.


```
enum class Color {Red, Green, Blue};
enum class Size {Small, Medium, Large};

struct Product {
  std::string name;
  Color color;
  Size size;
}

struct ProductFilter {
  teypdef std::vector<Product*> Items;
  static Items by_color(Items, Color color) {
    Items result;
    for(auto& i: items) 
      if(i->color == color)
        result.push_back(i);
    return result;
  }
  
  static Items by_size(Items, Size size) {
    Items result;
    for(auto& i: items) 
      if(i->size == size)
        result.push_back(i);
    return result;
  }
}
```

#### Conforming Policy

Pattern: Specification Pattern

The specification pattern is used quite a lot with data access. 

The below snippet is not complete, but demonstrates the approach. By having an abstract concept of filter, and specification we can implement any new filter and specifications without modifying the existing classes.

```
template<typename T> struct ISpecification {
  virtual bool is_satisfied(T* item) = 0;
};

template<typename T> struct IFilter {
  virtual std::vector<T*> filter(std::vector<T*> items, ISpecification<T>& spec) = 0;
};

struct BetterFilter: IFilter<Product> {
  std::vector<Product*> filter(std::vector<Product*> items, ISpecification<Product> &spec) override {
    Items result;
    for(auto& p: items)
      if(spec.is_satisfied(p))
        result.push_back(i);
    return result;
  }
};

....

```

### Liskov Substitution Principle

Objects in a program should be replacable with instances of their subtypes (subclasses). More formally, LSP is a particular definition of a subtyping relation, called (strong) behavioral subtyping. The principle ensures that functions taking type _T_ can also accept subtype _S_ and maintain their correctness.

#### Violating Principle

The below demonstrates the invalid design of a square being a subclass of a rectangle. 

```
struct Rectangle {
  virtual SetHeight(int);
  virtual SetWidth(int);
  ...
  int Area() const { const width*height; }
} 

struct Square: Rectangle {
  setWidth(int w) override {
    width = height = w;
  }
  
  setHeight(int h) override {
    height = width = h;
  }
}

void process(Rectangle const& r) {
  int w = r.getWidth();
  r.setHeight(10);
  assert(r.Area() == w * 10); // this invariant is invalidated with respects to a square
}

```

#### Conforming Principle

The factory method can be used to avoid the subclassing in the first place. Rather than having a square class, we use a rectangle factory.

```
struct RectangleFactory {
  static Rectangle CreateRectange(int w, int h);
  static Rectangle CreateSquare(int size);
};
```

### Interface Segregation Principle

No client should be forcedd to depend on methods that it does not use.

Here, we are forcing implementers to conform to a multi-function device when they may just be implementing a printer, or scanner. 

```
#include <vector>
struct Document;

struct IMachine {
  virtual void print(std::vector<Document*> docs) = 0;
  virtual void scan(std::vector<Document*> docs) = 0;
  virtual void fax(std::vector<Document*> docs) = 0;
};

```

The better approach is to break up the responsibilities into separate interfaces;

```
struct IPrinter {
  virtual void print(std::vector<Document*> docs) = 0;
}
struct IScanner {
  virtual void scan(std::vector<Document*> docs) = 0;
}
...
```

Effectively break up a large interface into smaller responsiblities. If you wish to have an interface which exposes all the functionality we can still achieve this by having an interface which derives from the broken up interfaces.

### Dependency Inversion Principle

High level modules should not depend on low level modules. Both should depend on abstractions.

_Example:_ Reporing component should depend on a ConsoleLogger but can depend on an ILogger

Generally: dependencies on interfaces and supertypes, not on concrete types.


