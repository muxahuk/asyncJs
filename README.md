# Asynchronous Javascript or how to avoid callback hell
Presentation for my talk on front end meetup

Hello everyone, Today I'm going to talk about "Asynchonous Javascript or how to avoid Callback Hell".

But let me start from introdusing myself.
My name is Mihails Semjonovs and currenlty i'm working in Accenture as nodejs developer.
Overall my work experiance is more than 9 years and i have worked with both: front and back end.

So.. To be on the same level let me talk little bit about what is callback.
Callback is a function, witch is passed as an argument to some utility that is performing some task ( usualy asynchosous )
and when that task is completed the utility will call passed function.

I think that's more than clear. Now lets find out what is callback hell.
Usually callback hell stands for triangilar, or how it also refers piromide of doom, shape code witch is created due to a multiple callback passing one inside another (image on the left),
but if you look at the right image, i have the same code rewriten without triangular shape.
And i can asure you that both of these codes suffer from callback hell.
So.. what really is callback hell ?

Let me provide this example and it should clearafy everything:
Imagine you'r beeing asked to write some ecommerce website and as the latest step before charging money from client you should use some 3-rd party utility to collect statistics.
[explane the image]
So you have launched website, it works fine.. After 4 mounths you are at the restaurant dringing morning coffe and suddenly calls you'r boss and with the panic in the woice is telling you that some celebrity was buying TV and hi's credit card was charged 5 times, he posted a tweet about that and now there's a huge PR problem.
You rush at the work. Start searching a problem and after a wile you get to the point when realise that there's no other possible way to be charged 5 times credit card rather than you'r callback was called 5 times.
So you go to that utility documentation page, you try to find about calling you'r callback multiple times and there's not a single word about that.
You decide to call to utility support and ask about that. The girl says that she can't find it eather and that she will ask it department and will get back to you via email.
On the next day you recive a huje letter from utility support where they are saying that they had some issue and theyr development code went live. He's been live only 5 minutes. And in that code they were calling you'r callback and waiting for some answer back and if there was no answer they would call you'r callback again 5 times and then silantly fail.
So you print that letter, go to you'r boss and show it to him. How many of you think that you'r boss would be like "Ok, sounds good for me." ?
I would say aproximetly 0. Becouse at these point there was exposed about you'r sistem is something you never realised before. You didn't realised that there was a trust point when you passed an callback.
Right. So you go back to you'r code that where written 4 mounths ago and you think "I'm geneouse, I'll make a boolean flag to check if it was called or not". After these little fix you go to you'r boss and tell him that the issue has been fixed. And then you'r boss asks you "What if the never call our callback, do you have code for these ?" and you'r starting panic, "No, i don't have, but i could set some timer and check if it was called or not" and then you'r boss "What about other callbacks, have you fixed them to ?". At these point you'r thinking "May be i can pretend that i din't heard him sayng that ?".
Because what has been exposed is how deep these rabbit hole goes. The trust issues that where involved in passing an callback.

And that's my first major problem of callback hell. I'v colled it Inversion of control.
By Inversion of control i mean that there's a part of my code witch i'm in control of and then there's a callback, witch is beeing passed to some utility and i'm not in control of it.
Most important what we need to understand here is that we are trusting some utility to properly call our callback.
But we need to understand that any utility posibaly could call our callback wrong or not to call it at all. 

The Second majore problem is that callbacks are not mind setted / designed.
I'm not a neiro cientist, but on the lowest part of our brain we all think synchonously. We can think only about one thing. And writing asynchonous code with callbacks is against how our brains work. Every time you write some callback, or even worse callback inside a callback, your brain has to do "non local jumps".

It reminds me of some game i was playing when i was a kid. On one of the levels i had to go to cave and it was compleatly dark, but i had a torch witch showed me only small pice of the cave. You could go all around the cave, but you would see only these small pice, you could not see all the map at one. Thet's what modern javascript programming is these days.
In our code we have callbacks all over the place and more often they are splited to different files and making these non linear jumps to find out how you'r code works is against how our brains work. You could memorize all the callbacks, even if it's small flow you'r brain is making huje amount of work in order to understand them. At least it's what i think.

