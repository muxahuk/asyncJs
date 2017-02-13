# Asynchronous Javascript or how to avoid callback hell
Presentation for my talk on front end meetup

Hello every one.
Today I'm going to talk about Asynchronous javascript or how to avoid callback hell.

But let me start from introducing my self.
My name is Mihails Semjonovs and currently i'm working in Accenture as node/js developer. Owerall my work experiance is more than 9 years and i have worked with both: front-end and back-end.
That being clear lets talk about callbacks.

What is callback ?
Callback is a funtion that is passed as an argument to some utility, witch is performing some task that can be synchronous or asynhronous. And when this task is done utility is calling our function in order for our programm to continue it's work.

On the left image we can see setTimeout utility and the function passed to it. In this example after 500 milisecconds setTimeout utility will call our callback.
On the right image i'v tryed to illustrate how it would look in synchronous scenario. But since we don't have such thing as "block" in javascript we need to use callbacks in order to make execution of sacond part of our code.

Hope this is clear. Now lets talk about what is callback hell ?

Usually callback hell is referenced to triangular shape code or how it also is called piromide of doom shape code witch is being created due to multiple callback execution one inside another.
Here's on the left image is an example of piromide of doom shape code.
But if you look at the right image witch i have rewriten, then you'll notice that there's no longer this piromide of doom shape code and it's still suffering from callback hell.

So what really is callback hell ?
Let me explane it to you by example.

Imagine you have being given a task to write some ecomerce shop. And as the last step before charging the money you need to use some utility to collect statistics data. 
sandStatistics function is provided by some 3-rd party library and as an argument it accepts callback to call after statistics were sendt.
In that callback we are charging credit card and showing thank you screen to the client.
So you have launched website and it's working fine. After 4 months you'r boss calling you and says that some celebrity was buying a TV and his credit card was charget 5 times, he has tweeted about it and now there's a huge PR issue. So you rush to the work, start seeking and remembering how al of this works, you try to hit "buy" button five times to check may be that's the problem, but you already have a code for that. Eventually you come up to a conclusion that there's no other way to be charged credit card 5 times rather then you'r callback was called 5 times.
So you start exploring that 3-rd party documentation and there's not a single word about calling you'r callback 5 times. You decide to call the support of that utility and the girl that answers doesn't have a clue how it's possible to call you'r callback 5 times. She says that will as IT department and reply you back. On the next day you recive long email from support where they are explaining everything step by step, but the point is that they'r development code accidently went live, it was live only for 5 minutes and in that code they where waiting for you to answer them something in you'r callback and if there was no answer they would retry for 4 more times and then fail silantly. They'v addmitted that it's they'r fault.
So you print these letter go to you'r boss and show it to him "The utility support admitted that it was they'r fault".
I have a question - how many of you thinks that you'r boss would be like "Ok, sound good for me ?".. Aproxymatly zero i think.
So you get to that code to fix it and you think I'm genious, i'll make a boolean flag to check if it was called or not.
After you'r little fix you go to you'r boss and show it to him. And he says "What if they never call our callback, do you have code for this ?".
At this point you'r starting to panic.. "No i don't, but i could make some timer to check if it was called or not" and the you'r boss says "Have you fixed all of the callbacks in you'r code ?" and you'r thinking "May be i can pretend he didn't sad that ?".
Because what has been exposed is how deep this rabbit hole goes with passing a callbacks. The trust issues that you might never realise.

And that's my first major callback problem. I'v called it inversion of controll.
I know that there are many difinitions to inversion of controll, but i mean only that i have some part of my code that i'm in controll of and than there's seccond part of my code, that is being passed to some utility and i'm not in controll of it. That's what invertiong of control means in my speach.
Most important what we need to understand is that we cannot trust any utility, event if we wrote it, that it will properly call our callback.
I have writen some possible problems that can accure when passing callback to some utility. In our example it was multiple times.

