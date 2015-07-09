```javascript
enyo.kind({
   name: "MyControl",
    foo: "",
    fooChanged: function(inOld) {    // Called when foo changes
        // Do something with "this.foo"
    }
});

tapHandler: function() {
    this.set("foo", "bar");      // Causes fooChanged() to be called
}


enyo.kind({
    name: "MyControl",
    publicProperty: 10
    _protectedProperty: 42,
    observers: [
        { method: "watchValues", path: ["publicProperty", "_protectedProperty"] }
    ],
    watchValues: function(previous, current, property) {  // Called when either property changes
        // Do something with "this.publicProperty" and "this._protectedProperty"
    }
});
```

```javascript
var object = new enyo.Object({value: true});
var observer = function (was, is) {
	enyo.log('value was "' + was + '" but now it is "' + is + '"');
};

object.observe('value', observer);
object.set('value', false); // -> value was "true" but now it is "false"
object.unobserve('value', observer);
object.set('value', true); // no output because there is no observer

observer: function(previous, current, property) {
    this.log();
},

tapHandler: function() {
    this.observe('foo', this.bindSafely('observer'));
    this.set("foo", "bar");      // Causes fooChanged() to be called
}  

enyo.kind({
    name: "MyValueSlider",
    components: [
        {kind: "moon.Slider", name: "slider"},
        {kind: "enyo.Control", name: "label"}
    ],
    bindings: [
        {from: "$.slider.value", to: "$.label.content"}
    ]
});

enyo.kind({
    name: "MyValueSlider",
    sliderValue: 10,    // Initial value of slider
    components: [
        {kind: "moon.Slider", name:"slider"},
        {kind: "enyo.Control", name:"label"}
    ],
    bindings: [
        {from: "sliderValue", to: "$.slider.value", oneWay: false},    // two-way binding
        {from: "$.slider.value", to: "$.label.content"}                // one-way binding
    ]
});

 enyo.kind({
    name: "MyValueSlider",
    components: [
        {kind: "moon.Slider", name:"slider", min: 0, max: 1},
        {kind: "enyo.Control", name:"label"}
    ],
    bindings: [
        {from: "$.slider.value", to: "$.label.content", transform: function(val) {
            return "The slider value is " + Math.round(val * 100) + "%";
        }}
    ]
});

{from: "$.slider.value", to: "$.input.value", oneWay:false, transform: function(val, dir) {
    if (dir == 1) { //source (`from`) setting value for target (`to`)
        return (val * 100) + "%";
    } else if (dir == 2) { //target (`to`) setting value for source (`from`)
        return parseInt(val) / 100;
    }
}}


enyo.kind({
    name: "MyForecastControl",
    weather: "sunny",
    city: "San Francisco",
    computed: [
        { method: "forecast", path: ["weather", "city"] }    // "forecast" method depends on properties in list
    ],
    forecast: function() {
        return "It's always " + this.get("weather") + " in " + this.get("city")
    }
}); 
    
enyo.kind({
    name: "MyForecastControl",
    weather: "sunny",
    city: "San Francisco",
    components: [
        {kind: "enyo.Control", name: "forecastLabel"}
    ],
    bindings: {
        {from: "forecast", to: "$.forecastLabel.content"}
    },
    computed: [
        { method: "forecast", path: ["weather", "city"] }    // "forecast" method depends on properties in list
    ],
    forecast: function() {
        return "It's always " + this.get("weather") + " in " + this.get("city")
    }
});
    
this.set("weather", "rainy");    
// forecastLabel's content becomes "It's always rainy in San Francisco"

this.set("city", "Seattle");
// forecastLabel's content becomes "It's always rainy in Seattle"


var myModel = new enyo.Model({
    name: "Kevin", 
    hometown: "San Francisco",
    avatar: "/assets/kevin.png",
    height: 6.0
});

myModel.get("name"); // returns "Kevin"
myModel.set("name", "Bob");
myModel.get("name"); // returns "Bob"


var BaseModel = kind({
    kind: null,
    mixins: [ObserverSupport, ComputedSupport, BindingSupport, EventEmitter, StateSupport]
});


 enyo.kind({
    name: "ContactView",
    personModel: null,
    components: [
        {kind: "enyo.Image", name: "avatar"},
        {kind: "enyo.Control", name: "nameLabel"},
        {kind: "enyo.Control", name: "townLabel"}
    ],
    bindings: [
        {from: "personModel.name", to: "$.nameLabel.content"},
        {from: "personModel.hometown", to: "$.townLabel.content"},
        {from: "personModel.avatar", to: "$.avatar.src"}
    ]
});

var myModel = new enyo.Model({
    name: "Kevin", 
    hometown: "San Francisco",
    avatar: "/assets/kevin.png",
    height: 6.0
});
this.$.contactView.set("personModel", myModel);

myModel.set("name", "Bob");        // The "nameLabel" component of the view will update
```

