# AndroidInterview
All-in-one resource to prepare for Android Interviews



## ACTIVITY

### Q. Describe a scenario where onPause() and onStop() would not be invoked.
This might occur, for example, if you detect an error during onCreate() and call finish() as a result.

### Q. What are “launch modes”? What are the two mechanisms by which they can be defined? What specific types of launch modes are supported?
A “launch mode” is the way in which a new instance of an activity is to be associated with the current task.
Launch modes may be defined using one of two mechanisms:

#### Manifest file:
When declaring an activity in a manifest file, you can specify how the activity should associate with tasks when it starts. Supported values include:

**standard (default):** Multiple instances of the activity class can be instantiated and multiple instances can be added to the same task or different tasks. This is the common mode for most of the activities.

**singleTop:** The difference from standard is, if an instance of the activity already exists at the top of the current task and the system routes the intent to this activity, no new instance will be created because it will fire off an onNewIntent() method instead of creating a new object.

**singleTask:** A new task will always be created and a new instance will be pushed to the task as the root. However, if any activity instance exists in any tasks, the system routes the intent to that activity instance through the onNewIntent() method call. In this mode, activity instances can be pushed to the same task. This mode is useful for activities that act as the entry points.

**singleInstance:** Same as singleTask, except that the no activities instance can be pushed into the same task of the singleInstance’s. Accordingly, the activity with launch mode is always in a single activity instance task. This is a very specialized mode and should only be used in applications that are implemented entirely as one activity.

#### Intent flags:
Calls to startActivity() can include a flag in the Intent that declares if and how the new activity should be associated with the current task. Supported values include:

**FLAG_ACTIVITY_NEW_TASK:** Same as singleTask value in Manifest file (see above).

**FLAG_ACTIVITY_SINGLE_TOP:** Same as singleTop value in Manifest file (see above).

