---
layout: post
title: Enumeration with Generic Interface
published: true
---

## Problem

I have commands to execute. Commands are plain C# classes with some data.

{% highlight csharp %}
    var commands = new List<ICommand>
        {
            new Command1("A"),
            new Command1("B"),
            new Command2(20, 30)
        };
{% endhighlight %}
And I want to make command-specific handlers.

{% highlight csharp %}
    public class Command1Handler : ICommandHandler<Command1>
    {
        public bool CanHandle(ICommand command)
        {
            return command is TCommand;      // this is always the same for all command handlers.
        }

        public void Execute(Command1 command)
        {
            // TODO: execute the command. I want to accept Command1, not ICommand.
        }
    }
{% endhighlight %}
And finally, I want to loop through commands and call corresponding commandHandlers.

{% highlight csharp %}
    var commandHandlers = new List<ICommandHandler>()
        {
            new Command1Handler(),
            new Command2Handler()
        };

    foreach (var command in commands)
    {
        var handler = commandHandlers.Single(x => x.CanHandle(command));
        handler.Execute(command);
    }
{% endhighlight %}
C# does not allow such usage, since `ICommandHandler<>` cannot be used without `<>`. How do we solve this problem? 


## Solution

Define `ICommandHandler` as command-type-agnostic.

{% highlight csharp %}
    public interface ICommandHandler
    {
        void Execute(ICommand command);
        bool CanHandle(ICommand command);
    }
{% endhighlight %}
Define `CommandHandlerBase` as generic class that accepts command-type `T`. It implements `ICommandHandler`, and it requires its child to implement `abstract Execute(T command)`.

{% highlight csharp %}
    public abstract class CommandHandlerBase<T>: ICommandHandler where T: ICommand
    {
        public bool CanHandle(ICommand command)
        {
            return command is T;
        }

        protected abstract void Execute(T command);

        public void Execute(ICommand command)
        {
            Execute((T)command);
        }
    }
{% endhighlight %}
Now command handler looks like:

{% highlight csharp %}
    public class Command1Handler : CommandHandlerBase<Command1>
    {
        protected override void Execute(Command1 command)
        {
            // TODO: execute the command
        }
    }
{% endhighlight %}
Note that `Command1Handler`'s `Execute()` receives `Command1`, not `ICommand`. 

Bonus: Command1Handler does not worry about CanHandle(), since by declaring `CommandHandlerBase<Command1>` I already said I can handle `Command1` only.