%title: Object Oriented Programming - A Tour
%author: Michael Phillips <michaeljoelphillips@gmail.com>
%date: 2018-04-18





```
   ____  _     _           _                                        
  / __ \| |   (_)         | |                                       
 | |  | | |__  _  ___  ___| |_                                      
 | |  | | '_ \| |/ _ \/ __| __|                                     
 | |__| | |_) | |  __| (__| |_                                      
  \____/|_.__/| |\___|\___|\__|                                     
             _/ |                                                   
   ____     |__/           _           _                            
  / __ \     (_)          | |         | |                           
 | |  | |_ __ _  ___ _ __ | |_ ___  __| |                           
 | |  | | '__| |/ _ | '_ \| __/ _ \/ _` |                           
 | |__| | |  | |  __| | | | ||  __| (_| |                           
  _____/|_|  |_|\___|_| |_|\__\___|\__,_|             _             
 |  __ \                                             (_)            
 | |__) _ __ ___   __ _ _ __ __ _ _ __ ___  _ __ ___  _ _ __   __ _ 
 |  ___| '__/ _ \ / _` | '__/ _` | '_ ` _ \| '_ ` _ \| | '_ \ / _` |
 | |   | | | (_) | (_| | | | (_| | | | | | | | | | | | | | | | (_| |
 |_|   |_|  \___/ \__, |_|  \__,_|_| |_| |_|_| |_| |_|_|_| |_|\__, |
                   __/ |                                       __/ |
                  |___/                                       |___/ 

```
---

-> # What is OOP? <-
^

* OOP is a Programming Paradigm where code is structured using classes to group data and functions together.
^
* Instances of Classes are called Objects.  Objects expose their behavior via methods.
^
* Everyone attending this talk has worked with classes and objects in PHP.

---

-> # Features of Object Oriented Programming <-
^

## Encapsulation / Abstraction
^
Hide the inner details of a class from the outside world.
^

## Polymorphism
^
Use objects of the same type interchangeably.
^

## Inheritance
^
Share behavior and properties of a class with it's children.


---

-> # Encapsulation <-

-> Hide the inner details of a class from the outside world. <-

---

-> # Encapsulation Example <-

```
class Api
{
    const PROD_URL = 'https://service.com';
    const DEV_URL = 'https://dev.service.com';
 
    public $env;
    public $username;
    public $password;
 
    public post(array $data) : void
    {
        $client = $this->auth($this->username, $this->password);
 
        if ($this->env == 'prod') {
            $client->send(self::PROD_URL, $data);
 
            return;
        }
 
        $client->send(self::DEV_URL, $data);
    }
}
```

---

-> # Encapsulation Example <-

```
public function formAction(Request $request) {
    $api->env = 'prod';
    $api->post($request->all());
 
    return new Response();
}
```
^

## What's the issue with this code?
^
* By making `$env` public, we allow it to be modified by the outside world.
^
    * Changing the environment isn't a desired behavior of our application.
^
* We're exposing more about the object than necessary for it to do what we want.
^
    * A client of this object does not need to know about `$username`, `$env`, or `$password`,
    so why do we expose it?

---

-> # Encapsulation <-

```
class Api
{
    // Make our public properties private.
    private $env;
    private $username;
    private $password;
 
    ...
}
```
^

By making our instance variables private, outside code cannot modify
or interfere with the implementation details of the object.
^

We can no longer access the `$env` property.

```
public function formAction(Request $request) {
    $api->env = 'prod'; // Cannot access private property
    $api->post($request->all());
 
    return new Response();
}
```

---

-> # Another Example <-

```
public class Person
{
    private $contactInfo;
 
    public function getFullName() : string
    {
        return $this->contactInfo->getFullName();
    }
}
```

```
public class ContactInfo
{
    private $firstName;
    private $lastName;
 
    public function getFullName() : string
    {
        return sprintf('%s %s', $this->firstName, $this->lastName);
    }
}
```
^

* The Person's full name is encapsulated by `$contactInfo`.
* Person never knows how ContactInfo stores the full name.
* We can refactor the internals of ContactInfo without breaking compatibility with Person.

---

-> # Polymorphism <-

-> Use objects of the same type interchangeably. <-

---

-> # Polymorphism Example <-

```
abstract class Phone
{
    abstract public function call(string $phoneNumber) : void;
}
```

```
class CellPhone extends Phone
{
    private $radio;
 
    public function call(string $phoneNumber) : void
    {
        $connection = $this->radio->prepareConnection();
 
        $connection->dial($phoneNumber);
    }
}
```

```
class WiredPhone extends Phone
{
    public function call(string $phoneNumber) : void
    {
        $this->dial($phoneNumber);
    }
}
```

---

-> # Polymorphism Example <-

```
class Person
{
    private $phone;
    private $phoneNumber;
 
    public function __construct(Phone $phone, string $phoneNumber)
    {
        $this->phone = $phone;
        $this->phoneNumber = $phoneNumber;
    }
 
    public function call(Person $person)
    {
        $this->phone->call($person->getPhoneNumber());
    }
 
    public function getPhoneNumber() : string
    {
        return $this->phoneNumber;
    }
}
```

---

-> # Polymorphism via Interfaces <-

-> Polymorphism can also be acheived via interfaces. <-

```
interface PhoneInterface
{
    public function call(string $phoneNumber) : void;
}
```

```
class Person
{
    private $phone;
    private $phoneNumber;
 
    public function __construct(PhoneInterface $phone, string $phoneNumber)
    {
        $this->phone = $phone;
        $this->phoneNumber = $phoneNumber;
    }
}
```

---

-> # Polymorphism via Interfaces <-

```
public class Modem implements PhoneInterface
{
    public function call(string $phoneNumber) : void
    {
        $this->prepareConnection();
        $this->dial($phoneNumber);
    }
}
```

* An instance of Modem can be used anywhere an instance of PhoneInterface can be used.
* Modem does not have a super class, but Modem is still considered to be of type `PhoneInterface`.

---

-> # Inheritance <-

-> Share behavior and properties of a class with it's children. <-

---

-> # Inheritance Example <-

```
public class Person
{
    protected $contactInfo;
    protected $age;
    protected $weight;
    protected $height;
}
```

```
public class Employee extends Person
{
    protected $salary;
    protected $position;
}
```
^

* Employee inherits all the behavior and instance variables from Person.
^
    * Food for thought: if Employee has access to the inner workings of Person,
    does this break encapsulation?
    ^
    * What about if this were a more complex example?

---

-> # Inheritance <-

-> Inheritance can be used to deduplicate code. <-

---

```
abstract class Payload
{
    private $data;
 
    public function getData() : string
    {
        return $this->data;
    }
 
    public function setData(array $data) : array
    {
        $this->data = $this->formatData($data);
    }
 
    abstract protected function formatData(array $data) : string;
}
```

```
class JsonPayload extends Payload
{
    protected function formatData(array $data) : string
    {
        return json_encode($data);
    }
}
```

```
class XmlPayload extends Payload
{
    protected function formatData(array $data) : string
    {
        // Format to XML
    }
}
```

---

-> # Inheritance <-

* JsonPayload and XmlPayload both implement the `formatData` method,
but inherit all other behavior from Payload.
* Encapsulation is not broken, because neither JsonPayload or XmlPayload
have access to `$data`.
* This relationship is Polymorphic because the two types are interchangeable
with Payload.

---

-> Finished <-
