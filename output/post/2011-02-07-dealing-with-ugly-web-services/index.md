---
title: "Dealing with Ugly Web Services"
date: "2011-02-07"
categories: 
  - "technology"
---

If you've reviewed the API's of different payment processors, you've seen that  they're fairly standard, but with lots of little differences. All in all, they're well formed, easy enough to work with - but some of them are a little dated in their approach. Sometimes, you have to deal with one web service for processing USD, and a completely different one for CAD. Now, they're not actually all that different in the way they work, they're nearly identical. Here's an example... to register a card on an American merchant account you would do this:

USApi.USDataObject data = new USApi.USDataObject();
avs.SetStreetNumber(streetNumber); 
avs.SetStreetName(streetName);
avs.SetPostalCode(postalCode);

Whereas if it's a Canadian merchant account you should do this:

Api.DataObject data = new Api.DataObject();
avs.SetStreetNumber(streetNumber); 
avs.SetStreetName(streetName);
avs.SetPostalCode(postalCode);

It's annoying; you have to write double the code. At first, you might think "dynamic objects", as they're perfectly suited to this kind of thing where type safety is kind of getting in the way... but that only works in .NET 4.0.  I once proposed a similar problem to [Michael](http://lazyloading.blogspot.com/), and he suggested the Accessor pattern. We create a new accessor for each data object.  The accessors hide the ugly away. A typical accessor for this scenario might look like this:

public class CompletionAccessor
{
     private object Obj;
     private CompletionAccessor(object capture) 
     { }

     public static CompletionAccessor GetCompletionInstance(PreAuthAccessor preauth, ProcessorCredentials credentials)
     {
          object completion = new object();
          if ( preauth is USApi.USPreAuth )
          {
               completion = getUsCompletion( (USApi.USPreAuth)preauth, credentials);
          }
          else if ( preauth is Api.PreAuth ) 
          {
               completion = getCaCompletion( (Api.PreAuth)preauth, credentials);
          }
          return new CompletionAccessor( completion );
     }

     private static getUsCompletion(USApi.UsPreAuth preauth, credentials)
     {
          // set up the US completion request based on the US pre-auth
     }

     private static getCaCompletion(Api.PreAuth preauth, credentials)
     {
          // set up the Canadian completion request based on the Canadian pre-auth 
     }
}

Michael would say "we're building a facade".  Sounds good to me! The net result is that the business logic code that does things like check the AVS response, check the authorization, save the processor reference, all the little things we have to do to process a payment properly don't have to be written twice, even if the code that deals with the partner's API directly does.

The next step might be to make sure that its testable - you might want to access the actual object inside of the accessor,  to run methods on them without a lot of casting and if statements.  To do this, you could make a generic accessor that could be re-used with a method on it that would allow you to run any method on the internal object by name without casting.  This idea is borrowed from Jon Skeet (of [multiple](http://msmvps.com/blogs/jon_skeet/) [fames](http://stackoverflow.com/users/22656/jon-skeet)) - if you were trying to figure out how to use dynamic objects in .NET 3.5,  you could read his [post](http://stackoverflow.com/questions/483215/how-to-do-dynamic-object-creation-and-method-invocation-in-net-3-5) on the matter.If you borrowed liberally from Mr. Skeet's post you might end up with this:

public abstract class GenericAccessor
    {
        internal object Obj { get; set; }

        protected GenericAccessor(object obj)
        {
            Obj = obj;
        }

        private GenericAccessor()
        { }

        public T GetMethodResult(string methodName)
        {
            Type type = Obj.GetType();
            MethodInfo method;
            T retval;
            try
            {
                method = type.GetMethod(methodName);
                retval = (T)method.Invoke(Obj, null);
                return retval;
            }
            catch (Exception ex)
            {
                Logger.LogException(ex);
                return default(T);
            }
        }
    }

 

So now, you can run GetMethodResult to access the processor's API methods directly even through your accessor without having to write a bunch of wrapper methods.  It's kind of like dynamic programming! Though, I prefer the real thing!
