Reference: http://reactivex.io/learnrx/  

## Functional Programming in Javascript  
### map  
map function to all element in Observable/Array  
```  
Observable/Array.map(fn);
```  

fn - the function use to handle element in Observable or Array  

### filter  
filter element in Observable/Array make fn return to true  
```
Observable/Array.filter(fn)  
```  

fn - a function return boolean    

### concatAll  
flat the Observable/Array, mean that make a 2d Observable/Array to 1d Observable/Array
```  
Observable/Array.concatAll()  
```  

### concatMap  
```  
Observable/Array.concatMap(fn)  
```  

concatAll then map  

### reduce  
traverse Observable/Array,  handle previous and previous result(get from all previous element), return a value  
```  
// if has init  
Observable/Array.reduce(fn, init)  
// no init - init = Observable/Array[0]  
Observable/Array.reduce(fn)
```  

fn - a function handle previous and result  

### zip  
zip to array to a new array(length decide by the shorter one)  
```  
zip(Observable/Array-1, Observable/Array-2, fn)  
```  
fn - the function use how to zip two Observable/Array  

## Working with Observables  
**an Observable is a sequence of values that a data producer pushes to the consumer**  
**an Observable can signal to a listener that it has completed**  
Array traversal is synchronous and completes, and event traversal is asynchronous and never completed
**difference between querying observable and array is the operation direction, the observable is push, the array is pull  
**push mean the element is push to fn by observable, but pull mean element pull from array by fn**  
### Observable.fromEvent  
return a subscription object  
convert an event to subscription  
dispose use to destory a subscription  
```  
const buttonClicks = Observable.fromEvent(button, "click");
 
// In the case of an Observable, forEach returns a subscription object.
const subscription = buttonClicks.forEach(function(clickEvent) {
	alert("Button was clicked. Stopping Traversal.");
    // Stop traversing the button clicks
    subscription.dispose();
});
```  

### Observable.take(count)  
take specific count element in Observable, so it can be use to stop a observable  
```  
const buttonClicks = Observable.fromEvent(button, "click");
// Use take() to listen for only one button click
// and unsubscribe.
buttonClicks.take(1).forEach(function() {
	alert("Button was clicked once. Stopping Traversal.");
});
```  

### Observable.takeUntil(expression)  
take element until expression equal to true, use to stop observable  
```
const stopButtonClicks = Observable.fromEvent(stopButton,"click");

const microsoftPrices = pricesNASDAQ.filter(function(priceRecord) {
	return priceRecord.name === "MSFT";
}).takeUntil(stopButtonClicks);

microsoftPrices.forEach(function(priceRecord) {
    printRecord(priceRecord);
});  
```  


## important example for AJAX API  
### Observable.create  
relies on the fact that all asynchronous APIs have the following semantics:  
* The client needs to be able to receive data.  
* The client needs to be able to receive error information.  
* The client needs to be able to be alerted that the operation is complete.  
* The client needs to be able to indicate that they're no longer interested in the result of the operation.  

Notice that the Observer above defines three methods:  
* next(), used by Observables to deliver new data  
* error(), used by Observables to deliver error information  
* complete(), used by Observables to indicate a data sequence has completed  
```  
const getJSON = function(url) {
	return Observable.create(function(observer) {
	    var subscribed = true;
	    $.getJSON(url, {
			success: function(data) {
	    		// If client is still interested in the results, send them.
                if (subscribed) {
				    // Send data to the client
					observer.next(data);
					// Immediately complete the sequence
					observer.complete();
				}
    		},
            error: function(ex) {
		        // If client is still interested in the results, send them.
			    if (subscribed) {
			        // Inform the client that an error occurred.
			        observer.error(ex);
			    }
		    }
        });

        // Definition of the Subscription objects unsubscribe (dispose in RxJS 4) method.
        return function() {
            subscribed = false;
        }
    });
};
const observer = {
	// onNext in RxJS 4
	next: function (data) {
	    alert(JSON.stringify(data));
	},
	// onError in RxJS 4
	error: function (err) {
		alert(err);
	},
	// onComplete in RxJS 4
	complete: function () {
		alert("The asynchronous operation has completed.");
	}
};

const subscription = getJSON("http://api-global.netflix.com/abTestInformation").subscribe(observer);
```  

### Throttle Input  
We want the ability to throttle the users's input so that if they interacting for one second, then we will get the user input  
```  
## wait for 1000ms then accept all element in observable    
function (clicks, saveData, name) {
  return clicks.throttleTime(1000).concatMap(function () {
    return saveData(name);
});
```  