The seccond major problem is that callbacks are not mind setted / designed. If we thing about how our brain works we will get to conclution that it works synchronously. We all can think about 1 thing at the time. And writing asynchronous code with callbacks is agains how our brains work.
Here's a quic example: When i was a kid i was playing some game and in that game there was a level where i was needed to go to cave with the torch. It was compleatly black, except the position i was in. I could go all around the cave, but i wouldn't see the full picture of the cave.. I could remember any corner, but that's not the same.
The same with callbacks - we can go throwgth all callbacks, but we wouldn't be able to understand it right away. We would make a map of callbacks in our had and place them in synchronous fashion to understand. That's just how our brains work at least i think so.. And that's a huge problem, cos we are writing in a way how our brains don't naturally work.

That being sad i hope everything is clear at this point.

Let me continue with non fixes. There where attempts to fix some issues related to callback hell, but they faild.
The first attempt was to pass multiple callbacks, one for success call and seccond for error call.
The seccond attempt is called error first style and the main idea is that in you'r callback the error would be passed as first parameter and then some other variables.
But both these fixes raised more trust issus as we could experiance both funtions could be called or thet there would be no error and no result.

First approach that i wanted to share with you is called thunk.
The thunk is a pure function, that needs no parameters to be passed in it in order to give you a result.
On these example you can see i have "add" function that accepts 2 parameters and just adds them.
Then i'm creating a thunk function and as you can see it accepts no parameters and simply calling and returning add function inside with predefined values.
So every time i call thunk() i will get the same result witch is 42 in this example.
Thet's what synhronous thunk is, the asynchronous thunk is a pure function that needs no parameters expept a callback to be passed and it will always pass the same result in you'r callback.
That's the same example that was preveouse, but instead of returning a value in thunk i'm calling a callback and passing it a result of the add function
And then, whenever i need a result from thunk i would call it and pass a callback to recive the answer.
I have writen more complax example, to show you the power of thunks:
So i have silly getData function with accepts 2 arguments the value and the callback and after 1000 milisecconds callback is called with value as a parameter.
Then i have createThunk function, witch has 2 variables predefined the cbs witch stands for callbacks and answer where will be stored an answer.
So i make a call to getData passing it value and inside the callback i'm cheking if value wasn't defined i'm setting it and then checking if cbs array is not empty and if it's not than calling all the callbacks with an answer as a parameter
finally i'm returning a function where i'm cheking if answer is set and if it is i'm just calling a callback with an answer and if it's not set i'm adding that callback to cbs array.
What this example does - it "caches" the value of getData function and every time you call thunk and pass it a callback the callback will be called when the value is ready. So for example you could call it when the answer isn't retrived yet and then you'r callback would be placed to array and called letter when answer is retrived or can be called immidiatly if answer already is presented.
That being sad i have to admit that thunk doesn't solevs any of the callback issues. But what it does - gives you an idia of how Promises work, cos promise is just a time independent wrapper around the value.

This leads us to the firs real solution of callback hell - promises.
Before we get to the promise api i want you to get the idea why promises are so important, I want you to understand promises. So that when you would face a promise hell you wouldn't say "better to get rid of promisses". Promises are most important thing that was done for codifying resonable codding pattern. Aldought api might look not what we expected.

So, for you to understand promises let me give you few examples:

For example you go to McDonalds and you order a cheesburger. But you don't get cheesburger right away. They give you a check with the number of you'r order. But that doesn't means that you cannot reason about cheesburger, you can start thinking how tasty it is or how cheesburger is dancing cha cha cha.. You have something in you'r hand that alows you to think of you'r future cheesburger. And then when you'r order is done and they call you'r number you are exchanging you'r check for a cheesburger. This is silly methoprore but non the less methaphore. Promise is an idia that we need a placeholder, that is time independed wrapper around the value. In programming language it is called a future value. So that's one way of thinking about promise that it's "future value".

Another way of thinking about the promise is that it uninverts the inversion of controll i was talking previosly. What if insted of passing a callback to some utility, we would get some event listener from it ? and whanever that utility finishes just call the "finish" event or if it fails "error".
Now let's imagine that instad of .on( "finish" ) we could say something like .then() and instead of .on('error') - .catch();
And that what is uninverting an inversion of controll. Now we have some wrapper returned from utility that we can subscribe on.
So that's the seccond way of thinking about the promises that they are event emitters.

