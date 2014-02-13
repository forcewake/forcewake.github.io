# [ [Coding Instinct](http://www.codinginstinct.com/)](http://www.codinginstinct.com/)

"We are survival machines - robot vehicles blindly programmed to preserve the
selfish molecules known as genes" - Richard Dawkins

  * [Home](http://www.codinginstinct.com/)
  * [Subscribe (RSS)](http://feeds2.feedburner.com/codinginstinct)

[Argument validation using attributes and method
interception](http://www.codinginstinct.com/2008/05/argument-validation-using-
attributes.html)

Last week Fredrik Normen had a couple of [nice
posts](http://weblogs.asp.net/fredriknormen/archive/2008/05/08/how-to-
validate-a-method-s-arguments.aspx) on argument validation using C# extension
methods. It got me thinking on a different approach where a method interceptor
could handle the validation.

First a short recap on argument validation. I guess everyone have seen or
written code with some kind of argument validation, like this for example:

    
    public void Add(string value)
    {
      if (string.IsNullOrEmpty(value)) 
          throw new ArgumentException("The value can't be null or empty", "value");
        
      ///...
    }

To make argument validation easier I have (to a small extent) used a modified
version of [this](http://www.codeproject.com/KB/cs/designbycontract.aspx)
design by contract utility class. This utility class lets you write argument
validation like this:

    
    public void Add(string value)
    {
      Check.Require(value != null, "value should not be null");
        
      ///...
      
      Check.Ensure(Count > 0);
    }

So this got me thinking on how you could handle argument validation using
method interception. With a method interceptor you could inspect the function
arguments checking for validation attributes. I envisioned code looking like
this:

    
    [Ensure(() => this.Count > 0)]
    public void Register([NotNull] string name, [InRange(5,10)] int value)
    {
      ///...
    }

Getting the ensure post condition to work like that will be impossible since
you can only use constant expressions in attribute constructors. This is
something I think they should look at for C# 4.0, being able to define lambdas
in attribute arguments would make interesting scenarios possible (like this).
The NotNull and InRange attributes were very simple to implement:

    
    public abstract class ArgumentValidationAttribute : Attribute
    {        
      public abstract void Validate(object value, string argumentName);
    }
    
    [AttributeUsage(AttributeTargets.Parameter)]
    public class NotNullAttribute : ArgumentValidationAttribute
    {
      public override void Validate(object value, string argumentName)
      {
        if (value == null)
        {
            throw new ArgumentNullException(argumentName);
        }
      }    
    }    
    
    [AttributeUsage(AttributeTargets.Parameter)]
    public class InRangeAttribute : ArgumentValidationAttribute
    {
      private int min;
      private int max;
    
      public InRangeAttribute(int min, int max)
      {
        this.min = min;
        this.max = max;
      }
    
      public override void Validate(object value, string argumentName)
      {
        int intValue = (int)value;
        if (intValue < min || intValue > max)
        {
          throw new ArgumentOutOfRangeException(argumentName, string.Format("min={0}, max={1}", min, max));
        }
      }
    }

This is of course the easy part. It is the actual interceptor that is making
the magic happen. This implementation is just a proof of concept, and not
optimal from a performance stand point.

    
    public class ValidationInterceptor : IInterceptor
    {
      public void Intercept(IInvocation invocation)
      {
        ParameterInfo[] parameters = invocation.Method.GetParameters(); 
        for (int index = 0; index < parameters.Length; index++)
        {
          var paramInfo = parameters[index];
          var attributes = paramInfo.GetCustomAttributes(typeof(ArgumentValidationAttribute), false);
    
          if (attributes.Length == 0)
            continue;
    
          foreach (ArgumentValidationAttribute attr in attributes)
          {    
            attr.Validate(invocation.Arguments[index], paramInfo.Name);
          }            
        }
    
        invocation.Proceed();
      }        
    }

To make this interceptor more feasible for production you would probably have
to add some smart caching mechanism. This interceptor based argument
validation is of course somewhat limited in applicability. You have to use a
dynamic proxy generator to get the interceptor functionality and only
interface and virtual functions can be intercepted. But it is an interesting
idea and shows another nice usage scenario for method interception.

The concept of being able to intercept method calls is something that would be
great if it was built into a future version of the CLR runtime. That way
static and non virtual methods could be intercepted. It is a nice way to
implement cross-cutting concerns (like validation). But until then we will
have to live with proxy generators, it works nicely for interfaces:

    
    public interface IRegistrationService
    {
      void Register([NotNull] string name, [InRange(5, 10)] int value);
    
      void Register([NotNullOrEmpty] string name);
    }

If you specify the attributes on an interface you do not need to duplicate
them on the implementation methods! A small update to the interceptor to
support validation of the return value would allow something like this:

    
    public interface IUserRepository
    {
      [return: NotNull]
      User GetById([GreaterThanZero] int id);
      
      ///...
    }

The syntax for attributes on return values are kind of funky. If Spec# isn't
the future (I hope it is) then maybe something like this can be :)

Posted by Torkel Ödegaard at [10:52 PM](http://www.codinginstinct.com/2008/05
/argument-validation-using-attributes.html)

Labels: [AOP](http://www.codinginstinct.com/search/label/AOP),
[C#](http://www.codinginstinct.com/search/label/C%23), [Design By
Contract](http://www.codinginstinct.com/search/label/Design%20By%20Contract)

####  12 comments:

Erik said...

    

Wow, very cool! The Validation block does something similar for attributes,
but I do not think it can validate function arguments!

     [ May 13, 2008 at 6:33 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1210696380000#c5166475989520526738) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=5166475989520526738)
Anonymous said...

    

Yes this is the same as something I implemented to fake Aspects in C#.

     [ May 29, 2008 at 12:44 AM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1212014640000#c8032638940865155198) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=8032638940865155198)
[Victor Sergienko](http://victorsergienko.com/) said...

    

I'm not very happy with runtime check, it adds little to Debug.Assert or
whatever.

NotNull should better be a compile-time check.

  
Sadly, I don't see a way to make attribute work at compile time. I found a
pretty (and pretty dead) XC# http://www.resolvecorp.com/Products.aspx which
could.

  
Exactly what I wanted - a post-compile utility that analyzes compiled
assemblies and warns if possible-null value is used for not-null variable.
Like FxCop.

  
Sadly, reimplementing it for C#3 should be a lot of work.

     [ June 3, 2008 at 8:07 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1212516420000#c6682676370260028715) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=6682676370260028715)
[Calvin](http://www.blogger.com/profile/05299338367090580682) said...

    

I must say I would also love to see more flexibility with the arguments that
can be passed to attributes.

  
I ran into a similar problem with lambdas in attribute constructors which I
solved in the following manner.

  
public class MyAttWithLambda : Attribute

{

  
public MyAttWithLambda(Action[object] myAct)

{

myAct.Invoke(this);

}

  
}

  
public class MyAdditionalAtt : MyAttWithLambda

{

  
public MyAdditionalAtt() :

base(c => Console.WriteLine(this.ToString()))

{

  
}

  
}

  
Please forgive in obvious mistakes as I write this from memory...

  
Its not particularly elegant, but does solve the problem of, really any, types
being passed to attributes that cannot be passed in the declarative syntax.

  
I have also replaced < with [ and > with ] since the guys here deem the
[object] ( :P ) statement to be html :)

     [ October 23, 2008 at 2:36 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1224765360000#c4611898948508590156) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=4611898948508590156)
[Torkel Ödegaard](http://www.blogger.com/profile/08914354140151859277) said...

    

Interesting approach to get lambdas in attributes!

     [ October 23, 2008 at 3:03 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1224766980000#c4571508447910748254) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=4571508447910748254)
[michael.](http://www.blogger.com/profile/07111554408538441829) said...

    

Here's a simple implementation that uses PostSharp
(http://www.postsharp.org/about/):

  
[Serializable]

[AttributeUsage(AttributeTargets.Method | AttributeTargets.Constructor,
AllowMultiple = false)]

public class ArgumentValidator : OnMethodBoundaryAspect

{

public override void OnEntry(MethodExecutionEventArgs eventArgs)

{

ParameterInfo[] pinfo = eventArgs.Method.GetParameters();

foreach (ParameterInfo info in pinfo)

{

foreach (ArgumentValidationAttribute validator in GetCustomAttributes(info,
typeof (ArgumentValidationAttribute)))

{

validator.Validate(eventArgs.GetReadOnlyArgumentArray()[info.Position],info.Na
me);

}

}

base.OnEntry(eventArgs);

}

}

  
Then you decorate your methods like this:

  
[ArgumentValidator]

public void DoSomething(string theThing, [NotNull]string theOtherThing)

{ }

     [ October 23, 2008 at 6:41 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1224780060000#c2120582258223406857) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=2120582258223406857)
[Fasty](http://www.blogger.com/profile/16877731144003523451) said...

    

This solution and many others seem to be compensating for poor object oriented
language (OOL) designs together with lazy compilers.

  
The constraints on parameters and return values effectively are derived Types.
Consequently our code is underspecified when we use "string" for NonNullable<
string > and "int" for RangeLimitedInt< 1,5 >.

  
In fact, the ubiquity of "int" as a generalized counter from 0..n is a
testimonial to the relative immaturity of OOLs.

     [ December 13, 2008 at 6:35 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1229189700000#c6706654110335651148) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=6706654110335651148)
Vernon said...

    

Interesting post. Validation Aspects (www.codeplex.com/ValidationAspects) can
do all this using PostSharp. Well ok, not attribute funcs! That would be
great!

     [ January 19, 2009 at 9:20 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1232396400000#c6895651305856210121) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=6895651305856210121)
[Bill Forney](http://www.billforney.com) said...

    

You should check this out... [Spec#](http://research.microsoft.com/en-
us/projects/specsharp/)

     [ February 22, 2009 at 4:43 AM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1235274180000#c6269550545931298297) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=6269550545931298297)
[Philip Laureano](http://www.blogger.com/profile/08456668588660909284) said...

    

Try LinFu.DesignByContract. It's been out since last year, and it does exactly
what you mentioned in this post:

  
http://www.codeproject.com/KB/cs/LinFu_Part5.aspx

     [ February 25, 2009 at 10:02 AM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1235552520000#c3048671420747000211) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=3048671420747000211)
[Michael Cowan](http://www.blogger.com/profile/01618416377703735638) said...

    

I know I am just the QA guy .. but we have opted for the simpler method of
passing the parameter into a validator and specifying its name.

  
  
public static void IsUrlLoaded(string expectedUrl, IE browser)

{

ArgumentValidationService.ThrowIfNullOrEmpty("expectedUrl",expectedUrl);

ArgumentValidationService.ThrowIfNull("browser",browser);

...

...

}

  
And here is a simple validator:

  
public static void ThrowIfNull(string paramName, object obj)

{

if (obj == null)

{

throw new ArgumentNullException(paramName);

}

}

  
Its not fancy, but if you rely solely on the bubble up of exceptions for
logging, its better to catch bad data as soon as possible.

     [ April 1, 2009 at 5:27 AM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1238556420000#c2058055797462151818) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=2058055797462151818)
Anonymous said...

    

could you share this: "Small change to support validation of the return value"

  
What is the required change?

  
thanks.

     [ July 24, 2013 at 6:45 PM ](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html?showComment=1374684304857#c7282805706196146277) [ ![](//www.blogger.com/img/icon_delete13.gif) ](http://www.blogger.com/delete-comment.g?blogID=3198935374994345981&postID=7282805706196146277)

[Post a Comment](http://www.blogger.com/comment.g?blogID=3198935374994345981&p
ostID=7056699114019777817)

####

[ ]()

[Newer Post](http://www.codinginstinct.com/2008/05/nhibernate-validator.html)
[Older Post](http://www.codinginstinct.com/2008/05/spec-and-ideas-
for-c-40.html) [Home](http://www.codinginstinct.com/)

Subscribe to: [Post Comments (Atom)](http://www.codinginstinct.com/feeds/70566
99114019777817/comments/default)

![](http://www.codinginstinct.com-a.googlepages.com/me3_black_morecolor.jpg)

[Email](mailto:torkel@codinginstinct.com)

[Twitter](http://www.twitter.com/torkelo)

My name is Torkel Ödegaard. I am a developer, who is very passionate about
agile software development, TDD, open source and continuous learning. I live
in Stockholm, Sweden.

[![](http://feeds.feedburner.com/~fc/codinginstinct?bg=0033FF&fg=FFFFCC&anim=0
)](http://feeds.feedburner.com/codinginstinct)

[![](http://www.codinginstinct.com-a.googlepages.com/altdotnet.png)](http://al
tdotnet.org/)

## Categories

  * [Agile](http://www.codinginstinct.com/search/label/Agile) (2)
  * [ALT.NET](http://www.codinginstinct.com/search/label/ALT.NET) (4)
  * [AOP](http://www.codinginstinct.com/search/label/AOP) (3)
  * [ASP.NET](http://www.codinginstinct.com/search/label/ASP.NET) (4)
  * [ASP.NET MVC](http://www.codinginstinct.com/search/label/ASP.NET%20MVC) (13)
  * [Benchmarks](http://www.codinginstinct.com/search/label/Benchmarks) (3)
  * [Boo](http://www.codinginstinct.com/search/label/Boo) (3)
  * [C#](http://www.codinginstinct.com/search/label/C%23) (49)
  * [Castle](http://www.codinginstinct.com/search/label/Castle) (7)
  * [CMS](http://www.codinginstinct.com/search/label/CMS) (1)
  * [CodeSaga](http://www.codinginstinct.com/search/label/CodeSaga) (3)
  * [CQRS](http://www.codinginstinct.com/search/label/CQRS) (1)
  * [DDD](http://www.codinginstinct.com/search/label/DDD) (1)
  * [Design](http://www.codinginstinct.com/search/label/Design) (8)
  * [Design By Contract](http://www.codinginstinct.com/search/label/Design%20By%20Contract) (2)
  * [Git](http://www.codinginstinct.com/search/label/Git) (1)
  * [Graphics](http://www.codinginstinct.com/search/label/Graphics) (2)
  * [HTML/CSS](http://www.codinginstinct.com/search/label/HTML%2FCSS) (5)
  * [IoC](http://www.codinginstinct.com/search/label/IoC) (7)
  * [Javascript](http://www.codinginstinct.com/search/label/Javascript) (5)
  * [LINQ](http://www.codinginstinct.com/search/label/LINQ) (2)
  * [Logging](http://www.codinginstinct.com/search/label/Logging) (2)
  * [M](http://www.codinginstinct.com/search/label/M) (2)
  * [Metrics](http://www.codinginstinct.com/search/label/Metrics) (1)
  * [MGrammar](http://www.codinginstinct.com/search/label/MGrammar) (3)
  * [Microsoft](http://www.codinginstinct.com/search/label/Microsoft) (2)
  * [Misc](http://www.codinginstinct.com/search/label/Misc) (5)
  * [MonoRail](http://www.codinginstinct.com/search/label/MonoRail) (4)
  * [MSBuild](http://www.codinginstinct.com/search/label/MSBuild) (1)
  * [MVC](http://www.codinginstinct.com/search/label/MVC) (10)
  * [NAnt](http://www.codinginstinct.com/search/label/NAnt) (2)
  * [NHibernate](http://www.codinginstinct.com/search/label/NHibernate) (12)
  * [Oslo](http://www.codinginstinct.com/search/label/Oslo) (3)
  * [PDC](http://www.codinginstinct.com/search/label/PDC) (1)
  * [Powershell](http://www.codinginstinct.com/search/label/Powershell) (1)
  * [Presentations](http://www.codinginstinct.com/search/label/Presentations) (5)
  * [psake](http://www.codinginstinct.com/search/label/psake) (1)
  * [Reviews](http://www.codinginstinct.com/search/label/Reviews) (3)
  * [Science](http://www.codinginstinct.com/search/label/Science) (1)
  * [Security](http://www.codinginstinct.com/search/label/Security) (1)
  * [Silverlight](http://www.codinginstinct.com/search/label/Silverlight) (2)
  * [Slick Code Search](http://www.codinginstinct.com/search/label/Slick%20Code%20Search) (1)
  * [TDD](http://www.codinginstinct.com/search/label/TDD) (7)
  * [WatiN](http://www.codinginstinct.com/search/label/WatiN) (3)
  * [WCF](http://www.codinginstinct.com/search/label/WCF) (2)
  * [WPF](http://www.codinginstinct.com/search/label/WPF) (7)

[ ![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png) ](//www.blogger.c
om/rearrange?blogID=3198935374994345981&widgetType=Label&widgetId=Label1&actio
n=editWidget&sectionId=categories)

### Archive

  * [ ►  ](javascript:void(0)) [2013](http://www.codinginstinct.com/search?updated-min=2013-01-01T00:00:00%2B01:00&updated-max=2014-01-01T00:00:00%2B01:00&max-results=3) (3)
    * [ ►  ](javascript:void(0)) [March](http://www.codinginstinct.com/2013_03_01_archive.html) (3)
  * [ ►  ](javascript:void(0)) [2011](http://www.codinginstinct.com/search?updated-min=2011-01-01T00:00:00%2B01:00&updated-max=2012-01-01T00:00:00%2B01:00&max-results=3) (3)
    * [ ►  ](javascript:void(0)) [December](http://www.codinginstinct.com/2011_12_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [April](http://www.codinginstinct.com/2011_04_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [March](http://www.codinginstinct.com/2011_03_01_archive.html) (1)
  * [ ►  ](javascript:void(0)) [2010](http://www.codinginstinct.com/search?updated-min=2010-01-01T00:00:00%2B01:00&updated-max=2011-01-01T00:00:00%2B01:00&max-results=10) (10)
    * [ ►  ](javascript:void(0)) [November](http://www.codinginstinct.com/2010_11_01_archive.html) (2)
    * [ ►  ](javascript:void(0)) [June](http://www.codinginstinct.com/2010_06_01_archive.html) (4)
    * [ ►  ](javascript:void(0)) [May](http://www.codinginstinct.com/2010_05_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [April](http://www.codinginstinct.com/2010_04_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [March](http://www.codinginstinct.com/2010_03_01_archive.html) (2)
  * [ ►  ](javascript:void(0)) [2009](http://www.codinginstinct.com/search?updated-min=2009-01-01T00:00:00%2B01:00&updated-max=2010-01-01T00:00:00%2B01:00&max-results=17) (17)
    * [ ►  ](javascript:void(0)) [November](http://www.codinginstinct.com/2009_11_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [October](http://www.codinginstinct.com/2009_10_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [September](http://www.codinginstinct.com/2009_09_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [August](http://www.codinginstinct.com/2009_08_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [July](http://www.codinginstinct.com/2009_07_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [May](http://www.codinginstinct.com/2009_05_01_archive.html) (2)
    * [ ►  ](javascript:void(0)) [April](http://www.codinginstinct.com/2009_04_01_archive.html) (1)
    * [ ►  ](javascript:void(0)) [March](http://www.codinginstinct.com/2009_03_01_archive.html) (4)
    * [ ►  ](javascript:void(0)) [February](http://www.codinginstinct.com/2009_02_01_archive.html) (3)
    * [ ►  ](javascript:void(0)) [January](http://www.codinginstinct.com/2009_01_01_archive.html) (2)
  * [ ▼  ](javascript:void(0)) [2008](http://www.codinginstinct.com/search?updated-min=2008-01-01T00:00:00%2B01:00&updated-max=2009-01-01T00:00:00%2B01:00&max-results=50) (62)
    * [ ►  ](javascript:void(0)) [December](http://www.codinginstinct.com/2008_12_01_archive.html) (5)
    * [ ►  ](javascript:void(0)) [November](http://www.codinginstinct.com/2008_11_01_archive.html) (6)
    * [ ►  ](javascript:void(0)) [October](http://www.codinginstinct.com/2008_10_01_archive.html) (9)
    * [ ►  ](javascript:void(0)) [September](http://www.codinginstinct.com/2008_09_01_archive.html) (7)
    * [ ►  ](javascript:void(0)) [August](http://www.codinginstinct.com/2008_08_01_archive.html) (5)
    * [ ►  ](javascript:void(0)) [July](http://www.codinginstinct.com/2008_07_01_archive.html) (4)
    * [ ▼  ](javascript:void(0)) [May](http://www.codinginstinct.com/2008_05_01_archive.html) (11)
      * [Descriptive comments for null arguments](http://www.codinginstinct.com/2008/05/descriptive-comments-for-null-functions.html)
      * [The Language Instinct Review](http://www.codinginstinct.com/2008/05/language-instinct-review.html)
      * [Boo in Visual Studio](http://www.codinginstinct.com/2008/05/boo-in-visual-studio.html)
      * [The Path of Least Resistance](http://www.codinginstinct.com/2008/05/path-of-least-resistance.html)
      * [NHibernate Validator](http://www.codinginstinct.com/2008/05/nhibernate-validator.html)
      * [Argument validation using attributes and method in...](http://www.codinginstinct.com/2008/05/argument-validation-using-attributes.html)
      * [Spec# and ideas for C# 4.0](http://www.codinginstinct.com/2008/05/spec-and-ideas-for-c-40.html)
      * [JQuery and Separation of Concerns](http://www.codinginstinct.com/2008/05/jquery-and-separation-of-concerns.html)
      * [IoC Container Benchmark ReRevisted - Ninject updat...](http://www.codinginstinct.com/2008/05/ioc-container-benchmark-rerevisted.html)
      * [WaitN and xUnit.NET Integration testing](http://www.codinginstinct.com/2008/05/waitn-and-xunitnet-integration-testing.html)
      * [Handling Execution Context / User Session](http://www.codinginstinct.com/2008/05/handling-execution-context-user-session.html)
    * [ ►  ](javascript:void(0)) [April](http://www.codinginstinct.com/2008_04_01_archive.html) (15)

[ ![](http://img1.blogblog.com/img/icon18_wrench_allbkg.png) ](//www.blogger.c
om/rearrange?blogID=3198935374994345981&widgetType=BlogArchive&widgetId=BlogAr
chive1&action=editWidget&sectionId=archive)

Copyright (C) Torkel Ödegaard | E-mail
[torkel@codinginstinct.com](mailto:torkel@codinginstinct.com) .

  *[10:52 PM]: 2008-05-12T22:52:00+02:00
