---
layout: post
title:  "你好 javascript"
categories: jekyll update
tags: 没事多看看 加点新的吧
---

##String
### " and '
```javascript
var name = 'tangkikodo';
var statment = "this is kikodo's string";
var statment2 = 'He is a "freak"';
var statment3 = "escape here: \"this is kikodo\' string\""
```
###escape
```javascript
var path = "C:\\folder\\myfile.c";
var multiline = "this is a long\
string";
var multiline2 = "this is line1\nthis is line2\n";
```
###join
```javascript
var part1 = "hello ";
var part2 = "world";
var all = part1 + part2;
```
###string method
```javascript
var length = "hello world".length;
var index = "hello world".indexOf("world"); //World-> -1
console.log("hello".charAt(2));
console.log("hello".substr(1, 2)); //start, length
console.log("HELLO".toLowerCase()); //origin str do not change
console.log("HELLO".toUpperCase()); //origin str do not change
```
###string compares
```javascript
var first = "hello";
var second = "Hello";
// == will change type
if (first.toLowerCase() === second.toLowerCase()) {  
	console.log("the strings are equal");
} else {
	console.log("the strings are different");
}
function compare(a, b) {
	console.log(a + '<' + b , a < b);
}
compare('a', 'b'); // true
compare('a', 'A'); // false 0x41, 0x31
compare('apple', 'orange'); // compare one by one. and substr lose
compare('app', 'app') //false ,if <= will be equal
```

##Numbers
### only one type of number
```javascript
var a = 11,
	b = -123;
var c = 1.5,
	d = 123.4321;
var e = 0.1,
	f = 0.2;
var result = e * f; 
//0.0200000000000000004 , near but not..
```
###advance 
```javascript
var g = 10E6;
var h = 1.23E6;
var i = 012; // oct 12 -> 10
var ii = 019; // auto change to decimal..
var j = 0x11; //heximal
```

##convert between string and number
###convert to whole number
```javascript
var j = parseInt("197");
var jj = parseInt("012");
var jjj = parseInt("019"); // -> 1
//recommand way
var k = parseInt("019", 10); // -> 19
var l = parseInt("0100110", 2);
var m = parseInt("0100110", 3);
var n = parseInt("23 people", 10); //as much as it could

var o = parseFloat("123.45");
```
###NaN
```javascript
var l = NaN;//false  
l == l;		//false 
l === l;    //false
l === NaN;  //false
//use is NaN()
console.log(isNaN(l));
```

##Operations
###operate
```javascript
var o = 1 + 2;
var q = 3 * 4.2;
var r = 16 / 8;
var s = 16 / 7;
var t = 15 % 10;
var u = (1 + 2) * (3 + 4);
```
[operator precedence](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)
###comparsion
```javascript
console.log( 1 < 2);
console.log( 1 === 1.00); //true
console.log( 1 == "1"); //true ,auto change
console.log( 1 === parseInt("1", 10)); //true

console.log( 1 != 3); //true
console.log( 1 !== 3); //true
```
###math
```javascript
var u = Math.random();
var u = Math.round(Math.random() * 10);
var u = Math.round(2.7); //upper from 2.5.
var u = Math.floor(2.9); // 2
var u = Math.ceil(6.2); // 7
var u = Math.pow(2, 5);
var u = Math.sqrt(4);
var u = Math.min(2, 4);
// type Math in Chrome's console, you can view all methods
```

##Array
###define
```javascript
var x = ['some', 'word', 'here'];
x.length;
var y = ['string', 3, true, function (a,b) {return a + b}];
var z = ['string', ['substr', 2]];
var z = [];
var a = new Array();
var a = new Array(50); //define 
```
###slice and operate
```javascript
var word = y[0];
var answer = y[1];
var foo = y[3];
foo(1,2);
var ud = y[100]; //undefined
y[4] = 144;
y[20] = 21; //interval value -> undefined
y[y.length] = "new element";
y[y.length] = "new element";
y[y.length] = "new element"; //auto add at end
```
###methods
```javascript
/*push and pop*/
var my_array = [2, 3, 4];
//log, otherwise it will be the same
console.log(my_array.toString()); 
my_array.push(5); //return current length
console.log(my_array.toString()); //log

var lastone = my_array.pop();
var lastone2 = my_array.pop();

/* unshift and shift*/
var my_array = [2, 3, 4];
console.log(my_array.toString());  // 2,3,4
my_array.unshift(1);
console.log(my_array.toString());  // 1,2,3,4

var my_array = [2, 3, 4];
console.log(my_array.toString());  // 2,3,4
my_array.shift();
console.log(my_array.toString());  // 3,4
```

###methods - sort
```javascript
var my_array = [10 ,42, 12, 3, 0, 4, 11];
console.log(my_array.toString());

my_array.sort(function (a, b) {
	return a - b; //-n, 0 , n
});
console.log(my_array.toString()); // itself is sorted, but.. string

//trick, mess it
my_array.sort(function (a, b) {
	return Math.random() - 0.5;
});

var my_array = [1,2,3,4,5];
my_array.reverse();

//sorted by length
my_array.sort(function (a, b){
	return a.length- b.length;
})
```

###method - concat
```javascript
var x = [1,2,3];
var y = [5,6,7];
var z1 = x.concat(y); //concat(l1,l2,l3);
var z2 = x.concat(5,6,7); //same result
var z3 = x.concat(5,[6,7]); //like append with extend
```

