# JavaScript closures...

:published_at: 2014-07-02
:hp-tags: 

JavaScript is the de facto programming language on the web.

But, I would say JavaScript is also easily the most misunderstood language among programmers. While syntactically it look the same with Java or even C, the design is different.

Allright, I want to share something on closure and without further ado, let's dive straight into this particular example:

```
(function () {
    var state2 = 0;
    var Abc = function () {
        this.state1 = 0;
    };
    Abc.prototype.count = function () {
        this.state1++;
        state2++;
    };
    Abc.prototype.get1 = function () {
        return this.state1;
    };
    Abc.prototype.get2 = function () {
        return state2;
    };
    window.Abc = Abc;
})();
var instance1 = new Abc();
instance1.count();
instance1.count();
instance1.count();
 
 
console.log('instance1.get1() ', instance1.get1());
    // -> Gives 3
 
console.log('instance1.get2()', instance1.get2());
    // -> Gives 3
```

But, if you try to create another instance:
```
var instance2 = new Abc();
instance2.count();
instance2.count();
instance2.count();

console.log('instance2.get1() ', instance2.get1());
    // -> Gives 3
console.log('instance2.get2()', instance2.get2());
    // -> Gives 6   // wtf happen here?
```

Seems like we're being clever by making `var state2 = 0;` to properly encapsulate the logic for this `Abc` class. But, unfortunately, it's a broken logic with `new`.

What happens here, `instance2` still refers to the exact `state2` variable due to closure. But, `state1` is always created for each new instance of `Abc` prototypal class.

My personal take on code encapsulation in JavaScript is to really encapsulate when needed. In normal cases, I don't mind if `state1` is leaking the class instance. After all, with module system being so robust and popular kid on the block, you really should not bind unnecessary variables to global environment.

If you need to bind `instance1` to global environment, there are ways to archive that and one of them is using factory pattern.


