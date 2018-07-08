# cpp_designpatterns

These notes are based upon D. Pluralight

# overview

* creational patterns (builder, factory, prototype, singleton)
* 
* 
* 

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



### Liskov Substitution Principle

### Interface Segregatoin Principle

### Dependency Inversion Principle