Now let's talk about api of promise
Promise construcor accepts a function with resolve and reject arguments that are functions predefined in promise. and whenewer you want to sucesfully fulfill the promise you would call resolve() function and to reject - reject() function. 
Promise api is straight forward. You have .then method witch accepts 2 callbacks, first for success promise resolve and second for error promise resolve. And you have .catch method witch accepts only 1 callback for error resolved promise. Seams simple and it is simple to.
The graitest thing about Promises is that you can chain them. Quite frankly any return in both successful callback and error callback becomes a new promise.
And then there's a static methods of promise class : resolve, reject, race and all. All of this methods are returning promise.
The resolve method is simly returning succesfully resolved promise with the value you pass in.
In this example .resolve will console.log the 42
The reject method is returning error resolved promise, with an error you pass in.
In this example .resolve will wil console.error the "error message"

And 2 methods that are applyed to collection of promises are race and all.
The race method accepts any iterable ( array for example ) with promises as values. And whenever any promise is resolved firs he's resolved value, witch can be eather success or false is beinge the result of Promise.race promise.
So in this example .race will console.log 42
The all method accepts any iterable ( array for example ) with promuses as values. And when all of the promises are resolved sucessfully it will be resolved sucefully, but if any of promises is resolved with error then result of this promise is error.
So in this example .all will console.error the "error" message.
In case of .all the result of that promise is array filled with values of promises that are being passed to .all method.

There are many libraries that extend Promise api and gives you more flexable way of processing promises. Library like Bluebird or asynquence. In these libraries you have such methods as promisifyAll and passing it an object it will convert every method from callback to the promise. I wount show it to you, but you definetly need to explore this feature.

The last thing i wanted to say about promises is that they can be chained. Basically every promise success or error functions are returning new promise. So for example:
I have a promise witch is returning me number 19 as an answer and i can siply return this asnswer with adding 23 to it and chain that promise. so in this example i will get console.log 42.

At this point the only question you might have is "how does promises solves callback hell if you have to pass a callback in it ?".
The Promises were designed to return that trust. 
Promise was designed to be resolved or rejected only once. The messages that gets passed to the promise is always kept. The resolved value is immutable. It means that no one can change the result of you'r promise.
This is a huge deal. Promises fixes all the issues related to inversion of control i was talking about before.

Second callback hell fix is generators.

Generator is a function that can be paused until someone calls a method to contunue it's execution till the next stop.

Most of you know this quest game, where you have to run to some stop, solve the quiz and then run to the next stop where is next question. The generators are most alike this game. On the first call generator is being run till the first stop, then it solves the question or in our examples we will be solving promises and then runs to the next stop and solves again.
Hope overall it is clear how generators are working, let's dig in to them..

When you create generator it returns you Iterator instance. Iterator is a new thing that was introduced in es6 ( along with promises, generators and many other things ). The generator instance has 3 methods, next, return and throw. The next method is running the code till next stop and when it stops its asking a question by passing whatever you type after yield keyword. Then you should answer this question when calling next method. Another method of generators iterator is throw, witch allows you to throw any exception to generator. And if you have try catch block inside generator these exception can be caught. And last method is return, witch basically ends your generator iterator execution.

So, in order for generator to process this "question" and "answer" steps there is required some utility that will run generator throught. Actualy there are a lot of libraries that do that for example co, spawn atc.
But we can create our own runner.. Basicaly they all do something similar to this example.

    function spawn (generator) {
        return function () {
            const iterator = generator.apply(null, arguments);

            function pass (result) {
                try {
                    result = iterator.next(result);
                } catch (e) {
                    return Promise.reject(e);
                }
                return tick(result);
            }

            function fail (error) {
                try {
                    error = iterator.throw(error);
                } catch (e) {
                    return Promise.reject(e);
                }
                return tick(error);
            }

            function tick (step) {
                if (step && step.done) return step.value;
                const result = step ? Promise.resolve(step.value) : Promise.resolve(undefined);

                return result.then(pass).catch(fail);
            }

            return Promise.resolve().then(tick);
        }
    }

