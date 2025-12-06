For years — (i think) like most developers — I searched for the silver bullet to build business software from scratch.What I found (DDD, Clean Architecture, Hexagonal, Vertical Slices…) never really fit.
Instead, it frustrated me deeply.But I didn’t give up.I returned to the roots and started relearning object-oriented programming (OOP) from scratch.This journey completely changed how I write code.
Here is what I learned — and why I started this blog. 2/14
Along the way, I met others who had gone through the same pain and frustration.I learned an incredible amount from them (@yegor256
, @javadevguy
, @amihaiemil
 and many more).Then came the decisive realization:The problem was sitting in front of the screen — me.My own subjective way of thinking had prevented me from truly understanding OOP.
It wasn’t the language. It wasn’t the concepts themselves.It was my mindset.3/14
That’s exactly why I started this blog.So you don’t have to take the same 10-year detour I did.Here are the four parts that document my complete re-learning journey — and that will forever change how you write Java code.4/14
Part 1: System Composition
Why DI containers are (almost always) an anti-pattern and how explicit, pure composition makes everything visible, testable and lightning fast.→ https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/000_system_composition_de.md5/14
Part 2 & 3: Decorate it All + The Mechanics of Good ObjectThe absolute game changer.Good objects are:
– immutable
– hold no state themselves
– pure proxies that animate external data
– only extended via decoratorsUtilities, setters, anemic models — all dead.→ https://medium.com/@andreas.wagner.info/decorate-it-all-bcce5753e635
→ https://medium.com/@andreas.wagner.info/the-mechanics-of-good-object-e431440e0e446/14
Part 4: How to Structure and Naming Code like a ProThe crowning finale.Forget:controller/service/repository folders  
UserService, InvoiceManager, get/set  
DTOs  
layered architecture

Instead:one concept = one package  
DbInvoice, ValidInvoice, StripeWallet  
price(newPrice)  
the 3 Happy Packaging Rules

→ https://github.com/andreas-wagner-dev/object-oriented-learning-journey/blob/main/blog/how_to_structure_and_naming_code_like_a_pro.md7/14
All of this is not theory.All of this runs in production today — in Self-XDSD.50 kLOC, > 600 classes, real payments (including crypto), start time < 600 ms, 96 % test coverage.
No Spring. No @Service
. No layers.https://github.com/self-xdsd
https://self-xdsd.comClone it. Run ./mvnw test. Look at the code.You won’t want to go back.8/14
This blog is my way of giving back.I wish someone had told me 15 years ago:“You’re the problem. Change your thinking — and the code will become beautiful.”Now I’m telling you.9/14
If you read this and still think
“But our system is too big”
“But we need Spring”
“But the team…”Then just look at Self-XDSD.It works. Better than anything I ever built with Spring.10/14
This is not an attack.
This is an invitation.Try it once — just one side project.You’ll feel the difference immediately.11/14
And when someone says “UserService” in the next meeting…
…you’ll smile inside.12/14
Who’s switching in 2026?Reply with “ICH” (or “I’M IN”) — I want to see how many we are.13/14
RT if you think more developers should read this.The revolution has already started.
We’re not alone anymore.14/14
Thank you to everyone who showed me the way.Without you, I’d still be stuck in layer hell.Now it’s your turn.#Java #OOP #NoSpring #CleanCode #SoftwareArchitecture(https://self-xdsd.com – beautiful code lives here)

