
## Munki Hacking for Profit


In a previous post I mentioned we can insert CSS into our footer page to change the way Managed Software Centre looks. Just as easily, we can add javascript to add some conditional logic.

in my environment we allow clients to purchase software individually. In many cases we will have a number of licenses and we want to make that available for clients to use, however we want to track the usage somehow. It would be nice to be able to allow this functionally within the MSC app and have the \[INSTALL\] buttons say and do something else if a client is not yet entitled to install that software.

The steps we need to take are:

- Grab a list of software and a pricelist
- grab a list of software the current user is entitled to
- provide a web form that allows a user to register for a particular piece of software

This gets a a little tricky as it needs some external data and a way to update that data. A basic database with tables for software and prices and a table for user registrations. (this is the part where the reader needs to supply their own infrastructure). For the pricelist and software entitlements, I like to use a bit of CGI that reads the relevant tables for the data and spits out some JSON that we can read and use - essentially, where the data resides is up to you, as long as you can get it in the right format, even a static flat file will do.

The output though looks a little something like this:

Pricelist JSON

```
[
    {
        "appName": "Adium",
        "price": "1.99"
    },
    {
        "appName": "MacPorts",
        "price": "0.99"
    },
    {
        "appName": "GoToMeeting",
        "price": "299.00"
    }
]
```

appName matches the package name in Munki - this gets important in a bit

So to start with, in our footer we want to create a javascriopt block to put our code in and set up some global variables:

```
<script type="text/javascript">

var pricesArray = [];
var purchasesArray = [];
var userName = "";

</script>
```

We want a way to get a generic JSON file from wherever you're getting it from (you will want to check how your server is set up - The html is rendered from a local file:// location and there are some rules around javascript requesting pages from other sources)

```
function getJson(url) {
	var request = new XMLHttpRequest();
	var appArray = [];
	
	request.open('GET', url, false);
	request.send(null);
	if (request.status === 200) {
		appArray = JSON.parse(request.responseText);
	}
	return appArray;
}
```

Next we want to populate our price list array

```
function priceListInit() {
	var url = "http://localhost/munki_repo/pricelist.json"; //custom - call some generator or static file, return json
	return getJson(url);
}
```

pricesArray = priceListInit();  

The list of apps that a user has access to is also required - the JSON for that looks like this

Purchases JSON

```
[
    {
        "appName": "Foo"
    }
]
```

Again, this is where you need to set up some infrastructure to dynamically collect this info. 

We can get the current user from the path created by the html MSC generates. It's kept in **"/Users/username/Library/Caches/com.googlecode.munki.ManagedSoftwareCenter/html"** 

A quick and dirty way of extracting this info using JS goes something like this:

```
function MSCUser() {
    var pathArray = window.location.pathname.split( '/' )
    var userName = pathArray[2];
    return userName;
}

userName = MSCUser();
```

  

here we are reading the current files path, splitting on "/" and extracting the username

now we can get the purchases for the current user

```
function userPurchasesInit(currentUser) {
    var url = "http://localhost/munki_repo/purchases.aspx?username="+currentUser;
    return getJson(url);
}

purchasesArray =  userPurchasesInit(userName);
```

we also need a function to re-direct users if they aren't licensed for a particular item - rather than perform an install we want to have them action a purchase form or the like - MSC can render regular web pages (to a degree, but it's pretty good) so a simple web form will suffice - the logic you use is up to you but you want to take the username, the app they want and some way of recording a payment info (I use an internal payment system so I get them to enter their details and from there the item is "unlocked")

```
function buyApp(appName) {
    // opens the app purchase form - integrated auth
    displayPage(true, 'http://munki.your.org/Purchase/'+appName);
}
```

for the software request form, again, I use a simple page that collects the right info and saves it into the previously mentioned databases.

Next, a way to check the purchases status of a particular item - this is called in the main loop from the main loop as we're going through the list of purchasable apps

```
function checkPurchasedStatus(appName, currentUser) {
    // verify if the application has been purchased
    // this portion will need to be deployment specific
    var result = false;
    for(var i = 0; i < purchasesArray.length; i++) {
        if (appName == purchasesArray[i].appName+ purchasesArray[i].appVersion || appName == purchasesArray[i].appName) {
            result = true;
        }
    }
    return result;
}
```

Finally, the main loop - here's how this one works

we get all the elements that have a class of "msc-button-inner not-installed"

for each element, we grab the app name then check if it exists in the prices array and is not already purchased, we modify the element inner html to reflect the price and what action to take.

```
function processPrices(pricesArray) {
    var installButtons = document.getElementsByClassName('msc-button-inner not-installed');
    for (var i = 0; i < installButtons.length; i++) {
        var appName = installButtons[i].id.replace('_action_button_text','');
        for(var p = 0; p < pricesArray.length; p++) {
            if (pricesArray[p].appName.includes(appName) && checkPurchasedStatus(appName) != true) {
                var appButton = document.getElementById(installButtons[i].id);
                if (appButton.innerHTML.indexOf("Install") > -1 && appButton.innerHTML.indexOf("Installed") <= 0) {
                    if (pricesArray[p].type.indexOf("Perpetual") > -1) {
                        licType = "Purchase"
                    } else {
                        licType = pricesArray[p].type
                    }
                    appButton.innerHTML = licType+' : $'+pricesArray[p].price;
                    appButton.parentNode.onclick = function(){ buyApp(this.children[0].id.replace('_action_button_text','')); };
                    appButton.className = appButton.className + ' buy';
                }
            }
        }
    }
}
```

The last thing we want o do, is initiate processPrices() when the page loads. We also want to call it every couple of minutes to refresh the page

```
priceListInit();
setInterval(function () {priceListInit()}, 120000); // refresh the buttons every 2 minutes
```

You can see the code, in context, on my [github](https://github.com/bartreardon/munki_client_resources/tree/master/purchase_buttons)
