Error error on the wall
=======================

We know by now that [code]Maybe[/code] is used to add a context of possible failure to values. A value can be a [code]Just something[/code] or a [code]Nothing[/code]. However useful it may be, when we have a [code]Nothing[/code], all we know is that there was some sort of failure, but there's no way to cram some more info in there telling us what kind of failure it was or why it failed.

The [code]Either e a[/code] type on the other hand, allows us to incorporate a context of possible failure to our values while also being able to attach values to the failure, so that they can describe what went wrong or provide some other useful info regarding the failure. An [code]Either e a[/code] value can either be a [code]Right[/code] value, signifying the right answer and a success, or it can be a [code]Left[/code] value, signifying failure. For instance:

This is pretty much just an enhanced [code]Maybe[/code], so it makes sense for it to be a monad, because it can also be viewed as a value with an added context of possible failure, only now there's a value attached when there's an error as well.

Its [code]Monad[/code] instance is similar to that of [code]Maybe[/code] and it can be found in [code]Control.Monad.Error[/code]:

[code]return[/code], as always, takes a value and puts it in a default minimal context. It wraps our value in the [code]Right[/code] constructor because we're using [code]Right[/code] to represent a successful computation where a result is present. This is a lot like [code]return[/code] for [code]Maybe[/code].

The [code]&gt;&gt;=[/code] examines two possible cases: a [code]Left[/code] and a [code]Right[/code]. In the case of a [code]Right[/code], the function [code]f[/code] is applied to the value inside it, similar to how in the case of a [code]Just[/code], the function is just applied to its contents. In the case of an error, the [code]Left[/code] value is kept, along with its contents, which describe the failure.

The [code]Monad[/code] instance for [code]Either e[/code] makes an additional requirement, and that is that the type of the value contained in a [code]Left[/code], the one that's indexed by the [code]e[/code] type parameter, has to be an instance of the [code]Error[/code] type class. The [code]Error[/code] type class is for types whose values can act like error messages. It defines the [code]strMsg[/code] function, which takes an error in the form of a string and returns such a value. A good example of an [code]Error[/code] instance is, well, the [code]String[/code] type! In the case of [code]String[/code], the [code]strMsg[/code] function just returns the string that it got:

But since we usually use [code]String[/code] to describe the error when using [code]Either[/code], we don't have to worry about this too much. When a pattern match fails in [code]do[/code] notation, a [code]Left[/code] value is used to signify this failure.

Anyway, here are a few examples of usage:

When we use [code]&gt;&gt;=[/code] to feed a [code]Left[/code] value to a function, the function is ignored and an identical [code]Left[/code] value is returned. When we feed a [code]Right[/code] value to a function, the function gets applied to what's on the inside, but in this case that function produced a [code]Left[/code] value anyway!

When we try to feed a [code]Right[/code] value to a function that also succeeds, we're tripped up by a peculiar type error! Hmmm.

Haskell says that it doesn't know which type to choose for the [code]e[/code] part of our [code]Either e a[/code] typed value, even though we're just printing the [code]Right[/code] part. This is due to the [code]Error e[/code] constraint on the [code]Monad[/code] instance. So if you get type errors like this one when using [code]Either[/code] as a monad, just add an explicit type signature:

Alright, now it works!

Other than this little hangup, using this monad is very similar to using [code]Maybe[/code] as a monad. In the previous chapter, we used the monadic aspects of [code]Maybe[/code] to simulate birds landing on the balancing pole of a tightrope walker. As an exercise, you can rewrite that with the error monad so that when the tightrope walker slips and falls, we remember how many birds were on each side of the pole when he fell.