**FLAG_ACTIVITY_CLEAR_TOP:** If the activity being started is already running in the current task, then instead of launching a new instance of that activity, all of the other activities on top of it are destroyed and this intent is delivered to the resumed instance of the activity (now on top), through onNewIntent(). There is no corresponding value in the Manifest file that produces this behavior. [[More](https://developer.android.com/guide/components/activities/tasks-and-back-stack.html#TaskLaunchModes)]


### Q. Lifecycle when going from Activity A to B.
A(onPause) > B(onCreate) > B(onStart) > B(onResume) > A(onStop)

### Q. On return from B to A.
B(onPause) > A(onRestart) > A(onStart) > A(onResume) > B(onStop) > B(onDestroy)

## ACTIVITY/INTENT

### Q. How do you start implicit intent? How would you avoid potential crash?
An implicit intent specifies an action that can invoke any app on the device able to perform the action. Using an implicit intent is useful when your app cannot perform the action, but other apps probably can. If there is more than one application registered that can handle this request, the user will be prompted to select which one to use.

However, it is possible that there are no applications that can handle your intent. In this case, your application will crash when you invoke startActivity(). To avoid this, before calling startActivity() you should first verify that there is at least one application registered in the system that can handle the intent. To do this use resolveActivity() on your intent object:
  ``` java
  Intent sendIntent = new Intent();
    sendIntent.setAction(Intent.ACTION_SEND);
    sendIntent.putExtra(Intent.EXTRA_TEXT, textMessage);
    sendIntent.setType(HTTP.PLAIN_TEXT_TYPE); // "text/plain" MIME type
 if (sendIntent.resolveActivity(getPackageManager()) != null) {
        startActivity(sendIntent);
    } 
 ```

## ACTIVITY/FRAGMENT

### Q. What is the difference between a fragment and an activity? Explain the relationship between the two.
An activity is typically a single, focused operation that a user can perform (such as dial a number, take a picture, send an email, view a map, etc.). Yet at the same time, there is nothing that precludes a developer from creating an activity that is arbitrarily complex.

Activity implementations can optionally make use of the Fragment class for purposes such as producing more modular code, building more sophisticated user interfaces for larger screens, helping scale applications between small and large screens, and so on. Multiple fragments can be combined within a single activity and, conversely, the same fragment can often be reused across multiple activities. This structure is largely intended to foster code reuse and facilitate economies of scale.

A fragment is essentially a modular section of an activity, with its own lifecycle and input events, and which can be added or removed at will. It is important to remember, though, that a fragment’s lifecycle is directly affected by its host activity’s lifecycle; i.e., when the activity is paused, so are all fragments in it, and when the activity is destroyed, so are all of its fragments.

## FRAGMENTS

### Q. How can two fragments communicate?
[See Example](https://developer.android.com/training/basics/fragments/communicating.html)

### Q. Does a fragment need a parameterless constructor? Why?
When a configuration change occurs (e.g., orientation change), by default Android destroys and recreates your activity, and also destroys and recreates the fragments in that activity. The "recreates the fragments" part is why you need the zero-argument public constructor on your fragments. It is also used in other cases, such as with a FragmentStatePagerAdapter.

## VIEWS

### Q. View vs SurfaceView.
The main difference is that SurfaceView can be drawn on by background theads but Views can't. SurfaceViews use more resources though so you don't want to use them unless you have to.

### Q. ListView vs RecyclerView
Reuses cells while scrolling up/down - this is possible with implementing View Holder in the listView adapter, but it was an optional thing, while in the RecycleView it's the default way of writing adapter.
Decouples list from its container - so you can put list items easily at run time in the different containers (linearLayout, gridLayout) with setting LayoutManager.
Example:
```java 
mRecyclerView.setLayoutManager(new LinearLayoutManager(this));
//or
mRecyclerView.setLayoutManager(new GridLayoutManager(this, 2));
```
Animates common list actions - Animations are decoupled and delegated to ItemAnimator.

## BROADCAST RECEIVER

### Q. What is a BroadcastReceiver?
A broadcast receiver is a component that enables the system to deliver events to the app outside of a regular user flow, allowing the app to respond to system-wide broadcast announcements. Because broadcast receivers are another well-defined entry into the app, the system can deliver broadcasts even to apps that aren't currently running

### Q. What are the different types of Broadcasts?
You can compare a sticky broadcast with a sticky note. Someone posts it and you can read when you pass by/your application starts - regardless of when it was posted.
An ordered broadcast is like passing a note - it passes from person/application to person/application. Anywhere in the chain the recipient can elect to cancel the broadcast preventing the rest of the chain from seeing it.
A normal broadcast.. well, just sends to everyone that's allowed & registered to listen to it.

## CONTENT PROVIDER

### Q. What is a ContentProvider and what is it typically used for?
A ContentProvider manages access to a structured set of data. It encapsulates the data and provide mechanisms for defining data security. ContentProvider is the standard interface that connects data in one process with code running in another process. [[More](https://developer.android.com/guide/topics/providers/content-providers.html)]

## LOADERS

### Q. What is a Loader?
Lets you load data from a content provider or other data source for display in an Activity or Fragment.
**Problem without loader:**
*lack of responsiveness due to performing potentially slow queries from the UI thread(Activity/Fragment)
*If using AsyncTask, you need to manage lifecycle events of both background and UI thread.
**With Loader:**
*run on separate threads to prevent unresponsive UI.
*simplify thread management by providing callback methods when events occur.
*persist and cache results across configuration changes to prevent duplicate queries.
*can implement an observer to monitor for changes in the underlying data source.

## SERVICE

### Q. Suppose that you are starting a service in an Activity as follows:
```Intent service = new Intent(context, MyService.class);             
startService(service);
```
### where MyService accesses a remote server via an Internet connection. If the Activity is showing an animation that indicates some kind of progress, what issue might you encounter and how could you address it?
Service is started on the main application thread (or UI thread) in the Activity. Thus UI thread will be blocked and screen would appear forzen.
The problem can be avoided by relegating any such remote requests to a background thread or, when feasible, using an an asynchronous response mechanism.

### Q. What is the difference between Service and IntentService? How is each used?
Service is the base class for Android services that can be extended to create any service. A class that directly extends Service runs on the main thread so it will block the UI (if there is one) and should therefore either be used only for short tasks or should make use of other threads for longer tasks.

IntentService is a subclass of Service that handles asynchronous requests (expressed as “Intents”) on demand. Clients send requests through startService(Intent) calls. The service is started as needed, handles each Intent in turn using a worker thread, and stops itself when it runs out of work. Writing an IntentService can be quite simple; just extend the IntentService class and override the onHandleIntent(Intent intent) method where you can manage all incoming requests.

### Q. What is a Service in Android? How many types of Services do you know?
A Service is an application component that can perform long-running operations in the background, and it does not provide a UI. Another application component can start a service, and it continues to run in the background even if the user switches to another application. Additionally, a component can bind to a service to interact with it and even perform interprocess communication (IPC).

**STARTED:** A service is started when an application component (such as an activity) calls startService(). After it's started, a service can run in the background indefinitely, even if the component that started it is destroyed. It does not return a result to the caller.

**BOUND:** A service is bound when an application component binds to it by calling bindService(). A bound service offers a client-server interface that allows components to interact with the service, send requests, receive results, and even do IPC. A bound service runs as long as another application component is bound to it. Multiple components can bind to the service at once, but when all of them unbind, the service is destroyed.

**SCHEDULED (API 21):** You can use the JobScheduler by registering jobs and specifying their requirements for network and timing. The system then gracefully schedules the jobs

### Q. Which service is used to perform IPC, when bind application to a service?
Bound service. bind it onStart().

### Q. How to run the service even after killing the application in android ? 
Return "Service.START_STICKY" from onStartCommand().

## ASYNC TASK

### Q. What is the relationship between the life cycle of an AsyncTask and an Activity? What problems can this result in? How can these problems be avoided?
An AsyncTask is not tied to the life cycle of the Activity that contains it. So, for example, if you start an AsyncTask inside an Activity and the user rotates the device, the Activity will be destroyed (and a new Activity instance will be created) but the AsyncTask will not die but instead goes on living until it completes.

Then, when the AsyncTask does complete, rather than updating the UI of the new Activity, it updates the former instance of the Activity (i.e., the one in which it was created but that is not displayed anymore!). This can lead to an Exception (of the type java.lang.IllegalArgumentException: View not attached to window manager if you use, for instance, findViewById to retrieve a view inside the Activity).

There’s also the potential for this to result in a memory leak since the AsyncTask maintains a reference to the Activty, which prevents the Activity from being garbage collected as long as the AsyncTask remains alive.

For these reasons, using AsyncTasks for long-running background tasks is generally a bad idea . Rather, for long-running background tasks, a different mechanism (such as a service) should be employed.

## JAVA

### Q.What is difference between Serializable and Parcelable ? Which is best approach in Android ?
Serializable is a standard Java interface. You simply mark a class Serializable by implementing the interface, and Java will automatically serialize it in certain situations.

Parcelable is an Android specific interface where you implement the serialization yourself. It was created to be far more efficient than Serializable, and to get around some problems with the default Java serialization scheme(creates too many temporary object which has to be garbage collected).

### Q. Can an Interface extend another Interface?
A Java class can only extend one parent class. Multiple inheritance is not allowed. Interfaces are not classes, however, and an interface can extend more than one parent interface. The extends keyword is used once, and the parent interfaces are declared in a comma-separated list.

### Q. What are the access modifiers you know? What does each one do?
**private** access modifier is accessible only within class.
**default** modifier is accessible only within package.
**protected** access modifier is accessible within package and outside the package but through inheritance only.
**public** access modifier is accessible everywhere

### Q. What is the difference between overriding and overloading a method in Java?
| Overloading   | Overriding    |
|:-------------:|:-------------:|
| compile-time | run-time |
| done in same class | requires child class |
| works with private, final n static | doesn't work with private, final n static |
| return type doesn't matter | return type should be same as parent/more specific |
| argument list different | argument list same |

### Q. What’s the difference between an interface and an abstract class?
Abstract classes can have constants, members, method stubs (methods without a body) and defined methods, whereas interfaces can only have constants and methods stubs.

Methods and members of an abstract class can be defined with any visibility, whereas all methods of an interface must be defined as public (they are defined public by default).

When inheriting an abstract class, a concrete child class must define the abstract methods, whereas an abstract class can extend another abstract class and abstract methods from the parent class don't have to be defined.
Similarly, an interface extending another interface is not responsible for implementing methods from the parent interface. This is because interfaces cannot define any implementation.

A child class can only extend a single class (abstract or concrete), whereas an interface can extend or a class can implement multiple other interfaces.

A child class can define abstract methods with the same or less restrictive visibility, whereas a class implementing an interface must define the methods with the exact same visibility (public).

### Q. Can a static method be overridden in Java?
Static methods cannot be overridden because they are not dispatched on the object instance at runtime. The compiler decides which method gets called.

### Q. What is Polymorphism? What about Inheritance?
Inheritance refers to using the structure and behaviour of a super class in a subclass.
Polymorphism refers to changing the behaviour of a super class in the subclass.

### Q. Can a constructor be inherited?
No,Only members are inherited, and a constructor is not considered a member.

### Q. What’s the difference between using == and .equals on a string?
In Java, == always just compares two references (for non-primitives, that is) - i.e. it tests whether the two operands refer to the same object.
However, the equals method can be overridden - so two distinct objects can still be equal.
```java
String x = "hello";
String y = "he" + "llo";
System.out.println(x == y); // true!
```

### Q. What is the hashCode() and equals() used for?
Default implementation of hashCode() provided by Object is derived by mapping the memory address to an integer value.
you say that two objects are equivalent they satisfy the “equals()” condition.

### Q. when to use finally block?
If you return from inside try block, but want to do something in all the cases, use finally block. if the catch block throws an exception, code after that will not be executed but finally will be executed. Try block throws unhandled exception.

### Q. What is the finalize() method?
finalize() is called by the garbage collector on an object when garbage collection determines that there are no more references to the object. Override this method to perform other cleanup.

### Q. What is the difference between instantiation and initialization of an object?
Initialization means assigning initial value to variables after declaring or while declaring. All variables are always given an initial value at the point the variable is declared. Thus, all variables are initialized.
Instantiation means defining or creating new object for class to access all properties like methods, fields, etc. from class.

### Q. Why Use Generics?
The following code snippet without generics requires casting:
```java
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0);
```
When re-written to use generics, the code does not require casting:
```
List<String> list = new ArrayList<String>();
list.add("hello");
String s = list.get(0);   // no cast
```

### Q. StringBuffer vs StringBuilder.
String is an immutable.
StringBuffer is a mutable and synchronized.
StringBuilder is also mutable but its not synchronized.
using synchronized methods in a single thread is overkill.

### Q. Enumerator vs Iterarot.
Using Enumeration, you can only traverse the Collection object. But using Iterator, you can also remove an element while traversing the Collection.Iterator is a fail-fast in nature. i.e it throws ConcurrentModificationException if a collection is modified while iterating other than it’s own remove() method. Where as Enumeration is fail-safe in nature. It doesn’t throw any exceptions if a collection is modified while iterating.

### Q. FailSafe vs FailFast
The only difference is fail-safe iterator doesn't throw any Exception, contrary to fail-fast Iterator, if Collection is modified structurally while one thread is iterating over it. This is because they work on clone of Collection instead of original collection and that's why they are called as fail-safe iterator.

### Q. What is Daemon thread?
A daemon thread is a thread that does not prevent the JVM from exiting when the program finishes but the thread is still running. An example for a daemon thread is the garbage collection.

### Q. What is java heap?
The heap is the runtime data area from which memory for all class instances and arrays is allocated. The heap is created on virtual machine start-up. Heap storage for objects is reclaimed by an automatic storage management system (known as a garbage collector); objects are never explicitly deallocated.

### Q. Can a dead thread be restarted?
There is no way to bring back the dead thread to runnable state,instead you should create a new Thread instance. It is never legal to start a thread more than once. In particular, a thread may not be restarted once it has completed execution.

## MISC

### Q. Thread vs Service vs AsynTask
Service is like an Activity but has no interface. Probably if you want to fetch the weather for example you won't create a blank activity for it, for this you will use a Service.
A Thread is a Thread, probably you already know it from other part. You need to know that you cannot update UI from a Thread. You need to use a Handler for this, but read further.
An AsyncTask is an intelligent Thread that is advised to be used. Intelligent as it can help with it's methods, and there are three methods that run on UI thread(onPreExecute,onPostExecute,onProgressUpdate), which is good to update UI components.

### Q. Describe Unit tests.
Unit Tests include sets of one or more programs which are designed to verify an atomic unit of source code, such as a method or a class. Tools is JUnit.

### Q. Describe UI Testing.
UI tests ensure that your application return the correct UI output in response to sequence of user actions on device. Espresso test recorder.

### Q. Describe Integration tests.
All unit tested modules, are combined and verified.integration tests often involve checking integration withAndroid components such as Service testing, Activity testing, Content Provider testing, etc

### Q. Describe Operational/Functional tests.
They are high level tests designed to check the completeness and correctness of application.

### Q. Describe System tests.
The system is tested as a whole and the interaction between the components, software and hardware is checked.

### Q. What is ORM?
Object/relational mapping. technique for converting data between incompatible type systems. maps Java objects to database tables

### Q. What is the StrictMode?
Developer tool which detects things you might be doing by accident and brings them to your attention.
catches accidental disk or network access on the application's main thread.

### Q. What is PendingIntent?
A PendingIntent is a token that you give to a foreign application (e.g. NotificationManager, AlarmManager, Home Screen AppWidgetManager, or other 3rd party applications), which allows the foreign application to use your application's permissions to execute a predefined piece of code.

### Q. How to start activity with application context?
Include the flag FLAG_ACTIVITY_NEW_TASK
```java
  Intent startActivity = new Intent();  
  startActivity.setClass(context, xxx.class); 
  startActivity.setAction(xxx.class.getName()); 
  startActivity.setFlags( 
              Intent.FLAG_ACTIVITY_NEW_TASK 
              | Intent.FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS); 
  context.startActivity(startActivity); 
```

### Q. Which Android Developer tools are there? 
Memory leaks: HPROF Viewer and Analyzer, Allocation Tracker 
ANR: Method trace
System Information
GPU: GPU debugger

### Q. What are the key components in Android architecture?
Activities, Services, Broadcast receivers and Content Providers.

### Q. ways data stored in Android?
SharedPreference, Internal Storage, External Storage, SQLite DB, Network communication.

### Q. What are some of security best practices?
Don't use raw SQL query, use https, dnt store keys locally, dont log sensitive data, dnt store sensitive data in webview cache, disable backup as it is modifiable, obfuscate your code to prevent reverse engineer.

### Q. Explain Android platform Architecture.
![Platform Architecture](https://developer.android.com/guide/platform/images/android-stack_2x.png)
**Linux Kernel:** Does threading and low-level memory management.

**Hardware Abstraction Layer (HAL):**  When a framework API makes a call to access device hardware(camera,bluetooth etc), the Android system loads the library module for that hardware component.

**Android Runtime:** Does Ahead-of-time (AOT) and just-in-time (JIT) compilation, garbage collection, debugging support(sampling profiler, crash reporting,watch points,monitor fields)

**Native C/C++ Libraries:** Components and services, such as ART and HAL are written in c/c++.

**Java API Framework:** Feature-set of the Android OS is available to you through APIs written in the Java language. eg Resource Manager,Notification Manager, Activity Manager, Content Providers

**System Apps:** Core apps for email, SMS messaging, calendars, internet browsing, contacts, and more.