```javascript
{
    "user_id": 1234,
    "name": "Kevin",
    "department": {
        "dept_id": 42,
        "name": "Encabulator Marketing"
    },
    "manager": {
        "user_id": 933,
        "name": "Gray"
    }
}

enyo.kind({
    name: "ContactModel",
    kind: "enyo.Model",
    options: {parse: true},
    source: "ajax",
    getUrl: function() {
        return "http://myservice.com/users/" + this.get("user_id");
     },
    parse: function(data) {
        data.dept_id = data.department.dept_id;
        data.dept_name = data.department.name;
        delete data.department;
        data.manager_id = data.manager.user_id;
        data.manager_name = data.manager.name;
        delete data.manager;
        return data;
    }
});


var myCollection = new enyo.Collection([
    {
        name: "Kevin", 
        hometown: "San Francisco",
        avatar: "/assets/kevin.png",
        height: 6.0
    },
    {
        name: "Gray", 
        hometown: "San Jose",
        avatar: "/assets/gray.png",
        height: 6.1
    },
    {
        name: "Cole", 
        hometown: "Santa Clara",
        avatar: "/assets/cole.png",
        height: 5.9
    }
]);
    
myCollection.at(0);                 // returns enyo.Model instance for "Kevin" record
myCollection.at(2).get("name");     // returns "Cole"


myCollection.add({name: "Ben", hometown: "Austin"});
myCollection.at(myCollection.length-1).get("name"); // returns "Ben"

myCollection.add(new ContactModel({name: "Aaron", hometown: "San Mateo"}));
myCollection.at(myCollection.length-1).get("name"); // returns "Aaron"


// we treat all additions as an array of additions
!(models instanceof Array) && (models = [models]);

new enyo.AjaxSource({name: "ajax"}); 

enyo.kind({
    name: "MyContactCollection",
    kind: "enyo.Collection",
    model: "MyContactModel",
    source: "ajax"              // optional
});

var myCollection = new MyContactCollection([
    {
        name: "Kevin", 
        hometown: "San Francisco",
        avatar: "/assets/kevin.png",
        height: 6.0
    },
     ...
]);

myCollection.at(0);        // returns instance of "MyContactModel" for "Kevin" record


new enyo.AjaxSource({name: "ajax"});

enyo.kind({
    name: "MyContactCollection",
    kind: "enyo.Collection",
    source: "ajax",
    url: "http://myservice.com/users"
});

var myCollection = new MyContactCollection();
myCollection.fetch();

enyo.kind({
    name: "MyContactCollection",
    kind: "enyo.Collection",
    source: "ajax",
    url: "http://myservice.com/users"    // 무시됨
    getUrl: function() {
        return "http://myservice.com/departments/" + this.get("dept_id") + "/users";
    }
});

new MyContactCollection({dept_id: 42});
myCollection.fetch();

{
    "status": {
        "error": "none",
        "last_request": 1230394903
    },
    "result": [
        {
            "user_id": 1234,
            "name": "Kevin",
            "hometown": "San Francisco"
            "avatar": "/images/kevin.png",
            "height": 6.0
        },
        {
            "user_id": 2345,
            "name": "Gray",
            "hometown": "San Jose"
            "avatar": "/images/gray.png",
            "height": 6.1
        },
        {
            "user_id": 4567,
            "name": "Cole",
            "hometown": "Santa Clara"
            "avatar": "/images/cole.png",
            "height": 5.9
        }
    ]
}

enyo.kind({
    name: "MyContactCollection",
    kind: "enyo.Collection",
    options: {parse: true},
    source: "ajax",
    url: "http://myservice.com/users"
    parse: function(data) {        // incoming data contains {status:..., result:...}
        return data.result;        // returned data contains {[{user_id:..., name:...}, {...}]}
    }
});

new enyo.JsonpSource({name: "jsonp"});

enyo.kind({
    name: "MyContactCollection",
    kind: "enyo.Collection",
    model: "MyContactModel",
    url: "http://myservice.com/users",
    source: "jsonp"        // use jsonp source instead of ajax for fetching
});

enyo.kind({
    name: "MyJsonpSource",
    kind: "enyo.JsonpSource",
    fetch: function(opts) {
        opts.callbackName = "kallback";
        this.inherited(arguments);
    }
});
new MyJsonpSource({name: "mysource"});


enyo.kind({
    name: "MySource",
    kind: "enyo.Source",
    fetch: function(rec, opts) {
        // implement code to fetch records
        opts.success(data);        // call success callback to return data
    },
    commit: function(rec, opts) {
        // implement code to store records
        opts.success();        // call success callback
    },
    destroy: function(rec, opts) {
        // implement code to destroy records
        opts.success();        // call success callback
    },
    find: function(rec, opts) {
        // implement code to find records
        opts.success(data);        // call success callback to return data
    },
});
new MySource({name: "mysource"});


 enyo.kind({
    name: "FacebookFeedSource",
    kind: "enyo.Source",
    fetch: function(rec, opts) {
        var resource;
        if (rec instanceof enyo.Collection) {
            var user = rec.get("user") || "me";
            resource = "/" + user + "/feed";
        } else {
            resource = "/" + rec.id;
        }
        FB.api(resource, function(response) {
            if (response && !response.error) {
                opts.success(response);
            } else {
                opts.fail(response);
            }
        });
    },
    commit: function(rec, opts) {
        if (rec.isNew) {
            FB.api("/me/feed", "POST", rec, function(response) {
                if (response && !response.error) {
                    opts.success(response);
                } else {
                    opts.fail(response);
                }
            });
        } else {
            opts.fail();  // FB only supports adding new posts, not editing
        }
    },
    destroy: function(rec, opts) {
        FB.api(rec.id, "DELETE", function(response) {
            if (response && !response.error) {
                opts.success(response);
            } else {
                opts.fail(response);
            }
        });
    }
});
new FacebookFeedSource({name: "fbfeed"});


enyo.kind(
    name: 'enyo.LunaSource',
    kind: 'enyo.Source',
    noDefer: true,
    fetch: function (model, opts) {
        var Kind = ((opts.mock || model.mock) ? enyo.MockRequest : enyo.ServiceRequest);
            ...
            model.request.go(opts.params || model.params || {});
    },
    commit: function (model, opts) {
        //redirect to fetch as for services they're basically the same
        this.fetch(model, opts);
    },
    destroy: function (model, opts) {
        var req = model.request || opts.request;
        if(req && model.request.cancel) {
            model.request.cancel();
            model.request = undefined;
        }
    }
});
```

