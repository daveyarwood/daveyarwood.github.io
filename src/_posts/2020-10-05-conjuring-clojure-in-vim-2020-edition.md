---
layout: post
title: "Conjuring Clojure in Vim: 2020 Edition"
tags:
  - conjure
  - vim
  - clojure
  - nrepl
published: true
---

{% include JB/setup %}

Clojure tooling for Vim has been getting more and more interesting over the last
several years.

When I first came to Clojure, [Fireplace][fireplace] was the standard Vim plugin
for Clojure development, providing Clojure developers with a great in-editor
REPL experience. I think it's still the case that the majority of Clojurian
Vimmers use Fireplace, but in recent years, a number of viable alternatives
have begun to appear, including the plugins [Acid][acid], [Iced][iced], and
[Conjure][conjure].

I've tried all of these plugins at one time or another, but I've been an avid
user of Conjure since pretty early on in the life of the project. Conjure is a
[Neovim][neovim] plugin that provides an excellent Clojure development
environment out of the box. It is easy enough to get started that I would
recommend Conjure to any Vim users who want to try Clojure for the first time.

# What is this sorcery?

The elevator pitch for Conjure is that it effortlessly connects to your Clojure
REPL and lets you evaluate Clojure code and see the results without having to
leave Vim or take your eyes off of your code. You can do all of this with zero
configuration, and with a handful of standard key mappings that are easy to
learn.

But that really describes all of the Clojure Vim plugins that I mentioned above,
so what sets Conjure apart from the rest of the pack?

The author of Conjure, Oliver Caldwell, has put a ton of thought and effort into
making the out-of-the-box user experience nice and comfortable.

If you're a Neovim user and you know how to use a plugin manager like
[vim-plug], all you have to do is:

1. Install the Conjure plugin for Neovim.
2. `cd` into the directory of a Clojure project. (You can easily start a new one
   by just making an empty directory.)
3. Start an nREPL server. (More about this below.)
4. In another terminal, `cd` into the same directory and open a Clojure source
   file in Neovim (e.g. `nvim foo.clj`).

Conjure will automatically connect to your nREPL server. Then, you can
immediately start writing forms like `(+ 1 2)` and evaluating them by pressing
`<localleader>ee`, and you'll see the results appear in a neat little popup
window.

<center>
<img src="{{ site.url }}/assets/2020-09-21-conjure-basic-usage.gif"
     title="Basic usage of Conjure"
     width="75%">
</center>

# nREPL and you

If you're new to Clojure, you might not be familiar with what an nREPL server
is, or how to start one.

An [nREPL][nrepl] is a type of REPL ([Read-Eval-Print Loop][repl]) that operates
over the network and integrates easily with external tools like Conjure.

There are a handful of existing build tools for Clojure: [Leiningen][lein],
[Boot][boot], and the [official Clojure CLI][clj]. If you're working on an
existing Clojure project, you can determine which one of these to use based on
what type of configuration file you find at the top level of the project:

* If there is a `project.clj` file, it's a Leiningen project.
* If there is a `build.boot` file, it's a Boot project.
* If there is a `deps.edn` file, it's a Clojure CLI project.

If you're starting a new project, I recommend the official Clojure CLI. It's
easy to get started. `deps.edn` is optional, you can just evaluate the command
below to start an nREPL server and you're off to the races.

Depending on the build tool you're using, run one of the following commands to
start an nREPL server:

{% highlight bash %}
# Leiningen
lein repl

# Boot
boot repl

# Clojure CLI
clojure -Sdeps '{:deps {nrepl/nrepl {:mvn/version "LATEST"}}}' -m nrepl.cmdline
{% endhighlight %}

> That Clojure CLI command is a mouthful! I recommend establishing an alias in
> your personal `deps.edn` file, [as described here][nrepl-alias]. Then the
> command is shorter:
>
> `clojure -A:nREPL -m nrepl.cmdline`
>
> ---
>
> In [my own personal `deps.edn` setup][dave-deps-edn], I have an `nrepl-server`
> alias that includes the `-m nrepl.cmdline` part too, which allows me to run an
> even shorter command:
>
> `clojure -A:nrepl-server`

# Bringing in dependencies