Ok. Hope these is clear. First let's talk about non fixes.
There's 2 non fixes i could remember:
the first one is multiple callback's. You had to pass one funtion for success result and another for failure. But it rises more trust issues as we could experiance that both callbacks were called.
Also there is error first ( or node style ) callback's, where in the callback is passed error as first parameter and the result. But it also rises more trust issues. What would happen if you'r callback is called with both, error and response paramethers. Do you have code for these ? or what if it calls with no error and no result ?

Now let's talk about thunks
Actually I haven't known them for a long time and when i finally got to know them and understood - it was shoking how could i live without them. Cos they aren't some library or something that's needed to be installed. They are just a pure javascript, just enother way of thinking. I wish i could knew about them earlier..
Ok. Back to my presentation. 
You might ask what's thunk ? Thunk is a pure function that needs no parameters to be passed in it in order for it to give you some result. And this result wouldn't be different. It's always the same. That's a synchronouse thunk and it looks something like this.
[explane image]
So every time we call thunk() we'll get the same result of 42.
As our topic is Asynchonous javascript let me explane what is Asynhronouse thunk. Asynchronous thunk is a pure funtion that need no parametres, except callback, to be passed in it, in order for it to give you some result by way of passing it as a parameter to you'r callback.
[explane image]
And these example i made just to ilustrait you how powerfull thunks are
[explane image]
That being sad I have to admit that thunks are not solving any of callback hell issues. We are still using callback's. But what thunk do - they are, more or less, explaining how Promises works. Cos promises are just some wrapper around the value, just like thunks, but has fencier api.

This leads us to the first real solution to callback hell - Promises.
Before we get to the promise api i want you to understand what's "hidden", what's stands behind the promises and why is it important to understand the idea behind of them. I can assure you that there's such thing as promisses hell and when in some point of time you rich it i don't want you to turn around and get rid of promisses. They are most important advances we made in codifying reasonable asynchonous coding patterns.

Let me provide some examples of what is promise by it nature.
Imagine that you'r in a mcdonalds and you are ordering a cheesburger, but you don't get cheesborger right away. Instead they give you a check, wher it's writen you'r order number, 12 in this metaphore. So the check becomes a promise to you'r cheesburger. You can start thinking of cheesburger in you'r had. How it's tasty or how it dances cha cha cha. You have something in you'r hand that allows you to think about your future value. And then when you'r order is done the call for order 12, and now you can exchange you'r promise for the value to that value itself. This is a silly methaphore, but methaphore non the less about what is happaning with promisses. Promisses are a codofication of an idai that we need a placeholder to elimenate time as a consurn wrapped around a value. We need a container around it. In programmic language it's called future value.
Infect promisses came to  javascript from E language. and in E they have this "futures" and in E a "future" is literally replaced by it's value, but you can perform operations that you would normaly do to this value. You can perform operations on that thing.
And we didn't get the same, but the brought something simillar, that allows us to think / reason about the values, ignoring the time.
So that's one way of thinking about the promise that it's "future value".

Another way of thinking about the promise it that it uninverts the paradigm that we are traditionally used to interact with some utuluty that are asynchronous in nature. When i call some utility and i know that utility will finish in some later time we need to pass a callback to it. And if you remember i'v called it "Inversion of control". But what if we could uninvert or not to invert it in the first place. Ruther then passing a callback to some utility. What if we could say something like: I'm gona call some async task, and i know that it myght get some time to finish, but rather than passing a callback to that utility it would like that utility to return me some event listener so i listen for the completion event and you just fire that event when you'r good and ready and i'll be notifyed by that event thet you'r finished and i'll know thet i can move on. In fact i could also listen to error event. If utility has some error or any other events you could just fire them and if i have subscribet to that event i'll do what's needed.