```javascript
enyo.kind({
    name: "flickr.Source",
    kind: "enyo.JsonpSource",
    urlRoot: "https://api.flickr.com/services/rest/",
    fetch: function(rec, opts) {
        opts.callbackName = "jsoncallback";
        opts.params = enyo.clone(rec.params);
        opts.params.api_key = "2a21b46e58d207e4888e1ece0cb149a5";
        opts.params.format = "json";
        this.inherited(arguments);
    }
});

new flickr.Source({name: "flickr"});

enyo.kind({
    name: "flickr.ImageModel",
    kind: "enyo.Model",
    options: { parse: true },
    source: "flickr",
    computed: [
        {method: "thumbnail", path: ["farm", "server", "id", "secret"]},
        {method: "original", path: ["farm", "server", "id", "secret"]}
    ],
    thumbnail: function() {
        return "https://farm" + this.get("farm") +
            ".static.flickr.com/" + this.get("server") +
            "/" + this.get("id") + "_" + this.get("secret") + "_m.jpg";
    },
    original: function() {
        return "https://farm" + this.get("farm") +
            ".static.flickr.com/" + this.get("server") +
            "/" + this.get("id") + "_" + this.get("secret") + ".jpg";
    },
    fetch: function(opts) {
        this.params = {
            method: "flickr.photos.getinfo",
            photo_id: this.get("id")
        };
        return this.inherited(arguments);
    },
    parse: function(data) {
        data = data.photo || data;
        data.title = data.title._content || data.title;
        data.username = data.owner && data.owner.realname;
        data.taken = data.dates && data.dates.taken;
        return data;
    }
});

enyo.kind({
    name: "flickr.SearchCollection",
    kind: "enyo.Collection",
    model: "flickr.ImageModel",
    source: "flickr",
    options: { parse: true },
    published: {
        searchText: null
    },
    searchTextChanged: function() {
        this.empty({destroy: true});
        this.fetch();
    },
    fetch: function(opts) {
        this.params = {
            method: "flickr.photos.search",
            sort: "interestingness-desc",
            per_page: 50,
            text: this.searchText
        };
        return this.inherited(arguments);
    },
    parse: function(data) {
        return data && data.photos && data.photos.photo;
    }
});
```