###method - slice
```javascript
var my_array = [0,1,2,3,4,5,6,7];
var result = my_array.slice(1, 4); //start & end
```

###method - join
```javascript
var my_array = ['this', 'is', 'a', 'line' , 1, 2];
var result = my_array.join(' ');
```
compare with python

```python
my_array = [1,2,3,4,5];
result = ' '.join(my_array) #it will raise TypeError
result = ' '.join(map(str, my_array))
```

###method - splice
```javascript
var my_array = [0, 1, 2, 3, 4, 5, 6];
delete my_array[2]; //set to undefined
//from 2, remove 1 element
my_array.splice(2, 1); 
//from 2, remove 0 element, add 1 element
my_array.splice(2, 0, 'two'); 
//from 2, remove 1 element, add 1 element
my_array.splice(2, 1, 'two'); 
my_array[2] = 'two';
//from 2, remove 0, add array
my_array.splice(2, 1, 'two', 'three', 'sth else'); 
```

##Functions
###basic functions
```javascript
function sayHello (name, greeting) {
	console.log(greeting + "hello" + name);
}
function sayHello (name, greeting) {
	if (typeof greeting === "undefined"){
		greeting = "Hello";
	}  //没有默认值的写法吗？
	console.log(greeting + "hello" + name);
}

sayHello();
```

###return value
```javascript
function sayHello (name, greeting) {
	if (typeof name === "undefined") {
		return 0;
	}
	if (typeof greeting === "undefined") {
		greeting = "Hello";
	}
	console.log(greeting + "hello" + name);
	return "Done";
	//return name.length;
	console.log("end of log"); //will not execute
}
```
###scope
```javascript
//shadowing variable
var color = "black";

function showColor () {
	var color = 'green';
	shape = "square";
	console.log(color);
}
showColor();
console.log("global shape", shape); 
//global can be define inside function..
```

###anonymous functions
```javascript
function myFunction () {
	return "fine";
}
myFunction();

var myFunction = function () {
	return "anonymous";
}
myFunction();

var callTwice = function (targetFunction) {
	targetFunction();
	targetFunction();
}
callTwice(myFunction);
callTwice(function () {
	console.log("hahaha");
})
//useful in debug
callTwice(function namedFunction () {
	console.log("hahaha");
})

(//only way to create a scope
function () {
	var a, b, c;
	//...
	console.log("from anon functions");
})(1, 'hello');
```

###examples
DOM

```javascript
button = document.getElementById('action');
input = document.getElementById('text_field');
//onclick is good, but not good enough
button.onclick = function () {
	console.log('aaa');
}
button.onclick = function () {
	console.log('bbb');
} //will override last one..
//addEventListener is better
button.addEventListener('click', function () {
	console.log('action1');
});
button.addEventListener('click', function () {
	console.log('action2');
	input.setAttribute('value', 'Hello world');
});
```

##Object
###basic
```javascript
var kikodo = {"name": "kikodo"};
kikodo.name;
//better 
var kikodo = {
	name: "kikodo",
	skills: ["ruby", "js"],
	"favorite color": "green"
};
kikodo["favorite color"] 
```

###method
```javascript
var kikodo = {
	name: "kikodo",
	greet: function () {
		console.log("hello, I am "+ kikodo.name); //not good
	}
};
kikodo.greet();

var kikodo = {
	name: "kikodo",
	greet: function (person) {
		console.log("hello, I am "+ person.name); //isn't ideal
	}
};
kikodo.greet(kikodo.name);

var kikodo = {
	name: "kikodo",
	greet: function () {
		console.log("hello, I am "+ this.name); //that's it
	}
};
kikodo.greet();

var greet = kikodo.greet;
greet() // hello, I am DOMWindow

function WhatIsThis () {
	console.log(this);  //-> DOMWindow
}
```

###apply
```javascript
var greet = kikodo.greet;
greet.call(kikodo); //bund to kikodo...
greet.call({}); //I am undefined
greet.call({"name": "Amit"}); //I am undefined
kikodo.greet.call({"name": "Amit"}); //holly shit, cool
kikodo.greet.call({"name": "Amit"}, "Matt", "bad");  // rest strings

kikodo.greet.apply(nick);
var jim = {
	name: "jim";
	greet: function (name, mood) {
		name = name || "you";
		mood = mood || "good";
		console.log("hello," + name + " I am " 
			+ this.name + " and I am in a " + mood + " mood!");
	}
}
jim.greet.apply(nick, ["Matt", "bad"]);  //array!
var args = ["Matt", "bad"];
jim.greet.apply(jim, args);
```


###prototype1
```javascript
var personPrototype = {
	name: "Anonymous";
	greet: function (name, mood) {
		name = name || "you";
		mood = mood || "good";
		console.log("hello," + name + " I am " 
			+ this.name + " and I am in a " + mood + " mood!");
	},
	species: "Homo Sapien"
}

function Person (name) {  
//constructor, Capitalized as convention
	this.name = name;
	return this;  //default return this if no return..
}

Person.prototype = personPrototype; //it knows where prototype is 


kikodo = new Person("kikodo") 
//new: this !-> windows, this ->Person
//new: __proto__ -> personPrototype
//new: [name, __prototype__]
kikodo.greet()
```
###prototype2
```javascript
Person.prototype.species = "Human";  //change things..
Person.prototype.favoriteColor = "red";
//personPrototype.favoriteColor = "red"; //same?

/* what happen if i forgot to add new */
kikodo = Person("kikodo"); //-> windows.name == "kikodo"
```
