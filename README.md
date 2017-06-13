# AndroidInterview
All-in-one resource to prepare for Android Interviews

## ACTIVITY

### Describe a scenario, though, where onPause() and onStop() would not be invoked.
This might occur, for example, if you detect an error during onCreate() and call finish() as a result.

### What are “launch modes”? What are the two mechanisms by which they can be defined? What specific types of launch modes are supported?
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


### Lifecycle when going from Activity A to B.
A(onPause) > B(onCreate) > B(onStart) > B(onResume) > A(onStop)

### On return from B to A.
B(onPause) > A(onRestart) > A(onStart) > A(onResume) > B(onStop) > B(onDestroy)
