---
layout: post
title: User* Can Create** New Patron***
category: "Rovani on C&sharp;"
tags:
- cqrs
- eventsourcing
- factory
- ddd
- vigiljouney
---

The first thing that I learned with Dependency Injection is that it encourages a mindset of "I'll figure that out that later." This allows me
to put in the bare minimum for what might pass a test, even though I know it will have all kinds of complex integration somewhere down the line.
To start this little experiment, I am going to put together a tiny amount of arguably functional code. I will start with the very simple
definition of what a User is, what it means to Create an entity, and how a Patron is represented.

## <sup>*</sup>"User"

A "User" in this case is the arbitrary creator of a command and consumer of the factory. For now, the User is the test suite (powered by xUnit).

{% highlight c# linenos=table %}
using Moq;
using System;
using Vigil.Domain;
using Vigil.MessageQueue;
using Vigil.MessageQueue.Commands;
using Xunit;

namespace Vigil.Patrons
{
    public class PatronFactoryTest
    {
        [Fact]
        public void User_Can_Create_New_Patron()
        {
            var queue = new Mock<ICommandQueue>(MockBehavior.Strict);
            queue.Setup(q => q.QueueCommand(It.IsAny<ICommand>(), It.IsAny<IKeyIdentity>())).Verifiable();
            PatronFactory factory = new PatronFactory(queue.Object);

            IKeyIdentity result = factory.CreatePatron(new CreatePatronCommand()
            {
                DisplayName = "Test User",
                IsAnonymous = false,
                PatronType = "Test Account"
            });

            queue.VerifyAll();
            Assert.NotEqual(Guid.Empty, result.Id);
        }
    }
}
{% endhighlight %}

## <sup>**</sup> "Create"

At this point, I am going to define Create as issuing the command to _something else_ to instantiate and persist a new
representation of the entity.

{% highlight c# linenos=table %}
using System.Diagnostics.Contracts;
using Vigil.Domain;
using Vigil.MessageQueue;
using Vigil.MessageQueue.Commands;

namespace Vigil.Patrons
{
    public class PatronFactory
    {
        protected readonly ICommandQueue _queue;

        public PatronFactory(ICommandQueue queue)
        {
            _queue = queue;
        }

        public IKeyIdentity CreatePatron(CreatePatronCommand command)
        {
            Contract.Requires(command != null);
            Contract.Ensures(Contract.Result<IKeyIdentity>() != null);

            var key = KeyIdentity.NewIdentity();

            _queue.QueueCommand(command, key);

            return key;
        }
    }
}
{% endhighlight %}

## <sup>***</sup> "Patron"

A patron, this early in development, is an abstract representation of what should be created - it does not care about persistance
or structure.

{% highlight c# linenos=table %}
using System.ComponentModel;
using System.ComponentModel.DataAnnotations;
using Vigil.Domain;

namespace Vigil.MessageQueue.Commands
{
    public class CreatePatronCommand : ICommand
    {
        [Required, StringLength(250)]
        public string DisplayName { get; set; }
        [DefaultValue(false)]
        public bool IsAnonymous { get; set; } = false;
        [Required, StringLength(250)]
        public string PatronType { get; set; }
    }
}
{% endhighlight %}

## The Rest of the Code

It always annoys me when a post references classes, interfaces, or other code that is no included in the actual post. It makes it terribly
difficult to understand what the referenced, but not shown, code pieces actually do. As such, here is the rest of it.

### ICommandQueue Interface

The most basic, simple interface with the bare minimum for what a Queue would need to implement.

{% highlight c# linenos=table %}
using Vigil.Domain;

namespace Vigil.MessageQueue
{
    public interface ICommandQueue
    {
        void QueueCommand(ICommand command, IKeyIdentity key);
    }
}
{% endhighlight %}

### ICommand Interface

This is just a simple way to identify commands and provide a way for future restrictions and contracts, when I need them.

{% highlight c# linenos=table %}
namespace Vigil.Domain
{
    public interface ICommand
    {
    }
}
{% endhighlight %}

### IKeyIdentity Interface

By abstracting the key to an interface, I can decide late if and how I would like to change it. This will limit the places
that are impacted if I need to change out the type of the Id, or add more fields.

{% highlight c# linenos=table %}
using System;

namespace Vigil.Domain
{
    public interface IKeyIdentity
    {
        Guid Id { get; }
    }
}
{% endhighlight %}

### KeyIdentity Interface

This is the basic implementation of the IKeyIdentity interface. It follows the ValueObject convention, making the Guid immutable.

{% highlight c# linenos=table %}
using System;
using System.ComponentModel.DataAnnotations;
using System.Diagnostics.Contracts;

namespace Vigil.Domain
{
    public class KeyIdentity : IEquatable<KeyIdentity>, IKeyIdentity
    {
        public static KeyIdentity NewIdentity()
        {
            return new KeyIdentity();
        }

        [Key]
        public Guid Id { get; protected set; }

        protected KeyIdentity()
        {
            Id = Guid.NewGuid();
        }

        protected KeyIdentity(Guid id)
        {
            Id = id;
        }

        /// <summary>Compares two Vigil.Data.Core.Identity classes for equality by Id.
        /// </summary>
        /// <param name="obj">The Vigil.Data.Core.Identity class to compare Id values.</param>
        /// <returns>Returns true if the two Id values are equal; otherwise, false.</returns>
        public override bool Equals(object obj)
        {
            if (ReferenceEquals(null, obj))
            {
                return false;
            }
            if (ReferenceEquals(this, obj))
            {
                return true;
            }
            if (obj.GetType() != GetType())
            {
                return false;
            }
            return Equals((KeyIdentity)obj);
        }

        public bool Equals(KeyIdentity other)
        {
            if (other == null)
            {
                return false;
            }
            return Equals(other.Id, Id);
        }

        public static bool operator ==(KeyIdentity left, KeyIdentity right)
        {
            return Equals(left, right);
        }
        public static bool operator !=(KeyIdentity left, KeyIdentity right)
        {
            return !Equals(left, right);
        }

        public override int GetHashCode()
        {
            return Id.GetHashCode();
        }

        public override string ToString()
        {
            return Id.ToString();
        }

        [ContractInvariantMethod]
        private void ObjectInvariant()
        {
            Contract.Invariant(Id != Guid.Empty);
        }
    }
}
{% endhighlight %}