```javascript
enyo.kind({
	name: "flickr.Application",
	kind: "enyo.Application",
	view: "flickr.MainView"
});

enyo.kind({
    name: "flickr.MainView",
    create: function() {
        this.inherited(arguments);
        this.set("photos", new flickr.SearchCollection());
        this.$.searchPanel.set("photos", this.photos);
        this.$.slideshow.set("photos", this.photos);
    }
});

enyo.kind({
    name: "flickr.SearchPanel",
    kind: "moon.Panel",
    published: {
        photos: null
    },
    handlers: {
        onInputHeaderChange: "search"
    },
    bindings: [
        {from: "photos", to: "$.resultList.collection"},
        {from: "photos.status", to:"$.spinner.showing", transform: function(value) {
            return this.photos.isBusy();
        }
    ],
    search: function(inSender, inEvent) {
        this.$.resultList.collection.set("searchText", inEvent.originator.get("value"));
    }
});

enyo.kind({
    name: "flickr.DetailPanel",
    kind: "moon.Panel",
    transitionFinished: function(inInfo) {
        if (inInfo.from < inInfo.to) {
            this.model.fetch();
        }
    }
});



module.exports = kind.singleton({
    name: 'enyo.FluxDispatcher',
    kind: CoreObject,
    subscriptions: {},
    subscribe: function(storeid, callback) {
        ...
        this.subscriptions[storeid][id] = callback;
        return id;
    },
    notify: function(storeid, payload) {
        var notifyQue = this.subscriptions[storeid];
        for (var key in notifyQue) {
            var callback = notifyQue[key];
            if(callback) {
                callback(payload);
            }
        }
    }
});
    
module.exports.Store = kind({
    name: 'enyo.FluxStore',
    kind: CoreObject,
    constructor: kind.inherit(function (sup) {
        return function () {
            sup.apply(this, arguments);
            //id the store with the dispatcher
            this.id = FluxDispatcher.subscribe();
            //if the store has an update method, subscribe to payload updates
            if(this.update) 
                this.updateID = FluxDispatcher.subscribe(this.id, bindSafely(this, this.update));
        };
    }),
    ...
});

module.exports = kind.singleton({
    name: 'flickr.Constants.Actions',
    search: 'search'
});
    
```


