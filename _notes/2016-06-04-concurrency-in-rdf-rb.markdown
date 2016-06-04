---
layout: note
title:  "Concurrency in RDF.rb"
date: 2016-06-04 15:53:55 -0700
Excerpt: 
keywords: ruby, rdf, data structures, concurrency

---

RDF.rb 2.0 was released this spring with support for [transaction scopes](http://rdf.greggkellogg.net/yard/RDF/Transaction.html). 
To make good on the promise of this interface, 2.0.0 also ships with a [fully serializable](https://en.wikipedia.org/wiki/Serializability) main memory quadstore. <label for="sidenote-1" class='sidenote-number'></label><input type="checkbox" id="sidenote-toggle-1" class="margin-toggle"><span id='sidenote-1' class='sidenote numbered' markdown='1'>We achieve this through the use of persistent [Hash Array Mapped Tries](http://lampwww.epfl.ch/papers/idealhashtrees.pdf), implemented in pure Ruby by the excellent [Hamster](https://github.com/hamstergem/hamster) gem.</span>

While our new implementation theoretically improves the concurrency story for RDF.rb, it isn't thread safe. The underlying data representation may be purely functional, but the Repository itself is swimming in _shared mutable state_. Specifically, we have the potential for a _data race_ during execution of code like `@data = data`; and, more generally, for _race conditions_ wherever our changes depend on previous reads. Notably, this affects [`#transaction`](https://github.com/ruby-rdf/rdf/blob/474a3c9bd84bb6ad8828d1a83ddfad9c2da447f7/lib/rdf/repository.rb#L571-L575), as demonstrated in the following snippet:<label for="sidenote-2" class='sidenote-number'></label><input type="checkbox" id="sidenote-toggle-2" class="margin-toggle"><span id='sidenote-3' class='sidenote numbered' markdown='1'>Running this in your environment is may yield different results. You may even see expected results. Nevertheless, trust me, this code is not safe.</span>

{% highlight ruby %}
require 'rdf'
repo = RDF::Repository.new

threads = []
err_count = 0

# make 10 threads, processing 1000 transactions each
10.times do |n|
  threads << Thread.new do
    1_000.times do |i|
      begin
        repo.transaction(mutable: true) do
          # insert a unique statement for each transaction
          insert RDF::Statement("thread_#{n}".to_sym,
                                RDF::URI('http://example.com/num'),
                                i)
        end
      rescue RDF::Transaction::TransactionError
        # count up the statements that fail in execution
        err_count += 1
      end
    end
  end
end

threads.each(&:join)

# not even close to 10_000!
repo.count + err_count # => 5587
{% endhighlight %}

The good news is that races are reasonably isolated. Because we have persistence at the data structure level, the only shared object we mutate is the `@data` variable itself, changing which object it points to. Any dreams of perfectly asynchronous concurrency are dashed by these reassignments, but to get thread safety we only need to synchronize the assignments themselves. For transactions, this means `#execute`; in place of the `repo.transaction` block above, we have:

{% highlight ruby %}
# ...
begin
  tx = repo.transaction(mutable: true)
  tx.insert RDF::Statement("thread_#{n}".to_sym,
                           RDF::URI('http://example.com/num'),
                           i)
  mutex.synchronize { tx.execute }
rescue RDF::Transaction::TransactionError
# ...
{% endhighlight %}

As an implementation-specific solution, this leaves something to be desired. A better approach is to build locks directly into the transaction (a [proof of concept branch](https://github.com/ruby-rdf/rdf/commit/c8080e28eda934353293e2114d0ce6ddc4e9a3ae) for this already exists). However, it's not immediately clear that the trade-offs here are good. The synchronization overhead is a factor, even for single-threaded transactions. For multi-threaded cases, we'll deliver on our promise of serializable transactions, but it's not obvious that the concurrency will any performance boost. It's equally likely that we'll have more failed transactions. More work---and especially rigorous benchmarking---is needed.

In the end, I suspect that giving more thought to thread safety needs in general will uncover better options.
