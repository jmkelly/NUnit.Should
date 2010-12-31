NUnit.Should
============

What?
-----

Yet another library that adds BDD-style `Should()` assertions to [NUnit][].

It's done using a *whole* [16 lines of code][core] plus some optional `Should*()` [helper methods][helpers].

Download
--------

Latest version: 1.0.0.0

Download .dll for [.NET 2.0][2.0] or [.NET 3.0][3.0]+

[Browse Source][]

Why?
----

I like NUnit.  When I started doing .NET development back in 2005, it was the conventional testing library. 
Although Visual Studio now comes with MSTest, I've found that NUnit still seems to be the conventional testing library 
for open-source projects.

I have a big problem with NUnit, however.  For the past 3-4 years, I've been writing assertions like this using [RSpec][]:

    the_number.should == 5
    the_dogs.should include('rover')

That are 2 different styles of writing these types of assertions in NUnit:

    Assert.AreEqual(5, theNumber);
    Assert.Contains("rover", theDogs);

Or you can use Assert.That:

    Assert.That(theNumber, Is.EqualTo(5));
    Assert.That(theDogs, Contains.Item("rover"));

That's not too bad, right?

Well, when I recently started doing C# full-time again, I didn't mind using Assert.Whatever(expected, actual) or the Assert.That syntax (which reads better, but is very verbose).

But then I realized that the language you use to write your specs is *actually __important!__*  There's a *reason* why BDD frameworks use `Should` instead of `Assert`, even though they do the same things.

When you say `something.Should(be in some state)` you're more likely to check for an object's behaviors/states, in my honest opinion.  When you `Assert.AreEqual(foo, bar)` you're more likely to be writing low level "unit tests."

It's just different wording.  But it's important.

So, I started looking for libraries that would give me a `Should()`-like syntax with NUnit but I didn't have much luck.  I found NBehave, but it offers more than I need and it required .NET 3.5 (and I want to be able to support .NET 2.0). 
I also found some projects on github, but 1 had no code and another didn't implement many `Should()` methods

I wanted a way to integrate nicely with NUnit's `Assert.That` contraints so that, out of the box, I should be able to write `5.Should(Be.EqualTo(5))` without implementing the `EqualTo` method myself, but just use NUnit's!

So NUnit.Should was born.

The Code
--------

The code is so tiny, I put it here in the README.  Now ... there are also some `Should*()` [helper methods][] available, but this is the main code.

    using System;
    using System.Collections;
    using NUnit.Framework.Constraints;
    
    namespace NUnit.Framework {
    
        public class Be      : Is       { public Be(){} }
        public class Have    : Has      { public Have(){} }
        public class Contain : Contains { public Contain(){} }
    
        public static partial class ShouldExtensions {
            public static void Should(this object o, IResolveConstraint constraint) {
                Assert.That(o, constraint);
            }   
            public static void ShouldNot(this object o, Constraint constraint) {
                Assert.That(o, new NotOperator().ApplyPrefix(constraint));
            }   
        }   
    }

That's it!  That let's you say `5.Should(Be.EqualTo(5))` instead of `Assert.That(5, Is.EqualTo(5))`.  Couldn't be simpler!

License
-------

NUnit.Should is released under the MIT license.

[NUnit]:   http://nunit.org
[RSpec]:   http://rspec.info
[core]:    #
[helpers]: # 

[2.0]:           http://github.com/remi/NUnit.Should/raw/1.0.0.0/bin/Release/NET20/NUnit.Should.dll
[3.0]:           http://github.com/remi/NUnit.Should/raw/1.0.0.0/bin/Release/NET30/NUnit.Should.dll
[Browse Source]: http://github.com/remi/NUnit.Should/tree/1.0.0.0
