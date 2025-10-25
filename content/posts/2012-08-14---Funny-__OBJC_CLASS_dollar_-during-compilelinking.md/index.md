---
title: Funny "__OBJC_CLASS_$_" during compile/linking
date: "2012-08-14T17:28:42"
template: "post"
draft: false
slug: "/posts/funny_objc_class_$_during_compile_linking"
category: "Programming"
tags:
  - objective c
  - xcode
description: "I've been following the CoreData tutorial and I receive errors ... Turns out, CoreData.framework wasn't in my project"
---

I've been following the CoreData tutorial and I receive following errors during linking:

    Undefined symbols for architecture i386:
      "_NSSQLiteStoreType", referenced from:
          -[AppDelegate persistentStoreCoordinator] in AppDelegate.o
      "_OBJC_CLASS_$_NSEntityDescription", referenced from:
          objc-class-ref in AddRoleTVC.o
      "_OBJC_CLASS_$_NSFetchRequest", referenced from:
          objc-class-ref in RolesTVC.o
      "_OBJC_CLASS_$_NSFetchedResultsController", referenced from:
          objc-class-ref in RolesTVC.o
      "_OBJC_CLASS_$_NSManagedObject", referenced from:
          _OBJC_CLASS_$_Role in Role.o
      "_OBJC_CLASS_$_NSManagedObjectContext", referenced from:
          objc-class-ref in AppDelegate.o
      "_OBJC_CLASS_$_NSManagedObjectModel", referenced from:
          objc-class-ref in AppDelegate.o
      "_OBJC_CLASS_$_NSPersistentStoreCoordinator", referenced from:
          objc-class-ref in AppDelegate.o
      "_OBJC_METACLASS_$_NSManagedObject", referenced from:
          _OBJC_METACLASS_$_Role in Role.o

Turns out, `CoreData.framework` wasn't in my project. Adding that to the build phase solve the problem. As simple as that.
