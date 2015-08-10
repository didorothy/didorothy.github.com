Title: C# Semaphore
Date: 2015-08-10
Category: Programming
Tags: .NET, C#

While working on building a resource pool I ran across a very interesting tool:
`System.Threading.Semaphore`. Initially I thought that this would be used to
handle locking but actually that is done with a `lock() {}` block. Semaphore
instead provides a way to count how many resources are in use and to set an
upper limit.

For my purposes, building a resource pool, Semaphore provided a convenient way
to limit the total number of resources that were available for use. Simply
create a Semaphore and tell it how many resources you want to make available.
Something like the following provides the basics:

    :::csharp
    System.Threading.Semaphore sem = new System.Threading.Semaphore(5, 5);

    // reserve a resource
    sem.WaitOne();

    // release a resource.
    sem.Release();

The above shows the basics. Create the Semaphore; when you need a resource call
`WaitOne()` before using/requesting the resource, and when you are finished
call `Release()` after finished/returning the resource. The arguments passed
when creating a Semaphore tell the starting amount available and the limit. I
did not see a specific need for making these numbers different but there is
probably a specific use case where this is helpful.

A more complete (though not necessaryly functional) example follows:

    :::csharp
    class Pool {
        private System.Threading.Semaphore sem = new System.Threading.Semaphore(5, 5);
        private System.Collections.Queue resources = new System.Collections.Queue();

        public object get_resource() {
            sem.WaitOne();
            lock(resources) {
                // ... handle if no resources yet in our resources Queue
                return resources.Dequeue();
            }
        }

        public void release_resource(object res) {
            lock(resources) {
                resources.Enqueue(res);
            }
            sem.Release();
        }
    }

Perhaps this will be helpful to someone.
Either way it will be a good reference for me.
