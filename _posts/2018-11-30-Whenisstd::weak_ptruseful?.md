---
layout: post
title:  "Whenisstd::weak_ptruseful?"
date:   2018-11-30
tags:
      - 随笔
---

#Whenisstd::weak_ptruseful?


use_countrefer: [When is std::weak_ptr
useful?](https://stackoverflow.com/questions/12030650/when-is-stdweak-ptr-useful)

weak_ptr可以判断游荡的指针是否可用。

std::weak_ptr is a very good way to solve the [dangling
pointer](https://en.wikipedia.org/wiki/Dangling_pointer) problem. By
just using raw pointers it is impossible to know if the referenced data
has been deallocated or not. Instead, by letting a std::shared_ptr
manage the data, and supplying std::weak_ptr to users of the data, the
users can check validity of the data by calling expired() or lock().

应用场景：

-   cache objects. 可以用weak ptr
    保存这些objects，看看他们谁还在用，谁已经无效了。当然这种情况用shared_ptr的use_count判断为1也可以。The
    cache use case could work with a shared reference if the cache was
    able to test if the refcount is 1, thus knowing it has the only
    reference and it could release it reclaiming on demand. This would
    eliminate the need for weak references in this case.

-   交叉引用问题。

Suppose you have Team and Member objects.

Obviously it\'s a relationship : the Team object will have pointers to
its Members. And it\'s likely that the members will also have a back
pointer to their Team object.

Then you have a dependency cycle. If you use shared_ptr, objects will no
longer be automatically freed when you abandon reference on them,
because they reference each other in a cyclic way. This is a memory
leak.

You break this by using weak_ptr. The \"owner\" typically use shared_ptr
and the \"owned\" use a weak_ptr to its parent, and convert it
temporarily to shared_ptr when it needs access to its parent.

Store a weak ptr :

weak_ptr\<Parent> parentWeakPtr\_ = parentSharedPtr; // automatic
conversion to weak from shared

then use it when needed

shared_ptr\<Parent> tempParentSharedPtr = parentWeakPtr\_.lock(); // on
the stack, from the weak ptr

if( not tempParentSharedPtr ) {  // yes it may failed if parent was
freed since we stored weak_ptr

} else {  // do stuff

}// tempParentSharedPtr is released when it goes out of scope

-   管理进程std::shared_ptr\<Task>给subtask周期分配任务，std::vector\<std::weak_ptr\<Task>\>。用timer查看std::weak_ptr\<Task>是否还存在。Suppose
    you have a collection of tasks, executed asynchronously, and managed
    by an std::shared_ptr\<Task>. You may want to do something with
    those tasks periodically, so a timer event may traverse a
    std::vector\<std::weak_ptr\<Task>\> and give the tasks something to
    do. However, simultaneously a task may have concurrently decided
    that it is no longer needed and die. The timer can thus check
    whether the task is still alive by making a shared pointer from the
    weak pointer and using that shared pointer, provided it isn\'t null.