# test end


### Looking for the issue tracker?  
It's moved to [https://enyojs.atlassian.net](https://enyojs.atlassian.net).

---

# Quick Info

## Core

This repository contains Enyo core. We've pared it down to the essentials, so folks can work at the metal. Widget libraries, g11n code, and other fancy bits are optional packages.

## Lib

Packages should go in a folder named _lib_ (e.g. the _extra_ or _canvas_ repositories on GitHub). _lib_ is a magic name that enyo uses to work with add-on packages. It's recommended you create a _lib_ folder as sibling to _enyo_ and keep your packages there, but you can make as many _lib_ folders as you like and put them anywhere.

## Warning about file://

_Note_: in Chrome, various samples will not work from file:// because of Chrome's security policy. Run from a local http server, use the --allow-file-access-from-files in Chrome, or use the online versions at http://enyojs.com.

# What Is Enyo

Enyo is an object-oriented JavaScript application framework emphasizing modularity and encapsulation. Enyo is suitable for small and large-scale applications.

Enyo up to 1.x was the underlying framework used to develop applications for HP's TouchPad tablet. Enyo as shipped on the TouchPad included an complete set of user interface components and service wrappers. What you will find here is Enyo 2, what we informally call _core_: the primary infrastructure needed to support any number of Enyo-based libraries. Not to worry, Enyo 1.x itself is open-source licensed, and work is progressing on packaging up those controls and goodies to work with Enyo 2.

Enyo was designed from the beginning to be highly extensible. This repository reflects a small working set of code, that can be expanded with any number of libraries or plugins. 

Enyo 2 is lightweight, easy to digest, and powerful. 

# What Do I Get

