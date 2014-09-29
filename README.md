phpQuery
========

A library for manipulating and traversing XML documents in an easy and intuitive way. This library is inspired by the jQuery library and borrows some interesting ideas, like chaining.

Installation
------------

Download the project:
```bash
git clone https://github.com/soloproyectos/phpquery
```

and copy the `classes` folder in your preferred location (optionally, rename it). Finally, copy and paste the following PHP code:
```PHP
require_once "< YOUR PREFERRED LOCATION >/classes/autoload.php";
use com\soloproyectos\common\dom\DomNode;
```

And that's all. You are ready to use this library.

Methods
-------

#### Create nodes from a given source:
  * `createFromDocument($doc)`: creates an instance from a Document object
  * `createFromElement($element)`: creates an instance from a DOMElement object
  * `createFromString($string)`: creates an instance from a string

#### Basic methods:
  * `elements()`: gets internal DOM elements
  * `name()`: gets the node name
  * `parent()`: gets the parent node or a `null` value
  * `query(cssSelectors)`: finds nodes using CSS selectors
  * `xpath(expression)`: finds nodes using XPath expressions
  * `remove()`: removes the node from the document
  * `data($name, [$value])`: gets or sets arbitrary data
  * `append($string)`: appends inner XML text
  * `prepend($string)`: prepends inner XML text
  * `clear()`: removes all child nodes
  * `html([$string])`: gets or sets inner XML text
  * `text([$string])`: gets or sets inner text

#### Attributes:
  * `attr($name, [$value])`: gets or sets an attribute
  * `hasAttr($name)`: checks if a node has an attribute

#### CSS attributes:
  * `css($name, [$value])`: gets or sets a CSS attribute
  * `hasCss($name)`: checks if a node has a CSS attribute

#### Classes:
  * `addClass($className)`: adds a class to the node
  * `hasClass($className)`: checks if a node has a class
  * `removeClass($className)`: removes a class from the node

Basic Examples
--------------

#### Create an instance

Create a simple node:
```PHP
// creates a simple node with two attributes and inner text
$item = new DomNode("item", array("id" => 101, "title" => "Title 101"), "Inner text here...");
echo $item;
```

Create a complex node:
```PHP
// in this case we use a callback function to add complex structures into the node
$root = new DomNode("root", function ($target) {
    // adds three subnodes
    for ($i = 0; $i < 3; $i++) {
        $target->append(new DomNode("item", array("id" => $i, "title" => "Title $i"), "This is the item $i"));
    }
    
    // appends some XML code
    $target->append("<text>This text is added to the end.</text>");
    
    // prepends some XML code
    $target->prepend("<text>This text is added to the beginning</text>");
});
echo $root;
```

#### Create an instance from a given source:

```PHP
// creates an instance from a string
$xml = DomNode::createFromString('<root><item id="101" /><item id="102" /><item id="103" /></root>');

// creates an instance from a document
$doc = new DOMDocument("1.0", "UTF-8");
$doc->loadXML('<root><item id="101" /><item id="102" /><item id="103" /></root>');
$xml = DomNode::createFromDocument($doc);

// creates an instance from a given DOMElement
// $element is a DOMElement object
$xml = DomNode::createFromElement($element);
```

#### Use the `query` method

You can use the same `query` function to retrieve either single or multiple nodes.

```PHP
$xml = DomNode::createFromString('<root><item id="101" /><item id="102" /><item id="103" /></root>');

// selects and prints all items
$items = $xml->query("item");
foreach ($items as $item) {
    echo $item . "\n";
}

// select and prints a single item
$item = $xml->query("item[id = 102]");
echo $item;
```

#### Use the `attr`, `text` and `html` methods:
```PHP
$xml = DomNode::createFromString(file_get_contents("test.xml"));

// prints books info
$books = $xml->query("books item");
foreach ($books as $book) {
    echo "Title: " . $book->attr("title") . "\n";
    echo "Author: " . $book->attr("author_id") . "\n";
    echo "ISBN: " . $book->query("isbn")->text() . "\n";
    echo "Available: " . $book->query("available")->text() . "\n";
    echo "Description: " . $book->query("description")->text() . "\n";
    echo "---\n";
}

// gets the number of items
echo "Number of items: " . count($books);

// prints inner XML text
$genres = $xml->query("genres");
echo $genres->html();
```

#### Use the `attr`, `text` and `html` methods to change contents:

In the previous example we used `attr`, `text` and `html` for getting contents. In this example we are use the same methods to change the document.

```PHP
$xml = DomNode::createFromString('<root><item id="101" /><item id="102" /><item id="103" /></root>');

// changes or adds attributes and inner texts
$item = $xml->query("item[id = 102]");
$item->attr("id", 666);
$item->attr("title", "Item 666");
$item->text("I'm an inner text");
echo $item;

// changes inner contents
$item = $xml->query("item[id = 103]");
$item->html('<subitem>I am a subitem</subitem>');
echo $item;
```

#### Use `prepend` and `append` methods:

```PHP
$xml = DomNode::createFromString('<root><item id="101" /><item id="102" /><item id="103" /></root>');

// appends contents
$item = $xml->query("item[id = 102]");
$item->append('<subitem id="102.1" title="Subitem title">This text goes to the end...</subitem>');
echo $xml;

// appends a DomNode object
$item->append(new DomNode("subitem", array("id" => "102.1", "title" => "Subitem title"), "Some inner text here ..."));
echo $xml;

// appends a DomNode object and calls the `callback` function
$item->prepend(new DomNode("subitem", array("id" => "102.2", "title" => "Subitem title"), function ($target) {
    $target->text("I'm the first child node ...");
}));
echo $xml;
```

#### Use the `remove` and `clear` methods:

```PHP
$xml = DomNode::createFromString('<root><item id="101" /><item id="102" /><item id="103" /></root>');

// removes a single item
$item = $xml->query("item[id = 103]");
$item->remove();
echo $xml;

// removes a list of items
$items = $xml->query("item:even");
$items->remove();
echo $xml;

// removes all chid nodes
$xml->clear();
echo $xml;
```

#### Chaining

You can concatenate multiple methods in the same line:

```PHP
$xml = DomNode::createFromString('<root><item id="101" /><item id="102" /><item id="103" /></root>');

// changes and prints the node in the same line
echo $xml->query("item[id = 102]")->attr("title", "Item 101")->text("Some text...")->append("<subitem />");
```
