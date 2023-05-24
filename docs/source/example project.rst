Example Project
===============

.. _example-project:

Overview
--------

In this example project, we'll go over the basics such as making a speed controller move and more, 
eventually progressing until we have a fully functional robot. This is an example project based on
Team 8840's 2023 robot, **Brad**.

.. image:: images/brad.jpg
   :alt: VSCode Extensions
   :align: center

*Say hello to Brad! Image from Silicon Valley Regional, Practice Match.*

Brad has a few things on him - a (swerve) drive base, a double jointed arm, and a roller claw.
We'll start our way from the easiest, the roller claw, and work our way up (or should I say down) to the drive base.

Prerequisites
-------------
- A basic understanding of Java
- A basic understanding of FRC

If you have not installed `8840-utils` yet, go through :ref:`this guide<installation>`.

Getting Setup
-------------
In VSCode, open up your :code:`Main.java` file. You should see something like this:

.. code-block:: java
    :linenos:

     package frc.robot;

     import edu.wpi.first.wpilibj.RobotBase;

     public final class Main {
         private Main() {}

         public static void main(String... args) {
             RobotBase.startRobot(Robot::new);
         }
     }

This is the entry point of your robot code. The :code:`main` method is called when the robot is turned on.

Before, we let it start working with 8840-utils, we need an *event listener.* 
An event listener is a class that will "listen" to events, such as when the robot is turned on, 
when the robot is disabled, when the robot is enabled, etc.

In 8840-utils, we have a class called :code:`EventListener` that does just that. Delete the `Robot.java` file, then create a new file called `Robot.java`.

It should look like this to start off:

.. code-block:: java
    :linenos:

     package frc.robot;

     public class Robot {
        
     }

First, we need to make it extend `EventListener`. To do that, we need to add `extends EventListener` to the class declaration.

.. code-block:: java
    :linenos:

     package frc.robot;

     import frc.team_8840_lib.listeners.EventListener;

     public class Robot extends EventListener {
        
     }

.. tip::
    We recommend using autofill in VSCode to import classes. As you type the name of the class, it will show up in a dropdown. 
    Press enter to autofill the import statement, and use the arrow keys to select the class you want to import.

A bunch of errors should pop up now... and that's ok! That's built into 8840-utils in order to make sure that you don't forget to implement any methods.
We'll fix this now. In VSCode, hover over the :code:`Robot` for a second until a menu underneath pops up. 
Move your mouse on top of that menu and scroll down until you see :code:`Quick fix...`. Click on that.

.. image:: images/unimpl.png
    :alt: unimplemented methods fix
    :align: center

A bunch of methods should pop up. These are the methods that we need to implement. Save the class.
We recommend going through each one and deleting the :code:`throw` that's in each one, so you don't accidentally have an error later on.

Now, we need this linked to the robot. Go back to :code:`Main.java`.

We need to replace 

.. code-block:: java

    RobotBase.startRobot(Robot::new);

with

.. code-block:: java

    RobotBase.startRobot(frc.team_8840_lib.listeners.Robot::new);

instead! This will make sure that the robot is linked to 8840-utils.

We now need to let 8840-utils know that we're using the :code:`Robot` class we made before. Before the :code:`startRobot`, add this line

.. code-block:: java

    frc.team_8840_lib.listeners.Robot.assignListener(new Robot());

This will let 8840-utils know that we're using the :code:`Robot` class we made before.

We'll go back to the :code:`Robot` class now. We'll start by confirming everything works.

In the :code:`robotInit` method, we'll use the :code:`Logger` class to print out a message to the console.

Now, why use this instead of :code:`System.out.println`? Well, the :code:`Logger` class is a lot more powerful than :code:`System.out.println`.
Insted of just printing to the console, it adds a number of timestamps to make sure you know *exactly* when methods are called.
The message is also sent to a :code:`LogWriter`... but we'll get to that later, but it'll allow us to save everything to a file, then replay or analyze it later.

.. code-block:: java
    :linenos:

     // ...some code before
     @Override
     public void robotInit() {
         Logger.Log("Hello world!");
     }
     // some code after...

To test it, open up the WPILib menu from the top right and search for "simulate."

.. image:: images/wpilib-simul.png
    :alt: WPILib menu
    :align: center

Click on the "Simulate Robot Code" button. Click OK on the popup that shows up, then let the program run.

.. image:: images/first\ test.png
    :alt: first test
    :align: center

You should see something like this in the console. If you do, then everything is working!

While we're here, let's also go over some of the other information:

First off, you'll notice that at the first message after :code:`Robot program starting`, it says that "...NT3 port 1735, NT4 port 5810".

NT stands for NetworkTables. NetworkTables is a way for the robot to communicate with the driver station. 
It's made by WPILib, and is used by programs such as Shuffleboard and SmartDashboard. 
It's a great way for the robot to send data to the driver station, and we'll be using this later on!

Now, below that, you might notice a line that states that the "Server is listening on port 5805." This is part of :code:`8840-utils`!

If we really want to confirm this working, open your web browser and go to :code:`http://localhost:5805/`.

You should see a message like so:

.. image:: images/8840server.png
    :alt: 8840-utils server page
    :align: center

Interesting, right? This is primarly to communicate with :code:`8840-app`, our web app that allows you to do much more than just Shuffleboard, but we'll get to that later (again).

.. note::
    This will change soon with the release of v2023.3.0, with the introduction of the :code:`LibraryManager`. We'll update this when it does.

A few more messages can be found, they're probably missing information though since you're not at a competition. 
You should notice a message that says "Hello world!" though - that's the message we sent from the :code:`robotInit` method!

Now, let's move on to the next part - the roller claw!