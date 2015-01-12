+++
date = "2014-10-12T18:47:22+01:00"
draft = true
title = "About Anaconda"
+++

Anaconda is a plugin that turns your Sublime Text 3 into a full-featured Python
development IDE.

# Supported Platforms

Anaconda currently supports all three Sublime Text 3 platforms:
Linux, OS X and Windows.  The status of the plugin among these platforms is

Platform | Status | Development | Maintainer
-------- | ------ | ----------- | ----------
**Linux** | <span class="green">Stable</span> | <span class="green">Active</span>      | <a href="https://github.com/DamnWidget">@damnwidget</a>
**OS X**  | <span class="green">Stable</span> | <span class="green">Active</span>      | <a href="https://github.com/DamnWidget">@damnwidget</a>
**Windows** | <span class="green">Stable</span> | <span class="green">Active</span>      |

We are looking for a maintainer for the Windows platform. Currently,
<a href="https://github.com/DamnWidget">@damnwidget</a> fixes bugs and solves
incidents for Windows using a Virtual Machine; it works, but is not ideal.

On Windows, users can run the plugin in profiling mode (Sublime Text 3 doesn't
support the `cProfile` library in `POSIX` platforms), configuring the option
`anaconda_debug` to `profiler`. If the profiling mode is active, a profiling
log should be displayed in the Sublime Text 3 console, where the user can check
where the time is being spent.

# With performance in mind

Are you tired of extensions that promise you nice features but make your
Sublime Text freeze and block you from writing? Us too! This is
why the main goal of Anaconda is performance. Anaconda will never freeze
your Sublime Text, as everything in Anaconda runs asynchronously.

It doesn't matter whether you're linting a file with a few hundred or a few
thousand lines; Anaconda will work smoothly in each situation, helping you
to focus on your code without being interrupted every few seconds.

# Anaconda's History

At the beginning, Anaconda was a small project with no pretensions at all; we only
wanted to have a single plugin to complete and lint code in a single
package that ran in Sublime Text 3, as all the other plugins were broken or
just didn't work.

After a few weeks, other people started to use it and provide feedback.
One of the first things that people reported is that sometimes when they were
trying to auto-complete projects that used huge libraries like PyQt or NumPy,
 ST3 was unresponsive for a few seconds while the underlying `jedi` library
was caching and processing the contents of the package. This was obviously
the wrong approach and is why we decided to go asynchronous.

Today, Anaconda is used by thousands of Python developers around the globe, and
this number grows daily — more than any other Python-specialized
plugin for Sublime Text.

# Anaconda Architecture

Anaconda is an `asynchronous client-server` architecture application. That means
that part of Anaconda runs in the Sublime Text 3 embedded Python interpreter
runtime, and the other part runs in a decoupled, standalone, asynchronous server.

All the heavy processing is done in this standalone server so that the embedded Python
interpreter in Sublime Text 3 never gets stuck, stale or unresponsive.

## How does this work?

Anaconda detects if any Sublime Text 3 window has a Python buffer view. If there
are any Python buffers, it starts a new standalone Anaconda JsonServer for that window.  This server
knows how to speak JSON with the part of Anaconda that is running in the
Sublime Text 3 runtime.

Anaconda's JsonServers use the Python interpreter that is available in the
system `PATH`, but this behavior can be configured globally or per project (
take a look at [Configuring Anaconda the Right Way](http://localhost:1313/anaconda/anaconda_settings/)
for detailed information about that).

In this way, we solve two common problems in Sublime Text plugins:

* Plugins have to work/use the Python version of the embedded Sublime Text runtime (Python 3.3.3).
* Plugins usually make Sublime Text 3 unresponsive (or completely frozen) when they do weight process or calculations, even when using Threads, as the embedded Sublime Text Python interpreter also has a [GIL](https://wiki.python.org/moin/GlobalInterpreterLock) that gets locked.

### How does Anaconda solve these two problems?

* In Anaconda, auto completion, linting, refactoring, code analytics, complexity
checks and validations run using your system Python interpreter (or whatever you configure) with no limitations.

* In Anaconda, each Sublime Text window (if you have more than one open) uses
its own standalone copy of Anaconda's JsonServer running as a separate process,
so it can effectively use more than one core of your processor, without
blocking your Sublime Text embedded Python interpreter GIL or making the
ST3 GUI wait for any operation.

When you are writing, the part of Anaconda that resides in your Sublime Text
asks the JsonServer for completions, linting, code formating, documentation,
function signatures. It also imports validation, and does much more concurrently through
a real, non-blocking socket. The ST-residing part of Anaconda also registers a callback into Anaconda's callback
system to interact with the Sublime Text 3 API when there is an available
result coming back from the non-blocking socket.

This is why Anaconda can perform multiple operations at the same time without
any performance degradation, whether your project is a few files with
a few lines or a Django monster with hundred of thousands of lines.

## Why not for Sublime Text 2?

Honestly, who wants to use ST2 when there's ST3? Seriously, the
performance of ST3 is far better than ST2. The only reason not to switch used to be the lack of plugins,
but that's no longer a problem.

In addition, maintaining a multi-ST project is a pain — not because
you have to support Python2 and Python3 (JsonServer already supports them both),
but because the ST2 and ST3 APIs are not compatible. Believe us,
we already maintain a [multi-ST project](https://github.com/DamnWidget/SublimePySide),
and will not do it again, ever.
<br><br><br>

# Anaconda ST3 Python IDE logo

This is the Anaconda Sublime Text 3 Python IDE logotype.

<img src="/anaconda/img/anaconda-sm.png">