Your "project file" (`project.clj`, `build.boot` or `deps.edn`, depending on
which build tool you're using) allows you to specify what Clojure libraries your
project depends on.

The gif below shows how you can use the popular [clj-http] library to make an
HTTP request and print the response. The first step is to create a `deps.edn`
file that looks like this:

{% highlight clojure %}
{:deps
 {clj-http {:mvn/version "3.10.1"}}}
{% endhighlight %}

Then, simply start your nREPL server and start editing some code!

<center>
<img src="{{ site.url }}/assets/2020-09-21-conjure-clj-http.gif"
     title="Using clj-http via Conjure to fetch ASCII cat art from the internet"
     width="75%">
</center>

# A refreshing experience

Conjure provides a convenient way to reload any code in your project that has
changed since the last time you evaluated it, by using the tooling provided by
`clojure.tools.namespace`. This builds on top of the [CIDER nREPL
middleware][cider-middleware-setup], so to make use of this feature, you'll need
to set it up so that your nREPL includes that middleware.

Once you've got that squared away, you can easily reload everything by pressing
`<localleader>rr`. You can even configure Conjure to run hooks before and after
refresh, which can be handy when you're developing something that you might want
to restart every time you make changes, like a web server.

# Casting spells

One day, in the `#conjure` channel on [Clojurians slack][clj-slack], an
off-the-cuff discussion about re-evaluating the same form over and over again
during development led to an intriguing new Conjure feature called "eval at
mark."

This feature is also affectionately known as the "spellbook" feature because it
lets you evaluate any number of predefined Clojure forms, on demand, just by
pressing a few keys. I've been using this feature a lot since it was introduced,
and I love it!

In a typical workflow, I might set the mark `F` at a function call in a scratch
namespace, and then I can call that function from anywhere, e.g. while I'm
editing code in another namespace, by pressing `<localleader>emF`.

This sort of workflow helps a lot in common scenarios where I'm testing the
behavior of a function that I'm writing, and that function itself calls a number
of other functions that are defined in other namespaces. Sometimes, in the heat
of development, I end up jumping around through a bunch of different files as
I'm chasing a bug or implementing a complex feature.

Previously, I had to jump back into my scratch namespace everytime I wanted to
re-evaluate a form that calls the function I'm testing. Now, I can stay where I
am in the implementation code and just press:

* `<localleader>rr` to reload all of the code that changed, then

* `<localleader>emF` to re-eval the form at mark `F`

To tie it all together, here's a little demo of Conjure's refresh and "eval at
mark" features:

<center>
<img src="{{ site.url }}/assets/2020-09-21-conjure-eval-at-mark.gif"
     title="Using Conjure's 'eval at mark' feature"
     width="75%">
</center>

# Try it!

If you're a Vim-using Clojurist or a Clojure-using Vimmer, hopefully I've
inspired you to give [Conjure][conjure] a try. Go ahead, it's fun!

# Comments?

Reply to [this tweet][tweet] with any comments, questions, etc.!

[tweet]: https://twitter.com/dave_yarwood/status/1313096438822428674

[fireplace]: https://github.com/tpope/vim-fireplace
[acid]: https://github.com/clojure-vim/acid.nvim
[iced]: https://github.com/liquidz/vim-iced
[conjure]: https://github.com/Olical/conjure
[neovim]: https://neovim.io/
[vim-plug]: https://github.com/junegunn/vim-plug
[nrepl]: https://nrepl.org
[nrepl-alias]: https://nrepl.org/nrepl/0.8/usage/server.html
[dave-deps-edn]: https://github.com/daveyarwood/dotfiles/blob/90d989e0a0cf23f6c4e7bcb4258d315b2e40f14a/clojure/deps.edn#L8-L10
[repl]: https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop
[lein]: https://leiningen.org/
[boot]: https://github.com/boot-clj/boot
[clj]: https://clojure.org/guides/getting_started
[clj-cli]: https://clojure.org/guides/deps_and_cli
[clj-http]: https://github.com/dakrone/clj-http
[cider-middleware-setup]: https://docs.cider.mx/cider/basics/middleware_setup.html
[clj-slack]: http://clojurians.net/

