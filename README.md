# detached_task

A simple way to spin off async tasks without having a std::future to wait for.
Practical for fire and forget tasks that don't return a result.

The detached_task::execute funtion is a work around to allow fire and forget tasks
without spawning a new thread every time. Instead it relies on the internal thread
pool of std::async to perform the task.

**Example:**

```cpp
   detached_task::execute( [](){std::cout<<"hello world\n";} );
 ```

**NOTE:**
When leaving `main` active tasks will be waited for. But it is important that you
don't create new tasks once `main` is left. This sound pretty obvious but is a
likely pitfall once you start creating detached_task within your detached_task.

**Bad example:**
```cpp
int main()
{
  detached_task::execute(
    // BAD: new detached_task could be created after the main-thread invoked exit.
      detached_task::execute(
       [](){std::cout<<"hello world\n";}
      )
    );

  return 0;
}
 ```
