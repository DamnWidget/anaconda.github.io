+++
date = "2014-10-12T18:47:22+01:00"
draft = true
title = "About Anaconda"

+++

Anaconda is a plugin that turns your Sublime Text 3 into a full featured Python
development IDE.

### With performance in mind

Are you tired of extensions that promise you nice features but make your
Sublime Text freeze and blocks while you are writing?. Well, we too, this is
why the main goal of anaconda is the performance. Anaconda will never freeze
your Sublime Text as everything in anaconda runs asynchronous.

It doesn't matters if you are linting a file with a few hundred or a few
thousands of lines, anaconda will work smooth in each situation making you
focus in your code and not interrupting your coning each few seconds.

### Anaconda's History

At began, anaconda was a small project with no pretensions at all, we only
wanted to have a single plugin able to complete and lint code in a single
package.

After a few weeks, other people started to use it and the first feedbacks came.
One of the first things that people reported is that sometimes, when they were
trying to auto-complete projects that used huge libraries like PyQt or NumPy,
the ST3 was unresponsive for a few seconds while the underlying `jedi` library
was caching and processing the contents of the package, that was obviously
wrong and is why we decide to go asynchronous.

Today, thousands of Python developers use anaconda daily and this number
continue growing (more than any other Python specialized plugin).

### Anaconda Asynchronous Architecture

Anaconda is an `asynchronous client-server` architecture application. That means
that part of anaconda runs in the Sublime Text 3 embedded Python interpreter
runtime and other runs in a decoupled standalone asynchronous server.

All the heavy process is done in this standalone server so the embedded Python
interpreter in Sublime Text 3 never get stuck, stale or unresponsive.

#### How this works?

Anaconda detects if any Sublime Text 3 window has a Python buffer view. If there
is any Python buffer, it starts a new standalone anaconda's JsonServer (that is
knows how to speaks JSON with the part of anaconda that is running into the
Sublime Text 3 runtime) for that window.

Anaconda's JsonServers use the Python interpreter that is available in the
system `PATH` but this behavior can be configured globally or per project.

In this way, we solve two common problems in Sublime Text plugins:

* Plugins have to work/use in the Python version of the embedded Sublime Text runtime (Python 3.3.3).
* Plugins usually make Sublime Text 3 unresponsive (or completely freeze) when they do weight process or calculations even using Threads as the embedded Sublime Text Python interpreter also have a [GIL](https://wiki.python.org/moin/GlobalInterpreterLock) that get locked

##### How anaconda solves those two problems?

* In anaconda, auto completion, linting, refactoring, code analytics, complexity
checks, validations run using your system Python interpreter (or whatever other
that you configure) with no limitations

* In anaconda, each Sublime Text window (if you have more than one open) use
it's own standalone copy of anaconda's JsonServer running as a separate process
so it can be effectively using more than one core of your processor, not
blocking your Sublime Text embedded Python interpreter GIL and non making the
ST3 GUI wait for any operation.

When we are writing, the part of anaconda that resides in your Sublime Text,
asks to the JsonServer for completions, linting, code formating, documentation,
function signatures, imports validation and much more in concurrent way through
a real non blocking socket and register a callback into the Aanconda's callback
system to interact with the Sublime Text 3 API when there is an available
result coming back from the non blocking socket.

This is why anaconda can perform multiple operations at the same time without
any performance degradation not mattering if your project is a few files with
a few lines or a Django monster with hundred of thousands of lines.

### Why not for Sublime Text 2?

Honestly, who want to use ST2 could being using ST3? I am serious, the
performance of ST3 is far better than the old ST2. The only reason for
don't switch from ST2 to ST3 was the lack of plugins, but that is not a
problem nowadays.

A part from that, maintain a multi ST project is a pain, and is not because
you have to support Python2 and Python3 (JsonServer already support Python2
and Python3) but because ST2 and ST3 API's are not compatible, believe me,
I already maintain a [multi ST project](https://github.com/DamnWidget/SublimePySide)
and I will not do it again, ever.