The core code includes the Enyo kernel, the DOM extensions, some Ajax (XHR) tools, and basic wrapper kinds for a lot of DOM form elements. These things are actually separable (it's easy to make micro-builds of Enyo), but we believe this is a useful working set. 

The Enyo 2 kernel provides a modularity concept (Component) and a view concept (UiComponent). The DOM aspect includes a widget concept (Control) and an extensible event system (Dispatcher). The Ajax package includes basic xhr functionality and an implementation of xhr as a Component (Ajax).  The touch package provides platform-optimized scrollers, while the UI package provides base kinds for common controls like buttons and popups.

Just these pieces are sufficient to create large applications using the Enyo encapsulation model. Developers that want only this low-level code are encouraged to roll-their-own. For those that want a richer set of tools, there are some pre-built libraries already available, and much more on the way.

# Why Do I Care

First is our emphasis on cross-platform: Enyo core works on both desktop and mobile browsers.

Second is Enyo's building block approach to applications. Each piece of an application is a Component, and Components are constructed out of other Components.

For example, it's easy to define a combination of an `<input>` tag with a `<label>` tag into one _LabeledInput_ Component. 

Now I can use (and re-use) LabeledInput as one atomic piece. 

But that's just the beginning. Ultimately, large pieces of functionality can be exposed as single Components, for example a fancy report generator, or a color picker, or an entire painting application.

Use the Enyo encapsulation model to divide and conquer large projects. No particular piece of an application need be especially complex. Because combining pieces is central, it's natural to factor complex sections into smaller pieces. And because of the modularity, all these pieces tend to be reusable, in the same project, in other projects, or even for the public at large.

This is all part of our strategy to allow developers to focus on creativity and avoid Repeating Themselves.

# That's a Lot of Talk

The core Enyo design was proven out by the complex applications HP developed for the TouchPad platform. We don't claim this was particularly easy, there were a lot of hardworking developers on the apps teams, but we are confident the Enyo principles are effective on a large scale.

In any case, roll your sleeves up and try it for yourself.

# Give me the Basics

Here is an Enyo Hello World:

```html
<!doctype html>
<html>
<head>
	<title>Enyo</title>
	<script src="enyojs/2.1.1/enyo.js" type="text/javascript"></script>
	<link href="enyojs/2.1.1/enyo.css" rel="stylesheet" type="text/css" />
</head>
<body>
	<script type="text/javascript">
		new enyo.Control({content: "Hello From Enyo"}).renderInto(document.body);
	</script>
</body>
</html>
```

This example loads an enyo.js build from _enyojs/2.1.1/_. If you downloaded the SDK you have a versioned build file. If you pulled from GitHub, you can either make your own build using a minify script in _enyo/source/minify_ (requires Node), or you can load directly from the source (_enyo/source/enyo.js_). Loading from source is also called 'debug loading' because the modules are loaded as individual files, which is easier for debugging, but much less efficient.

The base enyo.Control works much like an HTML tag. You can assign _classes_ and _attributes_ and give it a _style_. E.g.

```javascript
new enyo.Control({content: "Hello From Enyo", classes: "foo", style: "color: red", 
      attributes: {tabIndex: 0}}).renderInto(document.body);
```

produces

```html
<div class="foo" style="color: red;" tabIndex="0">Hello From Enyo</div>
```

Now, the good parts start when you combine more than one Control, e.g.

```javascript
new enyo.Control({
	components: [
		{content: "Hello From Enyo"},
		{tag: "hr"}
	]
}).renderInto(document.body);
```

This Control now encapsulates two Controls into one scope (we can encapsulate any type of Component, that's why the property is called _components_. Controls are one kind of Component.) The outer Control is responsible for the encapsulated components: it manages their lifecycle, listens to their messages, and maintains references to them. For example:

```javascript
new enyo.Control({
	components: [
		{name: "hello", content: "Hello From Enyo", ontap: "helloTap"},
		{tag: "hr"}
	],
	helloTap: function() {
		this.$.hello.addStyles("color: red");
	}
}).renderInto(document.body);
```

Here we've given one of the components a name ('hello') and told it to send a 'helloTap' message when it's tapped (tap is basically the same as the DOM click event, but it works in both mouse and touch environments). The '$' property is a hash that references all the sub-components (we don't store these references directly on _this_ to avoid name conflicts). Notice there is no add/remove machinery to listen to this event, that's all taken care of.

The main point is that 'hello' and the 'hr', their references and behavior, are completely contained inside the outer control. Now, to re-use this, we need to make it a prototype.

Enyo contains a constructor/prototype-generator that we call enyo.kind. Constructors that enyo.kind produces are called _kinds_. Kinds are not magic, they are just regular old JavaScript constructors. Using the enyo.kind factory however allows us to remove boilerplate from our prototype generation (DRY) and have compact syntax. We can convert the Control above to a kind like so:

```javascript
enyo.kind({
	name: "Hello",
	kind: enyo.Control,
	components: [
		{name: "hello", content: "Hello From Enyo", ontap: "helloTap"},
		{tag: "hr"}
	],
	helloTap: function() {
		this.$.hello.addStyles("color: red");
	}
});
// make two, they're small
new enyo.Control({
      component: [ {kind: "Hello"}, {kind: "Hello"} ]
  }).renderInto(document.body);
```

The code above creates a new kind called "Hello" derived from enyo.Control. It contains some components and some behavior. I can create as many "Hello" objects as I want, each instance is independent, and the user of a "Hello" doesn't need to know anything about its internals.

This ability to define encapsulated objects and behavior (Components) and to re-use those encapsulations as prototypes (kinds) is money.
